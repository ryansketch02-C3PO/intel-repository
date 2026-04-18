# Payouts King (PayoutsKing)

> **Scoundrel #021** | C3PO Threat Actor Repository

---

## Identity

| Field | Detail |
|---|---|
| **Primary Alias** | Payouts King / PayoutsKing |
| **Other Names** | GOLD ENCOUNTER (Sophos tracking designation) |
| **Type** | Cybercriminal (Independent ransomware operation — explicitly NOT RaaS) |
| **Origin** | Unknown; operators believed to be former Black Basta / Conti affiliates |
| **First Observed** | April 2025 (Zscaler); June/July 2025 (ransomware.live first victim) |
| **Status** | 🔴 ACTIVE (last victim: January 2026; TTP evolution observed into early 2026) |
| **Threat Level** | 🔴 HIGH |
| **Lineage** | Conti → Black Basta → Payouts King |

---

## Overview

Payouts King emerged in mid-2025, largely under the radar, as a direct successor operation to **Black Basta** — which itself was a successor to the notorious **Conti** ransomware gang. When Black Basta imploded in February 2025 after its internal chat logs were publicly leaked, its experienced affiliates and initial access brokers didn't retire. They regrouped. Some went to Cactus. Some went to Payouts King.

What sets Payouts King apart from most emerging ransomware groups is its **deliberate choice to avoid the RaaS model**. No affiliates, no franchise — the core operators conduct all attacks themselves. This means there's more operational consistency across incidents and less attribution noise, but it also suggests a tight-knit, experienced team that trusts no one outside their circle.

Technically, the group punches above its weight. Their ransomware binary features **4096-bit RSA + AES-256 CTR encryption** (OpenSSL statically linked), sophisticated **anti-analysis techniques** (stack-based string obfuscation, API hashing with unique seeds, direct syscalls to kill EDR), and a novel **anti-sandbox execution guard** that requires a specific command-line parameter to begin encryption. They targeted VMware and ESXi environments — a hallmark of sophisticated enterprise-focused ransomware operators.

Sophos linked the group to campaign **STAC4713** (active November 2025), which abused **QEMU** — an open-source virtualizer — to create hidden reverse SSH backdoors inside virtual machines, making C2 traffic invisible to host-based endpoint detection. As of February 2026, the group evolved away from QEMU and shifted tactics toward Cisco VPN exploitation and Microsoft Teams-based social engineering.

From June 2025 to January 2026, the group claimed **dozens of victims** across multiple industries and countries. Their leak site operates on Tor; negotiations run through **Tox** (peer-to-peer encrypted messaging), not a web portal.

---

## Targeting Profile

| Category | Detail |
|---|---|
| **Primary Sectors** | Business Services, Healthcare, Manufacturing, Technology, Finance |
| **Geographic Focus** | Multi-regional; victims observed across US, Europe, Australia |
| **Preferred Victims** | Large enterprises with high-value data; hypervisor/ESXi environments targeted |
| **Data Theft Scale** | Hundreds of GB to multiple TB per victim reported |
| **Extortion Model** | Double extortion (data theft first, then selective encryption, then leak threats) |
| **Ransom Model** | No public demand amounts; victim-specific negotiation via Tox |
| **Leak Site** | Tor .onion — initially redacts victim names, reveals full identity if unpaid |
| **Aerospace Relevance** | Low-to-moderate direct; manufacturing/defense supply chain exposure possible |

---

## TTPs (MITRE ATT&CK)

### Initial Access
| Technique | ID | Notes |
|---|---|---|
| Phishing: Spearphishing Attachment | T1566.001 | Malware-laden emails as initial lure |
| Valid Accounts (stolen RDP/VPN credentials) | T1078.002 | Exposed RDP/VPN services targeted with stolen credentials |
| Exploit Public-Facing Application | T1190 | Feb 2026: exploited exposed Cisco SSL VPN for access |
| Phishing: Spearphishing via Service (vishing) | T1566.004 | Spam bombing followed by IT impersonation call via Microsoft Teams |
| User Execution: Malicious File | T1204.002 | Victim tricked into running payload via Quick Assist remote session |

### Execution
| Technique | ID | Notes |
|---|---|---|
| PowerShell | T1059.001 | Ransomware distribution and AV/EDR disabling |
| Windows Command Shell | T1059.003 | Batch execution for ransomware deployment |
| Windows Management Instrumentation | T1047 | Remote execution and discovery |
| Scheduled Task / Job | T1053 | Persistence and delayed execution |
| System Services: Service Execution | T1569 | Ransomware distributed via SMB shares + PsExec |

### Persistence & Privilege Escalation
| Technique | ID | Notes |
|---|---|---|
| Valid Accounts | T1078 | Compromised domain admin accounts for persistence |
| Exploitation for Privilege Escalation | T1068 | Likely leveraging known AD/Windows exploits (consistent with BB lineage) |
| Modify Registry | T1112 | Registry changes to enable safe mode boot execution |
| Boot or Logon Autostart Execution | T1547 | Safe mode persistence via bcdedit modifications |

### Defense Evasion
| Technique | ID | Notes |
|---|---|---|
| Obfuscated Files or Information | T1027 | Stack-based string encryption; unique FNV1 hash seeds per string (defeats precomputed tables) |
| Virtualization/Sandbox Evasion | T1497 | Anti-sandbox guard: requires specific CLI parameter + CRC checksum match before encrypting |
| Impair Defenses: Disable or Modify Tools | T1562.001 | Direct syscall-based termination of 131 hardcoded AV/EDR processes; bypasses standard API hooks |
| Masquerading | T1036 | Tools renamed to blend with legitimate processes |
| Indicator Removal: Clear Windows Event Logs | T1070.001 | Wipes event logs post-encryption |
| Abuse Elevation Control Mechanism | T1548 | Safe mode boot to reduce security tool load |
| DLL Side-Loading | T1574.002 | Feb 2026: sideloaded Havoc C2 payload (vcruntime140_1.dll) via ADNotificationManager.exe |
| QEMU Abuse (virtualization evasion) | T1564 / T1059 | STAC4713 campaign: QEMU used as covert reverse SSH backdoor inside hidden VM; C2 traffic invisible to host EDR |

