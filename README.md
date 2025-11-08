# <img src="https://pfcloud.io/assets/img/logo.png" width="50" style="vertical-align: middle;"> AS51396 (pfCloud) Prefix Lists  
Up-to-date IPv4 & IPv6 prefix lists for **AS51396 (pfCloud)**.  
Website: https://pfcloud.io

This repository automatically fetches all announced prefixes for AS51396 every 35 minutes and generates clean, shareable text files **only if the content of the API response has changed!**  
The lists are intended to help network operators, hosting providers, and abuse teams properly identify traffic originating from this ASN.

---

## ✅ Why this Exists

AS51396 is operated by **pfCloud** (https://pfcloud.io).

Multiple networks have reported that **some customers of AS51396 run automated Minecraft server scanners, port scanners, tracking services, or other suspicious/malicious traffic**.

Because of this, many administrators need a reliable, always-fresh list of IP prefixes belonging to AS51396 to:

- improve abuse handling  
- identify scanning traffic quickly  
- apply rate-limits or filtering  
- track which networks are impacted  
- provide clear evidence in abuse reports  

This project exists to make that easier and more transparent.

---

## ✅ What this Repository Contains

### **`as51396-full.txt`**  
All prefixes (IPv4 first, IPv6 second). This is the main combined list.

### **`as51396-ipv4.txt`**  
Only IPv4 prefixes announced by AS51396.

### **`as51396-ipv6.txt`**  
Only IPv6 prefixes announced by AS51396.

---

## ✅ Update Schedule

A server pulls live data from **https://bgp.tools/** every **35 minutes**,  
filters prefixes belonging to ASN 51396 (pfCloud), splits them into IPv4/IPv6 lists,  
and commits changes here automatically.

Only changed files are committed — no spammy commit history.

---

## ✅ Data Source

Data is fetched from:

**https://bgp.tools/table.txt**

The project uses only publicly available BGP information.

---

## ✅ Usage

You can download these lists directly:
```
curl -s https://raw.githubusercontent.com/marekgrebac/as51396-list/main/as51396-full.txt
curl -s https://raw.githubusercontent.com/marekgrebac/as51396-list/main/as51396-ipv4.txt
curl -s https://raw.githubusercontent.com/marekgrebac/as51396-list/main/as51396-ipv6.txt
```

---

# ✅ Firewall Rules (with automatic daily update)

Below are simple examples for automatically blocking or isolating traffic from AS51396 using the prefix lists in this repository.

---

## ✅ iptables — Automatic Daily Refresh

Since iptables rules are static, they must be reapplied each time the prefix list changes.  
This setup refreshes the rules *automatically every day*.

Create:

`/usr/local/bin/update-as51396-fw.sh`

```bash
#!/bin/bash

BASE="/etc/as51396"
mkdir -p "$BASE"

# 1. Download fresh prefix lists
curl -s -o "$BASE/as51396-ipv4.txt" \
 https://raw.githubusercontent.com/marekgrebac/as51396-list/main/as51396-ipv4.txt

curl -s -o "$BASE/as51396-ipv6.txt" \
 https://raw.githubusercontent.com/marekgrebac/as51396-list/main/as51396-ipv6.txt

# 2. Flush old rules
iptables  -D INPUT -j AS51396_BLOCK 2>/dev/null
iptables  -F AS51396_BLOCK 2>/dev/null
iptables  -X AS51396_BLOCK 2>/dev/null

ip6tables -D INPUT -j AS51396_BLOCK6 2>/dev/null
ip6tables -F AS51396_BLOCK6 2>/dev/null
ip6tables -X AS51396_BLOCK6 2>/dev/null

# 3. Create new chains
iptables  -N AS51396_BLOCK
ip6tables -N AS51396_BLOCK6

# 4. Populate IPv4 rules
while read p; do
  iptables -A AS51396_BLOCK -s "$p" -j DROP
done < "$BASE/as51396-ipv4.txt"

# 5. Populate IPv6 rules
while read p; do
  ip6tables -A AS51396_BLOCK6 -s "$p" -j DROP
done < "$BASE/as51396-ipv6.txt"

# 6. Attach chains to INPUT
iptables  -A INPUT -j AS51396_BLOCK
ip6tables -A INPUT -j AS51396_BLOCK6
```
Make executable:
```bash
chmod +x /usr/local/bin/update-as51396-fw.sh
```
Cron — update + firewall refresh every day at 03:00
```bash
0 3 * * * /usr/local/bin/update-as51396-fw.sh >/dev/null 2>&1
```
---
# ✅ UFW
---
Create:
`/usr/local/bin/update-as51396-ufw.sh`

Paste in the content:
```bash
#!/bin/bash

BASE="/etc/as51396"
mkdir -p "$BASE"

# 1. Download fresh lists
curl -s -o "$BASE/as51396-ipv4.txt" \
 https://raw.githubusercontent.com/marekgrebac/as51396-list/main/as51396-ipv4.txt

curl -s -o "$BASE/as51396-ipv6.txt" \
 https://raw.githubusercontent.com/marekgrebac/as51396-list/main/as51396-ipv6.txt

# 2. Remove old UFW rules
ufw --force reset

# 3. Reload defaults
ufw default allow incoming
ufw default allow outgoing

# 4. Apply block rules
while read p; do
  ufw deny from "$p"
done < "$BASE/as51396-ipv4.txt"

while read p; do
  ufw deny from "$p"
done < "$BASE/as51396-ipv6.txt"

# 5. Enable UFW (non-interactive)
yes | ufw enable
```
Make executable:
```bash
chmod +x /usr/local/bin/update-as51396-ufw.sh
```
Cron — update + firewall refresh every day at 03:00
```bash
5 3 * * * /usr/local/bin/update-as51396-ufw.sh >/dev/null 2>&1
```
