# Threat Actor Profile: Medusa Ransomware / Storm-1175

## Identity

| Field | Detail |
|---|---|
| **Scoundrel #** | 028 |
| **Primary Name** | Medusa Ransomware |
| **Key Affiliate** | Storm-1175 |
| **Aliases** | Medusa Blog (leak site), Gaze (payload name), Storm-1175 (primary affiliate — Microsoft taxonomy) |
| **Type** | Cybercriminal — Ransomware-as-a-Service (RaaS) |
| **Origin** | **Platform core:** Russia-linked (behavioral indicators) / **Storm-1175 affiliate:** China-based (financially motivated) / **Other affiliates:** DPRK (Lazarus Group confirmed) |
| **Active Since** | June 2021 (first identified); significant escalation from 2023 |
| **Status** | 🔴 ACTIVE |
| **Threat Level** | 🔴 HIGH |
| **CISA Advisory** | AA25-071A — #StopRansomware: Medusa Ransomware (March 12, 2025, joint CISA/FBI/MS-ISAC) |
| **Sectors Targeted** | Healthcare, Education, Finance, Professional Services, Manufacturing, Critical Infrastructure |
| **Geographic Focus** | United States, United Kingdom, Australia (primary); Italy, New Zealand (confirmed) |

---

## Overview

Medusa is one of the most operationally dangerous ransomware ecosystems active in 2025–2026 — not because it invented a new model, but because it has been adopted by affiliates who operate with a level of velocity and technical sophistication that breaks conventional incident response assumptions.

**The platform itself** is a mature, double-extortion RaaS first identified in June 2021. The core developers are assessed as Russia-linked based on behavioral indicators: the malware explicitly avoids targets in Russia and CIS-aligned countries, and the operation circulates on Russian-language dark web forums. The platform has claimed **500+ victims** globally since launch.

**Storm-1175** — the affiliate that has defined Medusa's threat profile in 2025–2026 — is a China-based, financially motivated cybercriminal group that Microsoft publicly attributed in April 2026. What makes Storm-1175 exceptional is not just *what* it does, but *how fast* it does it:

- **24-hour attack cycle:** In documented cases, Storm-1175 moves from initial exploitation to full network encryption and data exfiltration in under 24 hours. Most security programs are designed around multi-day dwell times. Storm-1175 operates in hours.
- **Zero-day capability:** The group has exploited at least three zero-day vulnerabilities, with two (CVE-2025-10035 and CVE-2026-23760) weaponized approximately one week *before public disclosure*. Pre-disclosure exploitation at this scale has historically been associated with nation-state actors, not financially motivated RaaS affiliates.
- **Exploit breadth:** Since 2023, Storm-1175 has exploited 16+ vulnerabilities across 10+ enterprise products, rotating through new N-days often within 1–24 hours of public disclosure.

