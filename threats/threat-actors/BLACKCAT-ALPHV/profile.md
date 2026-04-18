# BlackCat / ALPHV (Noberus)

> **Scoundrel #020** | C3PO Threat Actor Repository

---

## Identity

| Field | Detail |
|---|---|
| **Primary Alias** | BlackCat |
| **Other Names** | ALPHV, Noberus |
| **Type** | Cybercriminal (RaaS — Ransomware-as-a-Service) |
| **Origin** | Unknown; developers believed to be Russian-speaking; direct lineage from DarkSide/BlackMatter/REvil |
| **First Observed** | November 2021 |
| **Status** | ❌ Disrupted (FBI/DOJ takedown Dec 2023; group declared shutdown March 2024 after Change Healthcare exit scam) |
| **Threat Level** | 🔴 HIGH (historical; affiliate migration ongoing) |
| **MITRE ATT&CK Group** | G1016 |

---

## Overview

BlackCat/ALPHV emerged in late 2021 as a technically sophisticated RaaS operation — and immediately stood out for one reason: it was the **first major ransomware written in Rust**, a memory-safe systems language that enabled cross-platform encryption (Windows, Linux, VMware ESXi) and made static analysis substantially harder.

FBI intelligence established that many of BlackCat's developers and money launderers were **direct alumni of DarkSide and BlackMatter**, the groups behind the 2021 Colonial Pipeline attack. Some researchers regard BlackCat as a pragmatic rebranding of DarkSide, with REvil-linked affiliates folded in.

The group operated a highly professional criminal enterprise: a polished affiliate portal, negotiation teams, a publicly accessible (open-internet) data leak site (a first in the ransomware world), and a policy of accepting ransom payments in both **Bitcoin and Monero**. Affiliates retained 80–90% of ransom proceeds.

By May 2023, the group had hit **over 350 victims globally**. Their most consequential attack — **Change Healthcare (February 2024)** — crippled prescription drug processing across the US healthcare system, resulting in a **$22 million ransom payment**. Following that attack, an affiliate-developer payment dispute triggered what appears to have been an **exit scam**, with the group publicly announcing shutdown and effectively stealing the $22M ransom from their own affiliate.

As of early 2025, BlackCat/ALPHV has apparently dissolved — though former affiliates have likely migrated to other RaaS platforms.

---

## Targeting Profile

| Category | Detail |
|---|---|
| **Primary Sectors** | Healthcare, Financial Services, Critical Infrastructure, Manufacturing, Technology |
| **Secondary Sectors** | Energy, Business Services, Government, IT Services |
| **Geographic Focus** | United States (primary), Canada, Australia, United Kingdom, Germany |
| **Preferred Victims** | Large enterprises with sensitive data and low tolerance for downtime |
| **Ransom Range** | Several million USD; typically $1M–$20M+ |
| **Extortion Model** | Double extortion (encrypt + leak); sometimes triple (+ DDoS threat) |
| **Aerospace Relevance** | Moderate — targeted manufacturing, energy, and critical infrastructure adjacent to defense supply chain |

---

## TTPs (MITRE ATT&CK)

### Initial Access
| Technique | ID | Notes |
|---|---|---|
| Valid Accounts (compromised credentials) | T1078 | Primary vector; obtained via IABs or prior compromise |
| Exploit Public-Facing Application | T1190 | Exploited VPN vulnerabilities for network entry |
| Phishing for Information (vishing/smishing) | T1598 | Posed as helpdesk/IT staff via phone/SMS to steal credentials |
| Compromise Accounts | T1586 | Leveraged compromised accounts from external sources |
| External Remote Services | T1133 | Abused VPN and RDP for initial network access |

### Execution
| Technique | ID | Notes |
|---|---|---|
| PowerShell | T1059.001 | Deployed ransomware and disabled security tools |
| Windows Command Shell | T1059.003 | Batch scripts (start.bat, est.bat, run.bat) for deployment |
| Windows Management Instrumentation | T1047 | Network discovery and remote execution |
| Scheduled Task/Job | T1053 | Used Windows Task Scheduler to configure malicious GPOs |

### Persistence & Privilege Escalation
| Technique | ID | Notes |
|---|---|---|
| Access Token Manipulation | T1134 | Created "aadmin" user account; Kerberos token generation |
| Abuse Elevation Control Mechanism (UAC Bypass) | T1548 | Escalated to admin/SYSTEM level |
| Group Policy Modification | T1484.001 | Deployed ransomware and disabled defenses via GPO |
| Valid Accounts | T1078 | Maintained persistence via compromised domain admin accounts |

### Defense Evasion
| Technique | ID | Notes |
|---|---|---|
| Impair Defenses: Disable or Modify Tools | T1562.001 | Killed AV/EDR processes (POORTRY + STONESTOP drivers) |
| Indicator Removal: Clear Windows Event Logs | T1070.001 | Cleared logs after domain controller access |
| Obfuscated Files or Information | T1027 | Rust binary + junk code + encrypted strings for AV evasion |
| Masquerading | T1036 | Renamed files/tools to blend with legitimate processes |
| Virtualization/Sandbox Evasion | T1497 | Anti-analysis features in Rust binary |
| Allowlisting Bypass (Metasploit) | T1562 | Used allowlisted tools to evade detection |

