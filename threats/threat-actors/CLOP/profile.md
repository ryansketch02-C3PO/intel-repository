# Cl0p (TA505)

## Identity

| Field | Details |
|---|---|
| **Primary Alias** | Cl0p |
| **Other Names** | CLOP, Clop, TA505, FIN11, Lace Tempest, Spandex Tempest, DEV-0950, GRACEFUL SPIDER, GOLD TAHOE, GOLD EVERGREEN, Chimborazo, Hive0065, ATK103 |
| **Type** | Cybercriminal Group — Ransomware-as-a-Service (RaaS) / Data Extortion |
| **Origin** | Russia/CIS (Russian-speaking; CIS exclusion behavior strongly suggests domestic operation) |
| **First Observed** | February 2019 (Cl0p malware); TA505 active since at least 2014 |
| **Status** | 🔴 Active — zero-day exploitation campaigns ongoing into 2025/2026 |
| **Motivation** | Financial — ransomware, double/triple extortion, data theft |
| **Threat Level** | 🔴 HIGH |
| **Admiralty Grade** | A2 (highly reliable source, confirmed multi-agency attribution) |

---

## Overview

Cl0p is one of the most prolific and technically sophisticated ransomware operations on the planet. First emerging in 2019 as a variant of the CryptoMix ransomware family, it is operated by the criminal group **TA505** — a Russian-speaking, financially motivated syndicate active since at least 2014.

What separates Cl0p from the galaxy of ransomware thugs isn't just their technical skill — it's their **strategic use of zero-day vulnerabilities** in enterprise file transfer software to achieve **mass simultaneous compromise**. Rather than hitting one target at a time, Cl0p exploits a single flaw and instantly breaches hundreds or thousands of organizations. It's less of a blaster, more of a Death Star.

Their evolution is a case study in criminal adaptation:
- **2019:** Classic ransomware with digitally signed binaries to dodge detection
- **2020:** Pivot to double extortion — steal first, then encrypt, then threaten to leak
- **2020–2021:** Accellion FTA zero-days — 100 companies breached simultaneously
- **2023:** GoAnywhere MFT (CVE-2023-0669) — 130 victims in 10 days
- **2023:** **MOVEit Transfer** (CVE-2023-34362) — ~2,000–8,000 organizations globally, including US federal agencies, BBC, British Airways, Shell. Estimated $75–100M in proceeds.
- **2024–2025:** Cleo software zero-days (CVE-2024-50623, CVE-2024-55956) — 66+ organizations compromised in resurgent campaign

The US State Department has placed a **$10 million bounty** on information linking Cl0p to a foreign government.

Total known victims: **1,025+** | Total estimated extortion proceeds: **$500M+**

---

## Targets

- **Primary Industries:** Finance, Healthcare, Manufacturing, Education, Technology, Retail, Transportation, Energy, Government, Legal
- **Geographic Focus:** North America (US/Canada primary), Europe (UK, Germany, Netherlands, France)
- **Deliberately EXCLUDES:** CIS/former Soviet countries; Russian-language systems are skipped — classic Moscow tells
- **Preferred Target Profile:** Organizations using enterprise managed file transfer (MFT) solutions; large enterprises with critical data and capacity to pay

---

## Known Attacks / Notable Incidents

| Date | Target / Campaign | Impact | CVE |
|---|---|---|---|
| 2020–2021 | **Accellion FTA mass exploitation** | ~100 victims incl. Kroger, Reserve Bank of New Zealand, Singtel, ASIC, Office of WA State Auditor | CVE-2021-27101/02/03/04 |
| Feb 2023 | **GoAnywhere MFT exploitation** | 130+ organizations breached in 10 days; Hitachi Energy, Community Health Systems | CVE-2023-0669 |
| May–Jun 2023 | **MOVEit Transfer — Operation Lemurloot** | ~2,000–8,000 orgs globally; BBC, British Airways, Shell, Ernst & Young, NYC Dept of Education, multiple US federal agencies; $75–100M estimated proceeds | CVE-2023-34362, 35036, 35708 |
| Dec 2024–early 2025 | **Cleo software exploitation** | 66+ organizations; LEMURLOOT-style web shell implant; data extortion without encryption | CVE-2024-50623, CVE-2024-55956 |
| Oct 2025 | **Oracle E-Business Suite exploitation** | Critical zero-day in Oracle EBS (order management, procurement, logistics) | CVE-2025-61882 |

