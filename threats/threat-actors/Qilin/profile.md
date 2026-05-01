# Threat Actor Profile: Qilin (Agenda)

## Identity

| Field | Detail |
|---|---|
| **Scoundrel #** | 031 |
| **Primary Name** | Qilin |
| **Aliases** | Agenda, Qilin ransomware group |
| **Type** | Cybercriminal — Ransomware-as-a-Service (RaaS) |
| **Origin** | Russian-speaking operators (CIS avoidance confirmed via malware geofencing) |
| **Active Since** | October 2022 (first victim posted); significant escalation from mid-2024 |
| **Status** | 🔴 ACTIVE — #1 most prolific ransomware group globally as of Q1 2026 |
| **Threat Level** | 🔴 HIGH |
| **Affiliate Split** | 85/15 — affiliates keep 85 cents per extorted dollar (among highest in market) |
| **Sectors Targeted** | Manufacturing, Business Services, Technology, Healthcare, Financial Services |
| **Geographic Focus** | United States (primary), France, Canada, United Kingdom, Germany |
| **ATT&CK** | T1078, T1566, T1190, T1059.001, T1562.001, T1003.001, T1486, T1490, T1021.002, T1072, T1567.002 |

---

## Overview

Qilin is the most prolific ransomware operation on the planet right now — by victim count, by consistency, and by operational tempo. Four consecutive months above 100 publicly claimed victims in early 2026 (Jan 107 → Feb 104 → Mar 131 → Apr 103) is unprecedented in the tracking history of any single group. The total on their leak site hit **1,768 victims** as of April 30, 2026, spanning manufacturing, business services, technology, healthcare, and finance across 75+ countries.

What makes Qilin particularly dangerous isn't just volume — it's the **single-controller architecture** of their encryptor. Thrive's CIRT analysis of a current Qilin variant found that a single Windows executable orchestrates the *entire* attack chain: VSS deletion, Windows event log clearing, credential encryption, lateral movement via embedded PsExec, and autonomous ESXi fleet compromise via VMware.PowerCLI. No additional binaries are deployed after the initial controller. Every subsequent action uses **native, signed, legitimate Windows and VMware tools** — making signature-based detection largely ineffective.