### Credential Access
| Technique | ID | Notes |
|---|---|---|
| OS Credential Dumping: LSASS Memory | T1003.001 | Mimikatz and similar tools |
| Steal or Forge Kerberos Tickets | T1558 | Kerberos token generation for domain access |
| Adversary-in-the-Middle (Evilginx2) | T1557 | Intercepted MFA tokens and session cookies |
| Credentials from Password Stores | T1555 | Extracted passwords from domain controller, backups |
| Unsecured Credentials | T1552 | Harvested credentials from accessible locations |

### Lateral Movement
| Technique | ID | Notes |
|---|---|---|
| Remote Services: RDP | T1021.001 | Activated/exploited RDP across network |
| Remote Service Session Hijacking | T1563 | Used AnyDesk, Splashtop, TeamViewer under IT guise |
| Lateral Tool Transfer | T1570 | Deployed Cobalt Strike + Brute Ratel C4 beacons |
| Taint Shared Content | T1080 | Propagated via network shares |

### Exfiltration
| Technique | ID | Notes |
|---|---|---|
| Exfiltration Over Web Service: Cloud Storage | T1567.002 | Mega.nz, Dropbox used for staging/exfil |
| Archive Data | T1560 | 7-Zip/RAR compression before exfil |
| Exfiltration Over Alternative Protocol | T1048 | Exfil via SSH tunnels, non-standard channels |
| Automated Exfiltration | T1020 | ExMatter tool for automated data theft |
| Data Transfer Size Limits | T1030 | Split archives to avoid detection thresholds |

### Impact
| Technique | ID | Notes |
|---|---|---|
| Data Encrypted for Impact | T1486 | RSA+AES encryption; Rust binary with intermittent encryption mode |
| Inhibit System Recovery | T1490 | Shadow copy deletion (vssadmin), backup service termination |
| Service Stop | T1489 | Killed backup and AV services pre-encryption |
| Data Destruction | T1485 | Deleted victim data from systems post-exfiltration |
| Network Denial of Service | T1498 | Triple extortion: DDoS threat added to pressure victims |

---

## Tools & Infrastructure

| Category | Tool/Service |
|---|---|
| **Ransomware** | BlackCat/ALPHV payload (Rust-based; v1, v2, v3/Sphynx variants) |
| **C2 Frameworks** | Cobalt Strike, Brute Ratel C4 |
| **Credential Theft** | Mimikatz, Evilginx2 |
| **Remote Access** | AnyDesk, Splashtop, Megasync, Plink, Ngrok |
| **Exfiltration** | ExMatter, Mega.nz, Dropbox |
| **Propagation** | PowerShell scripts, batch scripts, PsExec, WMI |
| **Defense Kill** | POORTRY (malicious driver), STONESTOP |
| **Entry Point Malware** | Emotet botnet, Log4J Auto Expl |
| **Recon** | Network scanning (ipscan.ps1), WMI queries |
| **C2 Comms** | TOR, Tox, encrypted apps; victim-specific email |
| **Leak Infrastructure** | Open-internet data leak site (novel tactic); typosquatted victim site mirrors |

---

## Notable Incidents

| Date | Target | Impact | Notes |
|---|---|---|---|
| 2021–2022 | Multiple (60+ entities) | $M+ ransoms | Early RaaS expansion |
| Jun 2023 | Reddit | Data theft | 80GB stolen; threatened leak demanding API policy change |
| Nov 2023 | Henry Schein | Healthcare supply chain | Second attack after failed negotiation |
| Dec 2023 | FBI infrastructure seized | Group disrupted | Decryptor released; ~500 victims assisted |
| Feb 2024 | Change Healthcare (UnitedHealth Group) | $22M ransom paid; nationwide pharmacy disruption | Largest US healthcare cyber incident; exit scam followed |
| Mar 2024 | Group declared shutdown | Exit scam | Developers stole $22M affiliate payment; published source code sale claim |
| May 2024 | Hong Kong Consumer Council | Data breach | Last confirmed ALPHV-attributed incident |

---

## Law Enforcement Action

| Date | Action |
|---|---|
| Dec 19, 2023 | FBI seized BlackCat/ALPHV leak sites; released decryption tool; assisted ~500 victims |
| Feb 2024 | US State Dept offered $10M reward for ALPHV leadership identification + $5M for affiliate tips |
| Mar 2024 | Group announced shutdown following Change Healthcare exit scam |

**CISA Advisory:** AA23-353A (updated Feb 2024)  
**FBI FLASH:** CU-000167-MW (April 2022)

---

## Lineage & Connections

BlackCat/ALPHV sits at the center of a criminal genealogy spanning multiple generations of Russian-speaking ransomware operations:

```
GandCrab (2018–2019)
    └── REvil/Sodinokibi (2019–2021) [#016]
            └── [some members] ──────────────┐
DarkSide (2020–2021)                         │
    └── BlackMatter (2021)                   │
            └── [core developers] ────────── BlackCat/ALPHV (2021–2024) [#020]
                                                      │
                                            [affiliates migrated to]
                                         LockBit (#015), Cl0p (#018),
                                         RansomHub, Medusa, others
```

---

## Admiralty Scale Assessment

| Dimension | Grade | Notes |
|---|---|---|
| **Source Reliability** | A | FBI/CISA joint advisories, DOJ documentation |
| **Information Credibility** | 1 | Confirmed by law enforcement; multiple independent corroborations |
| **Overall Grade** | **A1** | Highest confidence |

---

## TLP Designation
**TLP:CLEAR** — Suitable for unrestricted distribution.

---

*Profile created: 2026-04-17 | Author: C3PO*  
*Sources: FBI FLASH CU-000167-MW, CISA AA23-353A, DOJ press releases, Wikipedia, ransomware.live*
