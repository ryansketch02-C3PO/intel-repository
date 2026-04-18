# Scattered Spider — Indicators of Compromise

> **Source:** SEKOIA-IO (GitHub), CISA AA23-320A, AHA/HC3, ReliaQuest, EclecticIQ
> **TLP:** TLP:CLEAR
> **Last Updated:** 2026-04-09

---

## ⚠️ Note on IOC Freshness

Scattered Spider operates **short-lived infrastructure** (domains active < 7 days). The domains and IPs below are historical indicators from confirmed campaigns. They should be used for **retrospective hunting and pattern detection**, not real-time blocking alone.

---

## Phishing / C2 Domains (Historical — 2023–2024)

### Pattern-Based (victimname substitution)
```
victimname-okta[.]com
victimname-servicedesk[.]com
```

### Confirmed Campaign Domains (SEKOIA-IO, Feb 2024)
```
gitlabhr[.]com
cellularhr[.]com
usinfo1[.]net
roblox-hrs[.]com
gitlabsso[.]com
walmartsso[.]com
rbxhr[.]net
bell-hr[.]com
uscell[.]net
grubhubsso[.]com
zen-sso[.]com
fireblocks-sso[.]com
aflac-hr[.]com
sec-sso[.]net
sinchdev[.]com
linkedinsso[.]com
zendesklt[.]com
uscellular-hr[.]com
bn-sso[.]com
applesso[.]com
uscchr[.]com
walmartworkspace[.]com
vz-hr[.]com
on-sinch[.]com
usccplus[.]com
marsh-hr[.]com
costsso[.]com
truecorphr[.]net
connect-sso[.]com
cellularsso[.]com
athene-usa[.]com
```

### MGM / Caesars Campaign (2023)
```
mgm-okta[.]com       (example pattern)
```

---

## C2 / Attacker IPs (Historical — 2024)

```
45.76.65.42
149.248.0.189
45.63.52.43
104.207.153.50
195.35.10.222
144.202.114.128
149.28.66.216
149.248.12.179
2a02:4780:b:1342:0:238d:fa59:3   (IPv6)
```

> ⚠️ These IPs are VPS/cloud-hosted and reassigned frequently. Use as hunting seeds, not hard blocks.

---

## Hosting ASNs (Infrastructure Pattern)

```
AS39287   ABSTRACT, FI
AS13335   Cloudflare, Inc.
AS399486  VIRTUO, CA
AS14061   DigitalOcean (DIGITALOCEAN-ASN)
AS20473   Vultr (AS-CHOOPA)
```

---

## Domain Registrars (Infrastructure Pattern)

```
NiceNIC International Group
Hosting Concepts B.V. (Openprovider)
NameSilo, LLC
GoDaddy.com, LLC
```

---

## Domain Keywords (Hunt Patterns)

Use these keywords in domain monitoring / DRP tools:

```
okta
sso
vpn
helpdesk
servicenow
corp
mfa
support
duo
rsa
internal
connect
servicedesk
schedule
info
help
service
```

**Regex pattern (typosquatting):**
```
[company-name]([-.]?)(okta|sso|vpn|helpdesk|mfa|servicedesk|duo|support)[.]com
```

---

## File Hashes (Malware Used by Scattered Spider)

### Spectre RAT
> Hash values vary; look for unsigned executables masquerading as remote management tools

### BlackCat / ALPHV (used in MGM attack)
```
# See ALPHV IOC repository for hash list
# CISA AA23-061A covers ALPHV/BlackCat hashes
```

### Raccoon Stealer
```
# Widely documented; search MISP/VirusTotal for "raccoon stealer" 
```

---

## Ransomware Ransom Notes

```
README.txt       (BlackCat/ALPHV standard note)
RECOVER-FILES.txt (DragonForce variant)
```

---

## Tools Observed (Living Off the Land)

```
# Legitimate tools abused — not malicious in isolation:
AnyDesk.exe
TeamViewer.exe
ScreenConnect.exe (ConnectWise)
ngrok.exe
Teleport (tsh.exe)
Rclone.exe
Mimikatz.exe / mimikatz_trunk.zip
LaZagne.exe
SharpHound.exe
ADRecon.ps1
PingCastle.exe
RustScan
```

---

## Behavioral IOCs (Detection Logic)

1. **Helpdesk calls requesting MFA reset** from unfamiliar users — especially employees found via LinkedIn
2. **New Okta IdP registered** via inbound federation — especially outside business hours
3. **Cloud VMs created** in environments not matching standard naming conventions
4. **Ngrok/Teleport connections** from endpoints that don't typically run them
5. **MEGA.NZ upload** activity from enterprise workstations
6. **VSS deletion commands** (`vssadmin delete shadows`, `wbadmin delete catalog`)
7. **Rapid MFA approval prompts** (>3 in 1 minute to one user account)
8. **New admin accounts** created with SSO/Okta attribute manipulation
9. **SSH exfiltration to Vultr IPs** (ASN20473) in unusual volumes
10. **Domain typosquats** of company + keywords (okta, sso, vpn) registered in last 7 days

---

## References

- SEKOIA-IO GitHub IOC dataset: https://github.com/SEKOIA-IO/Community/blob/main/IOCs/ScatteredSpider/20240220_ScatteredSpider_IOC.csv
- CISA AA23-320A: https://www.cisa.gov/news-events/cybersecurity-advisories/aa23-320a
- EclecticIQ IOC list: (referenced in AHA/HC3 report)
- ReliaQuest domain analysis: https://reliaquest.com/blog/scattered-spider-cyber-attacks-using-phishing-social-engineering-2025/