### Credential Access
| Technique | ID | Notes |
|---|---|---|
| OS Credential Dumping | T1003 | Domain credential harvesting post-initial access |
| Unsecured Credentials | T1552 | Exploitation of stored/reused credentials |

### Discovery & Lateral Movement
| Technique | ID | Notes |
|---|---|---|
| Network Service Discovery | T1046 | Internal reconnaissance post-compromise |
| Remote Services: RDP | T1021.001 | Lateral movement via stolen credentials |
| SMB/Windows Admin Shares | T1021.002 | Ransomware distributed to network shares |
| Remote Services: SMB/PsExec | T1021 | Remote execution of ransomware payload across domain |

### Exfiltration
| Technique | ID | Notes |
|---|---|---|
| Exfiltration Over C2 Channel | T1041 | Data staged and exfilled via Tor infrastructure |
| Automated Exfiltration | T1020 | Large-scale data theft (hundreds of GB to TB) before encryption |
| Transfer Data to Cloud Account | T1537 | Feb/Mar 2026: Rclone used to exfil data to attacker-controlled SFTP |
| Archive Collected Data | T1560 | Data compressed for exfiltration |

### Impact
| Technique | ID | Notes |
|---|---|---|
| Data Encrypted for Impact | T1486 | 4096-bit RSA + AES-256 CTR; partial encryption for large files (13-block model) — files appended with `.payoutsking` extension |
| Inhibit System Recovery | T1490 | Shadow copy deletion; bcdedit to force safe mode; targets Veeam and BackupExec specifically |
| Service Stop | T1489 | Backup services (Veeam, BackupExec) killed pre-encryption |
| Data Destruction | T1485 | Recycle bin wiped post-encryption |

---

## Tools & Infrastructure

| Category | Tool/Service |
|---|---|
| **Ransomware** | Custom binary; AES-256 CTR + 4096-bit RSA (OpenSSL statically linked); ChaCha20 support but unused |
| **C2 Framework** | Havoc C2 (Feb 2026 variant); QEMU reverse SSH backdoor (STAC4713, Nov 2025) |
| **Exfiltration** | Rclone (to SFTP); Tor-based .onion file servers |
| **Remote Access** | Quick Assist (Microsoft), Microsoft Teams (social engineering) |
| **Lateral Movement** | PsExec, RDP, SMB shares |
| **Credential Tools** | Likely Mimikatz (consistent with BB lineage) |
| **Backup Kill** | bcdedit (safe mode), vssadmin, Veeam/BackupExec service termination |
| **Evasion** | ADNotificationManager.exe (DLL sideload vector), QEMU |
| **Negotiation** | Tox peer-to-peer encrypted messaging |
| **Leak Site** | `payoutsgn7cy6uliwevdqspncjpfxpmzgirwl2au65la7rfs5x3qnbqd.onion` |

---

## Notable Incidents

| Date | Target | Impact | Notes |
|---|---|---|---|
| Jul 2025 | Unknown (first ransomware.live victim) | Data theft + encryption | Group's first publicly tracked victim |
| Nov–Dec 2025 | Multiple (STAC4713 campaign) | QEMU-assisted C2; data theft | Sophos identified QEMU abuse in this campaign |
| Dec 16–25, 2025 | 8 victims | Active campaign | High operational tempo through end of 2025 |
| Jan 14, 2026 | Peugeot Motocycles (France) | Data theft; leak threatened | Last confirmed ransomware.live victim |
| Feb 2026 | Unknown org | Cisco SSL VPN breach | TTP shift: abandoned QEMU, used Havoc C2 + Rclone |
| Mar 2026 | Unknown org | Teams social engineering | Spam bombing + IT impersonation + Quick Assist |

---

## Lineage & Criminal Genealogy

```
Conti (2020–2022) ──[dissolved May 2022; members dispersed]──┐
                                                               ▼
                                          Black Basta (2022–2025)
                                          [disbanded Feb 2025 after chat log leak]
                                                               │
                              ┌────────────────────────────────┤
                              ▼                                ▼
                         Cactus ransomware              Payouts King (2025–present) [#021]
                         (some affiliates)              (GOLD ENCOUNTER; no RaaS model)
```

**Note:** Payouts King operators explicitly state they do not use affiliates. This is operationally consistent — attack TTPs are highly uniform across incidents, unlike typical RaaS affiliate variance.

---

## Admiralty Scale Assessment

| Dimension | Grade | Notes |
|---|---|---|
| **Source Reliability** | B | Zscaler ThreatLabz, Sophos — credible commercial threat intel; no government advisory yet |
| **Information Credibility** | 2 | Confirmed by multiple independent researchers; no government attribution as of Apr 2026 |
| **Overall Grade** | **B2** | High confidence; inclusion threshold met |

---

## TLP Designation
**TLP:CLEAR** — Suitable for unrestricted distribution.

---

*Profile created: 2026-04-17 | Author: C3PO*  
*Sources: Zscaler ThreatLabz (Apr 16 2026), Sophos STAC4713 report, RedPiranha Threat Intel Dec 2025, BlackFog, ransomware.live, CybersecurityNews*