**The growth story:** Qilin rose from a mid-tier RaaS in 2024 to the dominant platform in 2025 after absorbing talent from the collapsing LockBit and ALPHV ecosystems — accelerated further when **RansomHub (#030) went dark on April 1, 2025**, sending its entire affiliate base searching for a new platform. Qilin's 85% payout, stable Rust/Go encryptor, and mature infrastructure made it the obvious destination.

Qilin claimed approximately **958 victims in 2025** — 30% more than its nearest rival Akira — representing an estimated **14–18% of all ransomware attacks globally** that year. By 2026 it had not slowed down.

---

## Technical Profile — The Single-Controller Approach

Qilin's encryptor is written in **Go (Golang)** and supports cross-platform deployment across Windows, Linux, and VMware ESXi. The current variant analyzed by Thrive's CIRT is a **single Windows executable** that self-orchestrates all four attack phases:

```
PHASE 1 — Initial Execution (Anti-Forensics)
  Requires command-line password argument — defeats automated sandbox analysis
  Registers persistence under Windows Run key (embeds password in registry value for restart)
  Locks mutex to prevent duplicate execution

PHASE 2 — Environment Preparation
  Delete all Volume Shadow Copies (vssadmin delete shadows /all /quiet)
  Stop + permanently disable Volume Shadow Copy Service
  Enable outbound SMB connections (fsutil symlink)
  Start PowerShell loop — clears ALL Windows Event Logs continuously throughout execution

PHASE 3 — Encryption
  Hardware detection: AES-CTR mode (AES-NI CPU) OR ChaCha20 (non-AES-NI CPU)
  Symmetric key wrapped with embedded RSA public key — no recovery without attacker private key
  Unique per-victim file extension generated
  Ransom note dropped: README-RECOVER-[extension].txt in every affected directory

PHASE 4 — Lateral Movement (Concurrent dual-path)
  Windows path: Embedded PsExec resources → stage to admin shares → install service → execute laterally
  ESXi path: VMware.PowerCLI + Posh-SSH → authenticate vCenter → enumerate ESXi hosts →
             enable SSH → upload payload to /tmp/ → disable execInstalledOnly → encrypt hypervisor fleet
  No pre-installed tools required — everything embedded in controller
  Safe Mode support — reboot doesn't stop execution
```

### Why Traditional Defenses Miss It

| Capability | Conventional Ransomware | Qilin Controller |
|---|---|---|
| Detection surface | Custom malware triggers EDR signatures | Single controller; all subsequent actions use native/signed tools |
| VSS removal | Standalone script or binary | Embedded; runs via cmd.exe as legitimate admin activity |
| Lateral movement | Custom implant or manual credential reuse | Self-contained PsExec; no pre-installed tools needed |
| Hypervisor targeting | Separate ESXi payload, manual deployment | Autonomous vCenter auth → enumerate → upload → execute |
| Log clearing | Separate cleanup script | Embedded PowerShell loop running continuously throughout execution |

---

## EDR Killer Capability

A subset of Qilin affiliates deploy a dedicated **EDR killer** prior to the controller. This tool:

- Targets **300+ EDR drivers** for disabling
- Uses **BYOVD (Bring Your Own Vulnerable Driver)** technique — loads signed but vulnerable drivers to disable security tools from kernel level
- Confirmed vulnerable drivers abused: Toshiba power management driver, Carbon Black Cloud Sensor updater (`upd.exe`), Zemana Anti-Rootkit driver
- Additional EDR-killing tools observed: **EDRSandBlast**, **PCHunter**, **PowerTool**, **YDArk**
- Implements **geofencing** — skips systems with CIS-locale language settings (consistent with Russia-origin operator)

---

## Initial Access Methods

| Vector | Notes |
|---|---|
| **Stolen credentials (IAB)** | Primary — credentials purchased via Telegram, Breach Forums, other underground markets; 14.1% of victims had infostealer-linked domains |
| **Phishing** | Standard spearphishing campaigns |
| **VPN / network device vulnerabilities** | Exploits internet-facing VPN and remote access appliances |
| **Backup service vulnerabilities** | Specifically targets backup infrastructure |
| **Exposed web applications** | Opportunistic scanning for vulnerable web-facing apps |
| **Malvertising (SmokedHam)** | Affiliate UNC2465 delivers SmokedHam backdoor via Google ad malvertising posing as legitimate utilities (RVTools, Remote Desktop Manager); leads to Qilin deployment |

---

## Tools & Tradecraft (MITRE ATT&CK)

| Phase | Tool / Technique | ATT&CK |
|---|---|---|
| Discovery | Nmap, Nping | T1046, T1018 |
| Initial Access | Stolen credentials, phishing, VPN exploitation | T1078, T1566, T1190 |
| Persistence | Windows Run key (password embedded) | T1547.001 |
| C2 / Post-exploitation | Cobalt Strike, Evilginx, NetExec, SystemBC, Tofsee | T1071 |
| RMM / Persistence | NetSupport, ScreenConnect, Zoho Assist | T1219 |
| Defense Evasion | EDR killer (BYOVD) — EDRSandBlast, PCHunter, YDArk | T1562.001, T1068 |
| Defense Evasion | Geofenced payload (skips CIS locales) | T1480 |
| Credential Theft | Mimikatz, Kali Linux tools | T1003.001 |
| Lateral Movement | Embedded PsExec, WinRM, admin shares | T1021.002, T1569.002 |
| ESXi Compromise | VMware.PowerCLI, Posh-SSH, vCenter API | T1072 |
| Networking | Proxychains | T1090 |
| LOLBAS | fsutil, PsExec, WinRM, PowerShell | T1059.001 |
| Exfiltration | EasyUpload.io, MEGA | T1567.002 |
| Impact | AES-CTR/ChaCha20 encryption; VSS deletion; event log wipe | T1486, T1490, T1070.001 |

---

## Notable Victims

| Victim | Sector | Date | Impact |
|---|---|---|---|
| Synnovis (NHS pathology) | Healthcare | June 2024 | Multiple UK NHS hospitals disrupted; blood tests, surgeries, appointments delayed; personal data (NHS numbers, names, DOB) stolen |
| Asahi Group Holdings | Manufacturing / FMCG | September 2025 | 1.5M+ customer records exposed (names, DOB, addresses, phones); order processing and shipments disrupted |
| Lee Enterprises | Media | 2025 | Significant disruption to US newspaper operations |
| Seeing Machines | Aerospace-adjacent tech | March 2026 | Listed on leak site — driver monitoring AI company serving A&D sector |
| nPower Technologies | Energy / Power | March 2026 | Listed on leak site — power systems technology |
| 1,768 total on leak site | Multiple | 2022–2026 | As of April 30, 2026 |

### Sector Breakdown (all-time)
| Sector | Victims |
|---|---|
| Manufacturing | 274 |
| Business Services | 219 |
| Technology | 166 |
| Healthcare | 156 |
| Financial Services | 115 |

---

## Operational Volume

| Period | Victims | Notes |
|---|---|---|
| October 2025 | ~200+ | Peak month — single highest ever |
| Q4 2025 | 484 | Peak quarter |
| Q1 2026 | 342 | Jan 107 + Feb 104 + Mar 131 |
| April 2026 | 103 | 4th consecutive month above 100 |
| YTD 2026 (Jan–Apr) | 445 | #1 group all four months |
| Full year 2025 | ~958 | ~14–18% of all global ransomware attacks |
| All-time (Oct 2022–Apr 2026) | **1,768** | Per ransomware.live |

---

## Threat Connections

🔗 **Connects to Scoundrel #030 — RansomHub (DARK)**
The primary beneficiary of RansomHub's April 2025 shutdown. Qilin's victim volume doubled in the weeks after RansomHub went dark. Most of the RansomHub affiliate talent migrated here first.

🔗 **Connects to Scoundrel #015 — LockBit (DISRUPTED/rebuilding)**
Affiliate UNC2465 previously operated under LockBit before migrating through DarkSide → LockBit → Hunters International → Qilin. Qilin absorbed significant LockBit affiliate talent post-Operation Cronos.

🔗 **Connects to Scoundrel #020 — BlackCat/ALPHV (DISRUPTED)**
Indirect — talent flowed ALPHV → RansomHub → Qilin as each platform collapsed in sequence.

🔗 **Connects to Scoundrel #025 — Akira**
Primary rival. Qilin claimed ~30% more victims in 2025. Both grew from the same post-LockBit/ALPHV talent base. Akira and Qilin are competing for the same affiliate pool — when one stumbles (as Akira did in Q1 2026 after SonicWall vuln utility declined), Qilin benefits directly.

---

## Mitigations

| Priority | Action |
|---|---|
| 🔴 IMMEDIATE | **Block `vssadmin.exe delete shadows /all /quiet`** via ASR rules or application control — this command has no legitimate silent production use; Qilin runs it in Phase 2 |
| 🔴 IMMEDIATE | **Segment VMware management from Windows endpoints** — vCenter credentials must only be accessible from dedicated management hosts; an endpoint workstation having network path to vCenter is the Qilin ESXi kill switch |
| 🔴 HIGH | **Alert on PowerShell importing VMware.PowerCLI or Posh-SSH** on non-VMware-admin hosts — this is a high-fidelity indicator of the ESXi lateral movement phase |
| 🔴 HIGH | **Restrict PsExec and admin share execution** — audit which systems can run PsExec; any execution outside designated jump hosts triggers P1 |
| 🔴 HIGH | **Monitor Windows Run keys for `–password` arguments** — Qilin's persistence mechanism leaves a detectable artifact before execution |
| 🔴 HIGH | **Second EDR layer / BYOVD protection** — some affiliates deploy EDR killer using BYOVD; a WDAC policy blocking unsigned kernel drivers cuts this off |
| 🟡 HIGH | **Monitor for stolen credentials via infostealer logs** — 14.1% of Qilin victims had infostealer-domain correlation; credential monitoring services catch pre-attack indicators |
| 🟡 HIGH | **Block malvertising** — DNS filtering and browser policies blocking known malvertising domains; UNC2465 delivers via Google Ads posing as RVTools/RDM |
| 🟡 MEDIUM | **Offline/air-gapped backups** — Qilin specifically targets backup infrastructure; offline copies are the only reliable recovery path |
| 🟡 MEDIUM | **Hunt for BYOVD vulnerable drivers** — scan for presence of Toshiba power management driver, Carbon Black upd.exe, Zemana Anti-Rootkit driver in non-standard locations |

---

## A&D Relevance

**Indirect but real and growing.** Qilin's confirmed sector focus is manufacturing, business services, and technology — all with meaningful A&D supply chain overlap:

- **Manufacturing** is Qilin's #1 target sector (274 victims). A&D Tier 1–3 manufacturers are in scope.
- **Seeing Machines** (aerospace-adjacent — driver monitoring AI used in defense vehicles) appeared on the Qilin leak site in March 2026
- **nPower Technologies** (power systems technology) appeared on the leak site in March 2026
- The ESXi autonomous compromise capability is particularly dangerous for A&D environments running consolidated VMware infrastructure — a single controller can encrypt an entire virtual datacenter without touching individual VMs

---

## References

- [Thrive CIRT — The Quiet Weapon: Inside a Qilin Ransomware Attack (April 29, 2026)](https://thrivenextgen.com/the-quiet-weapon-inside-a-qilin-ransomware-attack/)
- [Orange Cyberdefense — SmokedHam, Qilin, a few Google ads and some bossware (April 15, 2026)](https://www.orangecyberdefense.com/dk/blog/cert-news/smoking-out-an-affiliate-smokedham-qilin-a-few-google-ads-and-some-bossware)
- [Ransomware.live — Qilin group data](https://www.ransomware.live/group/qilin)
- [Breachsense — March 2026 Ransomware Report](https://www.breachsense.com/ransomware-reports/march-2026/)
- [Breachsense — April 2026 Ransomware Report](https://www.breachsense.com/ransomware-reports/april-2026/)
- [Cisco Talos / GuidePoint — Ransomware Q1 2026 analysis](https://industrialcyber.co/reports/ransomware-reaches-elevated-new-normal-as-attack-volumes-hold-steady-into-2026-reshape-baseline-risk-expectations/)

---

*Profile created: 2026-05-01 | Author: C3PO | Admiralty Grade: A1 | TLP: WHITE*