**The multi-nation affiliate picture** adds a further complication. CISA documented 300+ victims across critical infrastructure in its March 2025 advisory. By April 2026, the total on the leak site exceeded 500. Lazarus Group (Scoundrel #003, DPRK) was separately confirmed operating as a Medusa affiliate between November 2025 and February 2026 — targeting U.S. healthcare and nonprofits. Medusa's platform survives and scales regardless of which nation-state or criminal group is holding the keyboard.

---

## Attack Chain

```
RECON: External scan → identify internet-facing apps with recent/zero-day CVEs
         ↓
ACCESS: Exploit web-facing application (N-day or zero-day; 16+ CVEs exploited since 2023)
         ↓
PERSIST: Create new admin-group user account + web shell / RMM tool deployment
         ↓
LATERAL MOVEMENT: LOLBins (PowerShell, PsExec, Impacket) + Cloudflare tunnels (renamed conhost.exe)
                  + RMM tools (Atera, AnyDesk, SimpleHelp, MeshAgent, N-able, etc.)
         ↓
CREDENTIAL THEFT: Mimikatz + LSASS dump (Task Manager / Impacket)
                  + WDigest registry (UseLogonCredential) 
                  + Veeam credential recovery script
                  → Domain Controller pivot → NTDS.dit + SAM hive extraction
         ↓
DEFENSE EVASION: Defender AV registry tampering + C:\ exclusion (encoded PowerShell)
                 + Firewall modification to enable RDP if blocked
         ↓
EXFILTRATION: Bandizip (archive) + Rclone (sync to attacker-controlled cloud storage)
         ↓
DEPLOY: PDQ Deployer (RunFileCopy.cmd) or Group Policy Update → gaze.exe
        AES-256 encryption → .medusa extension appended
         ↓
EXTORT: Double extortion (encrypt + leak threat) — or triple extortion (documented by FBI)
```

**Typical timeline:** 5–6 days; minimum observed: **under 24 hours**

---

## Initial Access — Exploit Inventory

Storm-1175 has exploited 16+ vulnerabilities across 10+ products since 2023:

| CVE | Product | Notes |
|---|---|---|
| CVE-2022-41080 + CVE-2022-41082 | Microsoft Exchange (OWASSRF chain) | Initial OWA access → RCE |
| CVE-2023-21529 | Microsoft Exchange | |
| CVE-2023-27350, CVE-2023-27351 | PaperCut NG/MF | |
| CVE-2023-46805, CVE-2024-21887 | Ivanti Connect Secure | |
| CVE-2024-1708, CVE-2024-1709 | ConnectWise ScreenConnect | |
| CVE-2024-27198, CVE-2024-27199 | JetBrains TeamCity | |
| CVE-2024-57726, CVE-2024-57727, CVE-2024-57728 | SimpleHelp | |
| CVE-2025-31161 | CrushFTP | |
| CVE-2025-31324 | SAP NetWeaver | Exploited **1 day** after public disclosure |
| CVE-2025-10035 | Fortra GoAnywhere MFT | ⚠️ **ZERO-DAY** — CVSS 10.0; exploited **1 week before disclosure** |
| CVE-2025-52691 | SmarterTools SmarterMail | |
| CVE-2026-23760 | SmarterTools SmarterMail | ⚠️ **ZERO-DAY** — exploited **1 week before disclosure** |
| CVE-2026-1731 | BeyondTrust | |

---

## Tools & Tradecraft (MITRE ATT&CK)

| Phase | Tool / Technique | ATT&CK |
|---|---|---|
| Initial Access | Web application exploitation (see exploit inventory) | T1190 |
| Persistence | New admin user creation (`net user /add`) | T1136.001 |
| Persistence | Web shell deployment | T1505.003 |
| Persistence | RMM tools (Atera, AnyDesk, MeshAgent, SimpleHelp, ConnectWise, Level, N-able, DWAgent) | T1219 |
| Persistence | Cloudflare tunnels (renamed as `conhost.exe`) | T1572 |
| Lateral Movement | PowerShell, PsExec (LOLBins) | T1059.001, T1569.002 |
| Lateral Movement | Impacket (SMB/WMI) | T1021.002 |
| Lateral Movement | PDQ Deployer | T1072 |
| Lateral Movement | RDP (enabled via firewall modification if blocked) | T1021.001 |
| Credential Access | Mimikatz | T1003.001 |
| Credential Access | LSASS dump via Task Manager | T1003.001 |
| Credential Access | WDigest cache (`UseLogonCredential` registry) | T1003.001 |
| Credential Access | Veeam credential recovery script | T1555 |
| Credential Access | NTDS.dit extraction (via DC pivot) | T1003.003 |
| Credential Access | SAM hive access | T1003.002 |
| Defense Evasion | Defender AV registry tampering | T1562.001 |
| Defense Evasion | C:\ drive added to AV exclusion (encoded PowerShell) | T1562.001 |
| Defense Evasion | Cloudflare tunnel process rename | T1036.005 |
| Exfiltration | Rclone (cloud sync exfil) | T1567.002 |
| Exfiltration | Bandizip (archive staging) | T1560.001 |
| Impact | gaze.exe — Medusa payload; AES-256; `.medusa` extension; network-wide via PDQ Deployer or GPO | T1486 |
| Extortion | Double extortion (encrypt + leak site threat) | T1657 |
| Extortion | Triple extortion (FBI documented: victim paid → re-extorted for "real decryptor") | T1657 |

---

## Confirmed Victims

| Victim | Sector | Date | Ransom | Notes |
|---|---|---|---|---|
| SimonMed Imaging | Healthcare | Jan 2025 | $1M | 1.27M patients affected; 2TB exfiltrated |
| Bell Ambulance | Healthcare | Feb 2025 | $400K | 219.5 GB stolen |
| HCRG Care Group (UK) | Healthcare | Feb 2025 | $2M | 2.3TB exfiltrated |
| Passaic County, NJ | Government | Feb 2026 | Unknown | All systems + phone lines down |
| University of Mississippi Medical Center | Healthcare | Feb 2026 | $800K | 9-day outage; all outpatient clinics closed; cancer infusion patients rescheduled; paper operations; 20% revenue drop |
| Uffizi Gallery, Florence | Culture | Feb 1, 2026 | Unknown | Simultaneous with La Sapienza University |
| La Sapienza University, Rome | Education | Feb 1, 2026 | Unknown | Simultaneous with Uffizi Gallery |
| Toyota Financial Services | Finance | (prior) | $8M | Prior campaign; high-profile |
| 300+ critical infrastructure orgs | Multiple | 2021–2025 | Various | Per CISA AA25-071A |
| 500+ total on leak site | Multiple | 2021–2026 | Various | Per Medusa Blog |

> **40+ victims claimed in January–February 2025 alone — nearly double the volume for the same period in 2024.**

---

## Threat Connections

🔗 **Connects to Scoundrel #003 — Lazarus Group (DPRK)**
Broadcom/Symantec confirmed Lazarus Group operating as a Medusa RaaS affiliate between November 2025 and February 2026, deploying Medusa ransomware against U.S. healthcare organizations and nonprofits using custom backdoor tooling alongside the platform.

🔗 **Connects to Scoundrel #025 — Akira Ransomware**
Microsoft linked Storm-1175, alongside three other cybercriminal groups, to attacks deploying Akira via a VMware ESXi authentication bypass flaw in July 2024. Storm-1175 has used multiple RaaS platforms.

🔗 **Connects to ZD-001 — BlueHammer (CVE-2026-33825)**
BlueHammer profile explicitly identified Storm-1175/Medusa as a key ransomware group running 24-hour attack cycles with documented LPE exploit integration. A BlueHammer-enabled attack from Storm-1175 would be a worst-case scenario.

🔗 **Storm-2603 (untracked) — parallel China-nexus RaaS**
Microsoft separately documented Storm-2603, a China-backed group exploiting SharePoint zero-days ("ToolShell" chain) then deploying LockBit Black and Warlock ransomware — a separate but parallel convergence of state-adjacent capability with RaaS monetization.

---

## Mitigations

| Priority | Action |
|---|---|
| 🔴 IMMEDIATE | **Patch internet-facing applications within 24 hours of disclosure** — Storm-1175 has exploited CVEs within 1 day. Standard patch windows are insufficient. |
| 🔴 IMMEDIATE | **Continuously monitor CISA KEV catalog** — Add new KEV entries to emergency patch queue automatically. |
| 🔴 HIGH | **RMM tool allowlisting** — Block unapproved RMM tools (AnyDesk, Atera, SimpleHelp, MeshAgent) via application control. Flag any RMM not in your approved list as a P1 alert. |
| 🔴 HIGH | **Behavioral detection: LSASS, WDigest, Rclone** — Alert on LSASS memory access outside approved security tools; WDigest registry modifications; Rclone running on servers. |
| 🔴 HIGH | **Network segmentation** — Isolate internet-facing systems from internal networks. Slow lateral movement even if perimeter is breached. |
| 🟡 HIGH | **Veeam hardening** — Separate credential sets for backup infrastructure; not accessible from domain admin accounts. |
| 🟡 HIGH | **Monitor Cloudflare tunnel processes on servers** — Legitimate use is rare on server infrastructure; flag any cloudflared.exe process on non-DMZ servers. |
| 🟡 HIGH | **WAF + IDS signatures for all Storm-1175 exploited CVEs** — Deploy rules even for products you don't think you use (check shadow IT). |
| 🟡 MEDIUM | **Offline/air-gapped backups** — Given triple extortion documentation, payment doesn't guarantee recovery. Offline backups are the only reliable recovery posture. |
| 🟡 MEDIUM | **External attack surface monitoring** — Storm-1175 targets what's visible and exploitable. Know your footprint before they do. |
| 🟡 MEDIUM | **Enable tamper protection + `DisableLocalAdminMerge`** — Prevents Defender exclusion manipulation via local admin accounts. |
| ⚪ AWARENESS | **Incident response plan should account for sub-24h timeline** — Traditional IR procedures assume multi-day dwell. Tabletop exercises must simulate 24-hour scenarios. |

---

## A&D Relevance

**Indirect but real.** Medusa's confirmed targeting profile is healthcare, education, finance, and professional services — not aerospace & defense as a primary vector. However:

- **Manufacturing** is a confirmed Medusa target sector, and the A&D supply chain runs through manufacturing Tier 1–3 suppliers
- **Storm-1175's zero-day capability** means any internet-facing perimeter is a potential target regardless of sector
- **The Lazarus Group + Medusa combination** is directly relevant: Lazarus is a top-3 A&D threat actor (Scoundrel #003) now using the Medusa platform
- **The 24-hour attack cycle** renders standard patch windows irrelevant — any organization with an internet-facing application is on a permanent zero-day footing
- **Double/triple extortion** against defense-adjacent organizations could trigger ITAR/CUI exposure concerns beyond just operational disruption

---

## References

- [Microsoft Threat Intelligence — Storm-1175 blog (April 6, 2026)](https://www.microsoft.com/en-us/security/blog/2026/04/06/storm-1175-focuses-gaze-on-vulnerable-web-facing-assets-in-high-tempo-medusa-ransomware-operations/)
- [CISA AA25-071A — #StopRansomware: Medusa Ransomware (March 12, 2025)](https://www.cisa.gov/newsevents/cybersecurity-advisories/aa25-071a)
- [Cloud Security Alliance — Storm-1175: Zero-Day Exploit Chains in Medusa Ransomware Attacks (April 7, 2026)](https://labs.cloudsecurityalliance.org/research/csa-research-note-storm1175-medusa-ransomware-zero-day-20260/)
- [The Hacker News — China-Linked Storm-1175 Exploits Zero-Days to Rapidly Deploy Medusa Ransomware (April 7, 2026)](https://thehackernews.com/2026/04/china-linked-storm-1175-exploits-zero.html)
- [CybelAngel — Storm-1175's 24-Hour Attack Cycle (April 8, 2026)](https://cybelangel.com/blog/storm-1175-s-24-hour-attack-cycle/)
- [SOS Ransomware — Medusa: How Storm-1175 Exploits Zero-Days to Strike in 24 Hours (April 15, 2026)](https://sosransomware.com/en/ransomware-groups/medusa-ransomware-how-storm-1175-exploits-zero-days-to-strike-in-24-hours/)

---

*Profile created: 2026-04-29 | Author: C3PO | Admiralty Grade: A1 | TLP: WHITE*