---

## TTPs (MITRE ATT&CK)

### Initial Access
- **Exploit Public-Facing Application (T1190):** PRIMARY vector — zero-day and N-day exploitation of file transfer software (MFT, FTA, MFT servers)
- **Phishing — Spearphishing Attachment (T1566.001):** Historical method; macro-enabled Office docs deploying Get2 loader → SDBot / FlawedAmmyy
- **Valid Accounts (T1078):** Use of purchased/compromised credentials via Initial Access Brokers

### Execution
- **Command & Scripting Interpreter (T1059):** PowerShell (T1059.001), Windows Command Shell (T1059.003)
- **Shared Modules / DLL Loading (T1129):** Malicious DLL injection
- **Native API (T1106)**

### Persistence
- **Web Shell (T1505.003):** LEMURLOOT (MOVEit), DEWMODE (Accellion FTA), FileUtils.java (Oracle EBS)
- **Boot/Logon Autostart Execution (T1547):** SDBot application shimming
- **Create/Modify System Process — Windows Service (T1543.003)**

### Privilege Escalation
- **Exploitation for Privilege Escalation (T1068)**
- **Domain Policy Modification — Group Policy (T1484.001)**
- **Hijack Execution Flow (T1574):** DLL side-loading (T1574.002)

### Defense Evasion
- **Indicator Removal (T1070):** Clear Windows event logs (T1070.001), file deletion (T1070.004)
- **Process Injection — DLL Injection (T1055.001)**
- **Masquerading — Invalid Code Signature (T1036.001):** Early campaigns used signed binaries
- **Deobfuscate/Decode Files (T1140)**
- **Impair Defenses (T1562.001):** Disable AV/security tooling
- **Indirect Command Execution (T1202)**

### Discovery
- **Remote System Discovery (T1018)**
- **System Information Discovery (T1082)**
- **Domain Trust Discovery (T1482)**
- **Security Software Discovery (T1518.001)**
- **File and Directory Discovery (T1083)**
- **Process Discovery (T1057)**
- **Query Registry (T1012)**

### Lateral Movement
- **SMB/Windows Admin Shares (T1021.002)**
- **Remote Desktop Protocol (T1021.001):** Session hijacking
- **Pass-the-Hash (T1550.002)**
- **Lateral Tool Transfer (T1570)**

### Collection & Exfiltration
- **Data from Local System (T1005)**
- **Exfiltration Over Web Service (T1567):** Cloud storage (Mega.nz frequently used)
- **Exfiltration Over C2 Channel (T1041)**
- **Automated Exfiltration (T1020)**

### Impact
- **Data Encrypted for Impact (T1486)**
- **Data Destruction (T1485):** Delete originals post-encryption
- **Inhibit System Recovery (T1490):** vssadmin.exe to delete shadow copies

---

## Malware & Tooling

| Tool | Role |
|---|---|
| **Cl0p ransomware** | Encryption payload; `.clop` / `.Cllp` / `.Cl0p` extensions; CIS language check; CIS exclusion |
| **LEMURLOOT** | MOVEit-specific web shell; hardcoded password auth; data exfil & user management |
| **DEWMODE** | Accellion FTA web shell; used by FIN11 affiliate |
| **FileUtils.java** | Oracle EBS backdoor downloader |
| **TrueBot (Silence Group)** | First-stage downloader; system recon, screenshots; delivers FlawedGrace or Cobalt Strike |
| **FlawedAmmyy** | RAT (active since 2016); info collection, AV detection, C2 comms, additional malware delivery |
| **FlawedGrace** | RAT; advanced command execution, C2 communication |
| **SDBot** | RAT; infection propagation, application shimming for persistence |
| **Get2 Loader** | Phishing-stage downloader; drops SDBot, FlawedAmmyy, FlawedGrace |
| **Cobalt Strike** | Post-AD-compromise C2 framework |

