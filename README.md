# AS51396 Prefix Lists  
Up-to-date IPv4 & IPv6 prefix lists for **AS51396 (pfCloud)**.  
Website: https://pfcloud.io

This repository automatically fetches all announced prefixes for AS51396 every 35 minutes and generates clean, shareable text files.  
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
```curl -s https://raw.githubusercontent.com/marekgrebac/as51396-list/main/as51396-full.txt
curl -s https://raw.githubusercontent.com/marekgrebac/as51396-list/main/as51396-ipv4.txt
curl -s https://raw.githubusercontent.com/marekgrebac/as51396-list/main/as51396-ipv6.txt
```
Perfect for:

- firewalls  
- IDS/IPS  
- network monitoring  
- abuse tooling  
- anti-scanning rules  
- automated blacklists / rate-limits  

---

# ✅ Firewall Rules (with automatic daily update)

Below are simple examples for automatically blocking or isolating traffic from AS51396 using the prefix lists in this repository.

---

## 🔁 Daily Auto-Update Script (Linux)

Create:

`/usr/local/bin/update-as51396.sh`

```bash
#!/bin/bash

BASE="/etc/as51396"
mkdir -p "$BASE"

curl -s -o "$BASE/as51396-full.txt" \
 https://raw.githubusercontent.com/marekgrebac/as51396-list/main/as51396-full.txt

curl -s -o "$BASE/as51396-ipv4.txt" \
 https://raw.githubusercontent.com/marekgrebac/as51396-list/main/as51396-ipv4.txt

curl -s -o "$BASE/as51396-ipv6.txt" \
 https://raw.githubusercontent.com/marekgrebac/as51396-list/main/as51396-ipv6.txt
```

Make executable:
```chmod +x /usr/local/bin/update-as51396.sh```

Cron (daily at 03:00):
```0 3 * * * /usr/local/bin/update-as51396.sh >/dev/null 2>&1```
