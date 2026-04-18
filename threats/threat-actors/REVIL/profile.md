# REvil (Sodinokibi)

## Identity

| Field | Details |
|---|---|
| **Primary Alias** | REvil |
| **Other Names** | Sodinokibi, Sodin, GOLD SOUTHFIELD, Pinchy Spider |
| **Type** | Cybercriminal Group — Ransomware-as-a-Service (RaaS) |
| **Origin** | Russia (Russian-speaking; CIS-aligned) |
| **Lead Admin Alias** | Unknown / UNKN |
| **Lineage** | Direct successor to GandCrab ransomware (same core operators) |
| **First Observed** | April 2019 |
| **Status** | ❌ Disrupted — key arrests 2021–2022; dark web infrastructure taken down; currently inactive as a brand |
| **Peak Activity** | 2019–2021 |
| **Motivation** | Financial — ransomware, double extortion, big-game hunting |
| **Threat Level** | 🟡 MEDIUM (disrupted, but members dispersed into other RaaS ops) |
| **Admiralty Grade** | A2 (historical record solid; attribution confirmed by DOJ indictments) |

---

## Overview

REvil — short for "Ransomware Evil" — was one of the most prolific and dangerous ransomware operations in history. At its peak (2020–2021), it was responsible for 22% of all IBM incident response engagements and deployed ransomware against an estimated 175,000+ computers worldwide. The group pioneered "big-game hunting" — targeting large enterprises for multi-million dollar ransoms — and is credited with making double extortion (encrypt + leak) the industry standard.

REvil was not just a tool. It was a business. It operated on a franchise model that let affiliates rent the ransomware, collect ransoms, and pay a cut back to the core developers. At the time of its disruption, it had collected over $200 million in confirmed ransom payments.

---

## Key Figures

| Alias | Real Name | Status |
|---|---|---|
| **Rabotnik** / Vasinskyi | Yaroslav Vasinskyi (Ukrainian) | ✅ Arrested (Poland, 2021), extradited to US, **sentenced 13 years 7 months** (2024), ordered to pay $16M restitution |
| **Unknown / UNKN** | Yevgeniy Polyanin (Russian) | Indicted; $6.1M seized; believed to remain abroad |
| **Bassterlord** | Ivan Kondratyev | Indicted 2022; also linked to **LockBit** affiliate ops |
| Multiple others | 2 arrested in Romania (Nov 2021); 7 arrested in Russia (Jan 2022) |

---

## Lineage: GandCrab → REvil

REvil didn't appear from nowhere. It evolved directly from **GandCrab**, one of the most profitable ransomware operations in the world before REvil:
- GandCrab launched January 2018; hit ~50% of global ransomware market at peak
- April 2019: REvil and GandCrab deployed in the same attack for the first time
- May 2019: GandCrab "retires" (claimed $2B in earnings)
- June 2019: REvil appears on Russian cybercrime forums; operator "Unknown" admits: *"We used to be affiliates of GandCrab. We bought the source code and started our own business."*
- Code analysis by Cisco, Secureworks, and Brian Krebs confirmed shared codebase and likely same operators

---

## Notable Attacks

| Date | Target | Impact |
|---|---|---|
| 2020 | **Travelex (FX exchange)** | $6M ransom; operational disruption across global banks |
| Mar 2021 | **Acer** | $50M ransom demand (largest at the time); financial data leaked |
| May 2021 | **JBS USA** (meat supplier) | $11M paid; ~20% of US beef supply disrupted; FBI attribution |
| Jun 2021 | **Fujifilm** | Operational disruption; reportedly declined to pay |
| **Jul 2021** | **Kaseya VSA** (supply chain) | Zero-day CVE-2021-30116; 1,500+ downstream businesses hit via MSP chain; $70M demand |

The **Kaseya attack** is the defining incident — a single zero-day exploit that cascaded through managed service providers to hit over 1,500 companies simultaneously. It's one of the most impactful supply chain ransomware attacks in history.

---

## TTPs (MITRE ATT&CK)

### Initial Access
| Technique | ID | Notes |
|---|---|---|
| Exploit Public-Facing Application | T1190 | Oracle WebLogic (CVE-2019-2725), Kaseya VSA (CVE-2021-30116), Citrix (CVE-2019-19781) |
| Phishing | T1566 | Macro-laden Office docs, compressed JavaScript, HTML smuggling |
| Valid Accounts | T1078 | Stolen credentials via RDP brute force; purchased from IABs |
| Supply Chain Compromise | T1195 | MSP pivot — compromised Kaseya to reach 1,500+ organizations |

### Execution & Persistence
| Technique | ID | Notes |
|---|---|---|
| PowerShell | T1059.001 | Obfuscated scripts; used to decode and execute payloads |
| Scheduled Task | T1053.005 | Persistence and payload triggering |
| Masquerading | T1036 | Disguised ransomware as legitimate processes (e.g., msmpeng.exe) |
| Registry Modification | T1112 | Stores session encryption keys in `Software\recfg` |

### Privilege Escalation & Defense Evasion
| Technique | ID | Notes |
|---|---|---|
| Exploitation for Privilege Escalation | T1068 | CVE-2018-8453 (Windows win32k LPE), CVE-2020-1472 ZeroLogon |
| Process Doppelgänging | T1055.013 | In-memory evasion |
| Impair Defenses | T1562 | Disables Windows Defender, firewall, VSS |
| Virtualization/Sandbox Evasion | T1497 | Anti-debug, CRC32 API hashing, Russian keyboard language check |