---

## Key CVEs Exploited

| CVE | CVSS | Product | Description |
|---|---|---|---|
| CVE-2021-27101 | 9.8 | Accellion FTA | SQL injection → RCE |
| CVE-2021-27102 | 7.8 | Accellion FTA | OS command execution via web service |
| CVE-2021-27103 | 9.8 | Accellion FTA | SSRF via POST request |
| CVE-2021-27104 | 9.8 | Accellion FTA | OS command execution via SOAP action |
| CVE-2022-31199 | 9.8 | Netwrix Auditor | RCE used to deliver TrueBot |
| CVE-2023-0669 | 7.2 | Fortra GoAnywhere MFT | Pre-auth RCE |
| CVE-2023-34362 | 9.8 | Progress MOVEit Transfer | SQL injection → data exfil |
| CVE-2023-35036 | 9.1 | Progress MOVEit Transfer | SQL injection |
| CVE-2023-35708 | 9.8 | Progress MOVEit Transfer | SQL injection |
| CVE-2024-50623 | 9.8 | Cleo LexiCom/VLTrader/Harmony | Unrestricted file upload → RCE |
| CVE-2024-55956 | Critical | Cleo Harmony/VLTrader/LexiCom | Unauthenticated RCE via API |
| CVE-2024-5806 | 9.1 | Progress MOVEit Transfer SFTP | Auth bypass |
| CVE-2025-61882 | Critical | Oracle E-Business Suite | Zero-day; ERP systems compromise |

---

## Associates & Relationships

| Group | Relationship |
|---|---|
| **FIN11** | Overlapping/same core operators or close affiliate; conducted Accellion FTA campaign |
| **Silence Group** | Initial Access Broker providing TrueBot-linked network access to TA505 |
| **LockBit** | TA505 also operates as a LockBit affiliate |
| **REvil** | TA505 previously operated as REvil affiliate |
| **Hive** | TA505 operated as Hive affiliate |
| **Evil Corp** | Some overlap in persona references; distinct groups but Russian cybercriminal ecosystem proximity |

---

## Defenses & Mitigations

- **Immediate patch priority:** All internet-facing MFT solutions (MOVEit, GoAnywhere, Cleo), Oracle EBS, Accellion FTA
- **Disable/segment MFT systems** from core network when patching is delayed
- **Hunt for web shells:** LEMURLOOT, DEWMODE indicators on MFT servers
- **Monitor for:** Mass VSS deletion, PowerShell obfuscation, unusual outbound transfers to Mega.nz or similar cloud storage
- **Behavioral analytics:** vssadmin.exe calls, taskkill.exe on security processes, ETW patching
- **Dark web monitoring:** Cl0p DLS — groups announce victims before full data release; early warning possible
- **Email security:** Block macro-enabled Office docs for phishing-based initial access (legacy vector)
- **Audit MFT database administrative accounts** after any detected exploitation

---

## References

- [CISA/FBI Advisory AA23-158A (MOVEit)](https://www.cisa.gov/news-events/cybersecurity-advisories/aa23-158a)
- [Canadian Centre for Cyber Security — TA505/Cl0p Profile](https://www.cyber.gc.ca/en/guidance/profile-ta505-cl0p-ransomware)
- [Blackpoint Cyber Threat Profile: Clop](https://blackpointcyber.com/wp-content/uploads/2025/10/Clop-1.pdf)
- [CYFIRMA — Cl0p Latest Attacks (Cleo Campaign)](https://www.cyfirma.com/research/cl0p-ransomware-latest-attacks/)
- [GBHackers — Oracle EBS Zero-Day Attribution](https://gbhackers.com/clop-ransomware/)
