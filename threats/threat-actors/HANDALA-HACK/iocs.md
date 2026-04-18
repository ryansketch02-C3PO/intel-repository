# Handala Hack — Indicators of Compromise (IOCs)

> **TLP: WHITE** | Last Updated: 2026-04-10
> **Source Reliability: B2** (Check Point, Inversion6, KELA, ExtraHop)
> ⚠️ Note: Handala TTPs favor short-lived infrastructure (commercial VPNs, cloud storage, dynamic IPs). IOCs below should be used for hunting/historical correlation; do not rely solely on static IOC matching.

---

## Network Indicators

### IP Addresses / CIDRs

| IOC | Type | Description | Confidence |
|---|---|---|---|
| 169.150.227.0/24 | CIDR | Primary commercial VPN egress range used by Handala operators (pre-Jan 2026) | High |
| 64.176.172.0/24 | CIDR | Reported campaign infrastructure CIDR | Medium |
| 107.189.19.52 | IP | Command-and-control server (observed in endpoint attempting payload retrieval) | High |
| 146.185.219.235 | IP | Israeli VPN node used as egress (Handala operator slip) | Medium |
| Starlink IP ranges | CIDR | Used post-Iran internet blackout (Jan 2026+) for C2 and operational continuity | High |

### Domains / URLs

| IOC | Type | Description | Confidence |
|---|---|---|---|
| handala-redwanted[.]to | Domain | Doxxing / target identification site (created July 12, 2025) | High |
| link-target[.]net/jfby32 | URL | Delivery/lure URL observed in campaign | Medium |
| mega[.]nz / mega[.]io | Domain | Used to host malicious .msi installers and payloads | High |
| storjshare[.]io | Domain | Storj-hosted payload delivery for malicious installer | High |
| www[.]icanhazip[.]com | Domain | IP identification site used in victim profiling/recon | Medium |

---

## File / Malware Indicators

### Malware Families

| Name | Type | Description |
|---|---|---|
| **ZeroShred** | Wiper | Primary wiper tool (2025–2026); deployed in memory via multi-stage loader |
| **GoneXML** | Wiper (ransomware disguise) | Presents as ransomware but has no decryption functionality; purely destructive |
| **Hamsa Wiper** (HamsaUpdate) | Wiper | Used in Dec 2023 campaign; targets Windows & Linux; delivered via email lure |
| **Hatef Wiper** | Wiper | Custom wiper, earlier operations |
| **Handala Loader** | Loader | Delphi-based loader → AutoIT injector → destructive payload chain |

### Executables / Files

| IOC | Type | Description | Confidence |
|---|---|---|---|
| F5UPDATER.exe | Filename | Spear-phishing lure masquerading as F5 software update | High |
| GoXML.exe | Filename | Wiper-family executable | High |
| disable_defender.exe | Filename | Tool for disabling Windows Defender | Medium |
| mellona.exe | Filename | AV/defense disabling tool | Medium |
| OpenFileFinder.dll | Filename | DLL observed in data access/exfiltration stage | Medium |
| Phase3.ps1 | Filename | PowerShell script used in wiper deployment chain | Medium |
| rwdsk.sys | Filename | Raw disk driver artifact used by destructive tooling | High |

### Webshells

| IOC | Type | Description | Confidence |
|---|---|---|---|
| error4.aspx | Webshell | ASPX webshell observed in compromised web roots | High |
| ClientBin.aspx | Webshell | ASPX webshell | High |
| pickers.aspx | Webshell | ASPX webshell | High |
| Pickers.aspx | Webshell | ASPX webshell (case variant) | High |
| reGeorge | Webshell family | Used for web-tier persistence and lateral pivot | High |

### Service / Driver Artifacts

| IOC | Type | Description | Confidence |
|---|---|---|---|
| RawDisk3 | Service name | Used to run raw disk/destructive driver | High |
| Ukraine | Filename artifact | Wiper-stage artifact name appearing in reporting | Medium |

---

## Behavioral / Host Indicators

| Behavior | Description |
|---|---|
| Default hostnames DESKTOP-XXXXXX / WIN-XXXXXX | Pattern seen on Handala-controlled machines connecting from commercial VPN nodes |
| NetBird installation via RDP + browser download | Attacker manually downloads NetBird from official site after gaining RDP access; creates internal P2P tunnel |
| 5+ simultaneous attacker machines in environment | Multi-machine parallel wiping during destructive phase (Stryker-style attacks) |
| Group Policy wiper distribution | Uses AD Group Policy to push wipers across the network for maximum spread |
| Simultaneous 4+ wiper techniques | Maximizes destruction; look for multiple concurrent disk-wiping processes |
| Telegram C2 | Data exfiltration to private Telegram channels controlled by actors |
| Credential stuffing against VPN appliances | Hundreds of failed VPN login attempts from commercial VPN IP ranges preceding successful access |

---

## Email / Phishing Infrastructure

| IOC | Type | Description | Confidence |
|---|---|---|---|
| ProtonMail accounts | Email provider | Used for actor communications | Medium |
| Outlook accounts | Email provider | Used for actor communications | Medium |
| "Roxie Collins" | Domain registrant name | Used to register initial Handala websites | Medium |
| "vie6c" | WordPress username | Primary admin account on Handala-operated sites | Medium |

---

## MITRE ATT&CK Mapping

| Technique | ID | Notes |
|---|---|---|
| Spear-Phishing Attachment | T1566.001 | F5UPDATER.exe, CrowdStrike fix lures |
| Valid Accounts (VPN creds) | T1078 | Brute-forced or purchased VPN credentials |
| Scripting (PowerShell) | T1059.001 | Phase3.ps1 wiper deployment |
| Web Shell | T1505.003 | error4.aspx, reGeorge family |
| Remote Desktop Protocol | T1021.001 | Primary lateral movement method |
| Software Deployment Tools (Intune) | T1072 | Stryker attack — Intune abused for wiper distribution |
| Disk Wipe | T1561.001 | ZeroShred, Hatef, Hamsa wipers |
| Data Encrypted for Impact (Disguise) | T1486 | GoneXML presents as ransomware |
| Defacement | T1491.001 | Website defacement with psychological messaging |
| Exfiltration to Cloud Storage | T1567.002 | Mega.nz, Storj used for staging |
| Inhibit System Recovery | T1490 | Wiper operations prevent recovery |

---

## Hunting Queries (Conceptual)

```
# Hunt for NetBird installation from unexpected sources
process.name = "netbird.exe" AND process.parent.name IN ("explorer.exe", "chrome.exe", "firefox.exe")

# Hunt for known Handala webshell filenames
file.path MATCHES "/(error4|ClientBin|pickers|Pickers)\.aspx$"

# Hunt for raw disk driver loading
driver.name IN ("rwdsk.sys") OR service.name = "RawDisk3"

# Hunt for VPN brute force then success pattern
auth.failed_count > 20 AND auth.outcome = "success" AND src.ip.asn IN [Starlink ASNs, 169.150.227.0/24]
```

---

*Sources: Check Point Research (March 2026), Inversion6 Threat Brief, ExtraHop Iranian Cyber Analysis, KELA Cyber, RansomLook*