### Lateral Movement & Discovery
| Technique | ID | Notes |
|---|---|---|
| Remote Services: RDP | T1021.001 | PuTTY Link (Plink) used to tunnel RDP sessions |
| SMB/Network Shares | T1021.002 | Lateral movement to file shares |
| Cobalt Strike | S0154 | Primary post-exploitation framework for lateral movement |
| Remote Services: SMB | T1021 | Internal network scans: 10.0.x.x ranges, ports 80/443/3389/22 |

### Exfiltration
| Technique | ID | Notes |
|---|---|---|
| Exfiltration to Cloud | T1567 | AnyDesk, NetSupport Manager; data staged to cloud storage |
| Data Encrypted for Impact | T1486 | Salsa20 + Curve25519 ECDH hybrid; unique key per file |

### Impact
| Technique | ID | Notes |
|---|---|---|
| Data Encrypted for Impact | T1486 | Fast multithreaded encryption via IOCPs |
| Inhibit System Recovery | T1490 | Deletes VSS shadow copies; wipes backup folders |
| Financial Theft | T1657 | Double extortion; "Happy Blog" leak site for public shaming |
| Network Denial of Service | T1498 | DDoS pressure applied to victims refusing payment |

---

## Technical Profile

**Encryption:** Salsa20 (files) + Elliptic Curve Diffie-Hellman Curve25519 (key exchange) + AES + SHA-3 for key management. Ransomware verifies no Russian keyboard layout before executing (protects CIS nations).

**Ransom Note:** Dropped in every encrypted directory; includes Tor portal URL, unique victim ID (UID), and base64 KEY. Victims receive 30-day window before data is published.

**Exfil Tool:** StealBit (custom) + Rclone + cloud storage services. Also targeted Exchange servers, NAS devices, and consolidated backup systems for maximum leverage.

**Registry Artifact:** `HKCU\Software\recfg` — stores session keypairs. Presence is a high-confidence IOC.

**C2:** HTTPS (TOR) to hard-coded dmn-field domains. C2 traffic is encrypted, complicating detection.

**Geofencing:** Skips systems with CIS language keyboard layouts (Russia, Ukraine, etc.) — common Russian criminal opsec.

---

## RaaS Structure

- Core developers maintained malware, payment portals, leak site ("Happy Blog"), and negotiation support
- Affiliates received **60–70% of ransom payments**
- Affiliates identified by unique `pid` and `sub` config fields embedded in each sample
- Business-like operations: ran hacking contests offering cash prizes and employment
- Discussed notifying stock exchanges after compromises to increase financial pressure on victims
- Shifted from Bitcoin to **Monero** in 2020 for transaction privacy

---

## Timeline

| Date | Event |
|---|---|
| Apr 2019 | First REvil deployment alongside GandCrab |
| May 2019 | GandCrab "retires"; REvil launches exclusively |
| Jun 2019 | REvil advertised on Russian cybercrime forums by "Unknown/UNKN" |
| 2020 | Travelex attack; shift to Monero; double extortion via Happy Blog |
| Mar 2021 | Acer — $50M demand |
| May 2021 | JBS attack — $11M paid; FBI attributes to REvil |
| Jul 2021 | **Kaseya VSA supply chain attack** — 1,500+ victims; $70M demand |
| Jul 2021 | REvil vanishes from dark web (likely pressure from US/Russia bilateral talks) |
| Sep 2021 | REvil reappears briefly |
| Nov 2021 | DOJ indicts Vasinskyi + Polyanin; $6.1M seized; REvil infrastructure decryption keys obtained |
| Jan 2022 | Russia arrests 14 REvil members in coordinated sweep (rare Russian cooperation) |
| Mar 2022 | Vasinskyi extradited to US from Poland |
| May 2024 | Vasinskyi **sentenced 13 years 7 months**; ordered to pay $16M restitution |
| Post-2022 | Core members dispersed into LockBit, Conti successor groups, and other RaaS operations |

---

## Connections

- **LockBit (#015):** Ivan Kondratyev ("Bassterlord") operated as both a REvil affiliate *and* a LockBit affiliate — same person, two franchises, spanning the ransomware golden age
- **Scattered Spider (#013):** Post-ALPHV collapse, Scattered Spider-adjacent operators sought new RaaS partners; REvil's affiliate network was a talent pool that fed LockBit and others
- **GandCrab (predecessor):** Same core operators; REvil is a direct continuation
- **BlackMatter / ALPHV:** REvil's disappearance in 2021 created an affiliate vacuum that BlackMatter (and later ALPHV/BlackCat) filled — former REvil affiliates migrated

---

## Targeting Profile

- **Primary sectors:** Manufacturing, food/agriculture, technology, legal, financial services, MSPs
- **Geography:** ~60% US-based victims; also EU, Australia, Canada, Japan
- **Excluded:** CIS nations (Russia, former Soviet states), Syria — hard-coded in malware
- **Model:** "Big-game hunting" — prioritized large organizations with $1M+ ransom capacity; no ethics rules on healthcare or critical infrastructure

---

## Sources

- DOJ Indictment: Yaroslav Vasinskyi (Nov 2021) — justice.gov
- DOJ Sentencing: Vasinskyi sentenced 13y7m (May 2024) — justice.gov
- CISA Advisory AA21-131A
- Secureworks CTU: GOLD SOUTHFIELD / REvil Sodinokibi analysis
- HHS HC3 Advisory: REvil/Sodinokibi vs. Health Sector (Aug 2021)
- Intel 471: REvil RaaS affiliate operation analysis
- Unit 42: Kaseya VSA Threat Brief (Jul 2021)
- FBI/ODNI: Kaseya VSA Supply Chain Attack briefing

---

*Profile by C3PO 🤖 | Grade: A2 | TLP: WHITE | Created: 2026-04-12*
