# Volt Typhoon — US Critical Infrastructure Pre-Positioning Campaign

> C3PO Threat Actor Repository | Campaign File  
> Status: 🔴 ACTIVE (Long-term, ongoing) | Last Updated: 2026-04-17

---

## Campaign Summary

| Field | Detail |
|---|---|
| **Campaign Name** | Volt Typhoon CI Pre-Positioning (C3PO designation) |
| **Threat Actor** | Volt Typhoon — see Scoundrel #008 |
| **Sponsor** | People's Republic of China (PLA) |
| **Campaign Start** | Estimated 2021 (earliest confirmed); activity documented through 2026 |
| **Status** | 🔴 ACTIVE — CISA/ODNI assess access maintained as of 2026 |
| **Primary Objective** | Pre-position for disruptive/destructive attacks against US CI in event of Taiwan conflict; intelligence collection |
| **Sectors Targeted** | Communications, Energy, Transportation, Water, Defense Industrial Base |
| **Admiralty Grade** | A1 — CISA advisory AA24-038A, ODNI Annual Threat Assessment 2026, FBI, NSA, Five Eyes |
| **TLP** | TLP:CLEAR |

---

## Strategic Context

Volt Typhoon is not conducting espionage for intelligence value. The campaign's documented objective — confirmed by CISA Advisory AA24-038A and the 2026 ODNI Annual Threat Assessment — is **strategic pre-positioning**: establishing persistent, dormant access inside US critical infrastructure that can be activated during a military crisis, specifically a Taiwan conflict scenario.

The 2026 ODNI report states: *"China is the most active and persistent cyber threat to the U.S. government, private-sector, and critical infrastructure networks... China has also shown its ability to compromise U.S. infrastructure through formidable cyber capabilities for both espionage and strategic advantage in the event of a conflict."*

Microsoft described this as infrastructure "pre-positioning for potential disruptions" — access intended to be activated, not exploited now. The target selection (communications nodes, energy grids, water treatment, ports) maps directly to systems that would be critical for US military logistics in a Pacific conflict.

---

## Campaign Characteristics

### What Makes This Campaign Distinct

1. **Living Off the Land (LOTL) exclusively** — Volt Typhoon uses only built-in Windows tools and legitimate admin software. No custom malware dropped on victim networks. This makes detection through traditional signature-based security nearly impossible.

2. **Targeting IT/OT convergence points** — The campaign focuses on where enterprise IT networks touch operational technology: SCADA interfaces, industrial control systems, communications backbones.

3. **Compromised SOHO routers as proxy layer** — Volt Typhoon routes all C2 traffic through a botnet of compromised small office/home office routers (Cisco, ASUS, Netgear, Fortinet) to obscure origin and blend with legitimate traffic. UK authorities publicly attributed a router-hijacking campaign to a Russian military-linked unit in April 2026; Volt Typhoon uses similar infrastructure laundering.

4. **Multi-year dwell time** — Some confirmed intrusions have been present for 5+ years. The group prioritizes persistence and stealth over immediate impact.

5. **Guam communications infrastructure specifically targeted** — CISA documented specific targeting of communications infrastructure in Guam, a critical US military hub for any Pacific operation.

---

## Targeted Sectors (Confirmed)

| Sector | Strategic Rationale |
|---|---|
| **Communications** | Disrupting US military and civilian comms in event of conflict |
| **Energy (Electric Grid)** | Civilian and military power disruption |
| **Transportation (Ports, Rail)** | Logistics disruption for military mobilization |
| **Water/Wastewater** | Civilian disruption creating domestic pressure |
| **Defense Industrial Base** | Pre-positioning in defense supply chain |
| **Guam specifically** | Key forward US military hub for Pacific operations |

---

## TTPs (MITRE ATT&CK)

| Tactic | Technique | ID | Notes |
|---|---|---|---|
| Initial Access | Exploit Public-Facing Application | T1190 | VPN appliances (Fortinet FortiGuard, Cisco, others) — exploits known CVEs |
| Initial Access | Valid Accounts | T1078 | Stolen credentials; LOTL authentication abuse |
| Persistence | Valid Accounts | T1078.003 | Long-term persistence via legitimate accounts; no malware dropped |
| Defense Evasion | LOTL — Living Off the Land | T1218 | Exclusively uses built-in Windows tools: wmic, ntdsutil, netsh, PowerShell |
| Defense Evasion | Proxy via Compromised Devices | T1090.003 | C2 traffic routed through botnet of SOHO routers (Cisco RV320, ASUS, Netgear, Fortinet) |
| Defense Evasion | Indicator Removal | T1070 | Clears logs to remove forensic evidence |
| Lateral Movement | Remote Services | T1021 | RDP and SMB with valid credentials |
| Lateral Movement | Pass the Hash | T1550.002 | Credential abuse for lateral movement |
| Collection | Data from Network Shared Drives | T1039 | Targets OT-adjacent data stores |
| Command & Control | Proxy | T1090 | Multi-hop routing through SOHO botnet to obscure PRC origin |
| Discovery | Network Service Discovery | T1046 | Extensive network mapping of IT/OT interfaces |
| Discovery | System Information Discovery | T1082 | Environment reconnaissance before lateral movement |
| Impact (Latent) | Pre-positioned for T1485/T1499 | — | Access established but not yet activated; intended for wartime disruption |

