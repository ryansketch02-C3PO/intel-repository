# LockBit

## Identity

| Field | Details |
|---|---|
| **Primary Alias** | LockBit |
| **Other Names** | ABCD Ransomware (predecessor), LockBit 2.0 / LockBit Red, LockBit 3.0 / LockBit Black, LockBit Green, LockBit 4.0, LockBit 5.0 |
| **Type** | Cybercriminal Group — Ransomware-as-a-Service (RaaS) |
| **Origin** | Russia (administrator); international affiliate network |
| **Lead Admin Alias** | LockBitSupp |
| **Known Admin** | Dmitry Yuryevich Khoroshev (indicted, sanctioned — US/UK/AUS) |
| **First Observed** | September 2019 |
| **Status** | ⚠️ Active — LockBit 5.0 deployed since September 2025 |
| **Motivation** | Financial — ransomware, double extortion |
| **Threat Level** | 🔴 HIGH |
| **Admiralty Grade** | A2 (highly reliable source, confirmed activity) |

---

## Overview

LockBit is arguably the most prolific ransomware operation in history. Operating as a **Ransomware-as-a-Service (RaaS)** since September 2019, the group built a franchise model where a core developer team maintains the malware and infrastructure, while independent **affiliates** carry out the intrusions and deploy the ransomware in exchange for a cut of the ransom.

At its peak (2022–2023), LockBit accounted for **20–30% of all global ransomware victims** — dominating the RaaS landscape like no other. Its success came from a ruthless focus on **ease of use, affiliate loyalty, and continuous technical innovation**. Unlike many criminal groups that pay themselves first, LockBit let affiliates collect ransoms directly — a trust signal that attracted top-tier operators.

**Operation Cronos** (February 2024) — a joint US/UK/international law enforcement takedown — seized LockBit's infrastructure, decryption keys, and admin panels. LockBitSupp was publicly identified, sanctioned, and indicted. The group was thought finished.

They weren't.

**LockBit 5.0** emerged in September 2025 on the six-year anniversary of the group's founding. The new variant hit Windows, Linux, and VMware ESXi simultaneously, with enhanced evasion and anti-analysis capabilities. By early 2026, LockBit had already claimed 100+ new victims on its refreshed data leak site.

The Force does not die. It just learns new forms.

---

## Targets

- **Primary Industries:** Manufacturing, Healthcare, Finance, Technology, Government, Critical Infrastructure, Logistics
- **Geographic Focus:** United States (primary ~65%), India, Brazil, Europe; **deliberately avoids Russian-language systems**
- **Preferred Target Profile:** Large enterprises with exposed RDP/VPNs, weak credential hygiene, virtualized infrastructure (ESXi), and inadequate patching cadence

---

## Known Attacks / Notable Incidents

| Date | Target | Impact |
|---|---|---|
| 2020 | **Multiple manufacturing & logistics companies (US)** | Initial affiliate deployments; ABCD ransomware predecessor |
| 2022 | **Accenture** | LockBit 2.0; $50M ransom demand; 6TB data threatened |
| 2022 | **Government of Ontario (Canada)** | LockBit 2.0; ransomware deployed on provincial systems |
| 2022 | **Italian Revenue Agency** | 100GB data exfiltration claimed |
| 2023 | **Boeing** | LockBit 3.0; claimed breach; published 43GB of data after Boeing refused ransom |
| 2023 | **Industrial & Commercial Bank of China (ICBC)** | US Treasury markets disrupted; LockBit affiliate attack |
| 2023 | **Royal Mail (UK)** | Operations severely disrupted; $80M ransom demand refused |
| 2024 | **Operation Cronos (Disrupted)** | FBI/NCA/Europol seized infrastructure, 200 crypto wallets, 1,000 decryption keys |
| Sep 2025 | **LockBit 5.0 relaunch** | 100+ victims claimed within first months; Windows/Linux/ESXi simultaneous attacks |

---

## TTPs (MITRE ATT&CK)

### Initial Access
- **External Remote Services (T1133):** Exploit exposed RDP, VPN vulnerabilities (Fortinet, Citrix, SonicWall)
- **Phishing (T1566):** Spearphishing emails with malicious attachments or credential harvesting links
- **Valid Accounts (T1078):** Purchase or brute-force stolen credentials; use IABs (Initial Access Brokers)
- **Exploit Public-Facing Applications (T1190):** CVE-2023-4966 (Citrix Bleed), CVE-2023-47246 (SysAid), FortiGate bugs

### Execution
- **Command & Scripting (T1059):** PowerShell and batch scripts for reconnaissance, credential hunting, privilege escalation
- **Windows Management Instrumentation (T1047):** Lateral spread via WMI
- **Malicious File (T1204.002):** Deliver LockBit payload via compromised endpoints

### Persistence
- **Remote Access Software (T1219):** AnyDesk, Atera RMM, ScreenConnect, TeamViewer, Splashtop deployed post-compromise
- **Scheduled Tasks (T1053):** Ensure payload survives reboots

### Privilege Escalation
- **Credential Dumping (T1003):** Mimikatz, ProcDump (LSASS), LostMyPassword
- **Pass-the-Hash / Pass-the-Ticket:** Lateral movement via stolen Windows auth material