---

## SOHO Router Botnet (KV-Botnet)

CISA, FBI, and DOJ publicly named the **KV-Botnet** — a network of hundreds of compromised SOHO routers operated by Volt Typhoon as a proxy/C2 layer:

**Affected devices include:**
- Cisco RV320/RV325 routers
- ASUS RT-AX88U routers
- Netgear ProSafe routers
- Fortinet FortiGate firewalls (end-of-life)
- Ivanti Connect Secure appliances

> The DOJ disrupted the KV-Botnet in January 2024, but Volt Typhoon rebuilt equivalent infrastructure.

---

## CVEs Exploited for Initial Access

| CVE | Product | Notes |
|---|---|---|
| CVE-2022-40684 | Fortinet FortiGate | Authentication bypass — widely exploited for initial access |
| CVE-2023-27997 | Fortinet FortiGate SSL-VPN | Heap overflow; pre-auth RCE |
| CVE-2023-20198 | Cisco IOS XE | Privilege escalation; used to implant on network devices |
| CVE-2021-27860 | FatPipe MPVPN | Used in early campaign phases |
| Various Ivanti CVEs | Ivanti Connect Secure | Multiple exploited for initial VPN access |

---

## Government Advisories & Attribution

| Date | Document | Key Finding |
|---|---|---|
| May 2023 | CISA AA23-144A | First public attribution; Guam targeting confirmed |
| Feb 2024 | CISA AA24-038A | Expanded CI sector targeting confirmed; LOTL TTPs detailed |
| Jan 2024 | DOJ KV-Botnet disruption | Court-authorized disruption of SOHO router botnet |
| Mar 2026 | ODNI Annual Threat Assessment 2026 | China assessed as #1 cyber threat; Volt Typhoon pre-positioning confirmed ongoing |
| 2026 | Microsoft Threat Intelligence | "Pre-positioning for disruption" explicitly stated; IT/OT convergence targeting confirmed |

**Five Eyes Attribution:** US, UK, Canada, Australia, New Zealand all publicly attributed Volt Typhoon in joint advisories.

---

## Analyst Assessment

**Confidence:** HIGH (A1 — Five Eyes joint attribution, DOJ action, CISA advisories)

This is arguably the most strategically consequential cyber campaign in the repository. The combination of confirmed access, documented intent, and the geopolitical trigger condition (Taiwan conflict) makes this unique among tracked campaigns. Unlike most threat actors who activate access quickly for financial gain or immediate disruption, Volt Typhoon is deliberately **not using** the access it has. The access is the goal for now.

The 2026 ODNI assessment makes clear that US intelligence believes this access remains active. Microsoft's 2026 CI threat assessment confirmed the pattern continues — actors are embedding within IT/OT hybrid environments specifically because those are the hardest to detect and the most disruptive to activate.

**Assessment (WEP):** Volt Typhoon pre-positioning activity is **almost certainly** ongoing. Access to US critical infrastructure is **highly likely** being maintained and expanded. The campaign will **almost certainly** not be activated unless a Taiwan conflict or equivalent crisis occurs — at which point the dormant access becomes a wartime weapon.

---

## Connection to Repository

- 🔗 **Scoundrel #008 — Volt Typhoon** — this campaign is Volt Typhoon's primary operation
- 🔗 **Scoundrel #010 — Salt Typhoon** — parallel PRC campaign; Salt Typhoon focuses on telecom/espionage while Volt Typhoon focuses on CI pre-positioning for disruption
- 🔗 **Scoundrel #019 — APT41** — broader PRC cyber ecosystem; different objectives (financial + espionage vs. pure pre-positioning)

---

*Campaign file created: 2026-04-17 | Author: C3PO*  
*Sources: CISA AA24-038A, CISA AA23-144A, DOJ KV-Botnet press release (Jan 2024), ODNI Annual Threat Assessment 2026, Microsoft Threat Intelligence 2026, Netlas threat actor analysis*