### Defense Evasion
- **DLL Reflection / In-Memory Loading (T1620):** LockBit 5.0 decrypts and loads PE binary in memory without touching disk
- **ETW Patching:** Patches EtwEventWrite API to blind Windows telemetry
- **Service Termination (T1489):** Kills 63 security services using hashed service name comparison (anti-EDR)
- **Log Clearing (T1070.001):** EvtClearLog API wipes all event logs post-encryption
- **Anti-Analysis / Anti-Debugging:** Heavy obfuscation, geolocation checks (terminates on Russian locale)

### Discovery
- **Network Scanning (T1046):** SoftPerfect Network Scanner for target enumeration
- **System Information Discovery (T1082):** PowerShell discovery scripts; map AD structure

### Lateral Movement
- **RDP / SMB (T1021):** Classic lateral movement via Remote Desktop and file share abuse
- **Cobalt Strike / Metasploit (T1587.001):** Professional pentesting tools repurposed for lateral spread

### Collection & Exfiltration
- **Rclone / MegaSync (T1567.002):** Exfiltrate to cloud storage before encryption (double extortion)
- **StealBit:** LockBit's own custom exfiltration tool (seized in Operation Cronos, but rebuilt)
- **Archive for Exfiltration (T1560):** Compress and stage data before transfer

### Impact
- **Data Encrypted for Impact (T1486):** XChaCha20 encryption (LockBit 5.0); randomized 16-char hex file extension
- **Inhibit System Recovery (T1490):** Delete shadow copies, disable recovery tools
- **Financial Extortion (T1657):** Double extortion — pay or data gets published on DLS

---

## Infrastructure & Technical Details

- **LockBit 5.0 C2 (exposed Dec 2025):** `205.185.116.233` / `karma0.xyz` (hosted AS53667 PONYNET/FranTech)
- **Encryption:** XChaCha20 (LockBit 5.0) — faster, stronger than previous AES variants
- **File Extension:** Randomized 16-character hexadecimal appended to encrypted files
- **Platforms:** Windows (primary), Linux, VMware ESXi (dedicated ESXi binary in 5.0)
- **Ransom Notes:** Direct victims to Tor-based negotiation portals with 30-day countdown
- **Geofencing:** Hard-coded termination on Russian language/locale detection

---

## Known Affiliates / Members (Charged)

| Name | Alias | Status |
|---|---|---|
| **Dmitry Khoroshev** | LockBitSupp | Indicted (DOJ), OFAC sanctioned — at large |
| **Artur Sungatov** | — | Indicted (DOJ NJ) — at large |
| **Ivan Kondratyev** | Bassterlord | Indicted (DOJ NJ + N.CA); also charged for REvil attacks |
| **Mikhail Matveev** | Wazawaka / m1x | Indicted; FBI $10M reward — at large |
| **Mikhail Vasiliev** | — | Arrested (Canada); extradited to US |
| **Ruslan Astamirov** | — | In US custody |

---

## Connection Web

LockBit's affiliate ecosystem overlaps extensively with other tracked actors:
- **Ivan Kondratyev (Bassterlord)** was also an affiliate of **REvil** — dual-tracked
- **Scattered Spider (#013)** used ALPHV/BlackCat RaaS; post-ALPHV collapse, members moved to LockBit, DragonForce, and RansomHub affiliates
- **DragonForce** announced a symbolic "coalition" with LockBit and Qilin in Sep 2025 — likely marketing, no confirmed shared infrastructure

---

## Mitigations

- Disable unnecessary RDP exposure; enforce NLA and MFA on all remote access
- Patch promptly — LockBit affiliates rapidly exploit newly disclosed CVEs (Citrix, Fortinet, SonicWall)
- Implement immutable, offline backups — snapshot backups stored on ESXi are not sufficient
- Deploy EDR with ETW protection rules; alert on EvtClearLog activity
- Enable Protected Event Logging to prevent log tampering
- Network segment ESXi hosts and domain controllers; restrict lateral movement pathways
- Monitor for RClone, MegaSync, or unusual cloud upload activity
- Block `205.185.116.233` and `karma0.xyz` at perimeter (LockBit 5.0 infrastructure)

---

## References

- [DOJ — LockBit Disruption Press Release (Feb 2024)](https://www.justice.gov/archives/opa/pr/us-and-uk-disrupt-lockbit-ransomware-variant)
- [CISA/FBI/ASD — LockBit Advisory (Aug 2023)](https://www.cyber.gov.au/about-us/advisories/understanding-ransomware-threat-actors-lockbit)
- [Arete — LockBit 5.0 Analysis (Apr 2026)](https://areteir.com/resources/lockbit-5-0-ransomware-threat-resurgence)
- [H-ISAC — LockBit 5.0 Threat Bulletin (Oct 2025)](https://www.aha.org/system/files/media/file/2025/10/h-isac-tlp-white-threat-bulletin-new-lockbit-ransomware-emerges-as-most-dangerous-yet-10-1-2025.pdf)
- [Check Point — State of Ransomware Q3 2025](https://research.checkpoint.com/2025/the-state-of-ransomware-q3-2025/)
- [CyberPress — LockBit 5.0 Infrastructure Leak (Dec 2025)](https://cyberpress.org/massive-leak-exposes-lockbit-5-0-servers-ips-and-domain/)
- [ThreatFox — LockBit IOC Database](https://threatfox.abuse.ch/browse/malware/win.lockbit/)

---

*Profile created: 2026-04-11 | Source grade: A2 | TLP: WHITE*
