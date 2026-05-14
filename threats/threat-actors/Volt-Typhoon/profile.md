# Volt Typhoon — Threat Actor Profile

## Identity

| Field | Value |
|---|---|
| **Primary Name** | Volt Typhoon |
| **Designations** | BRONZE SILHOUETTE, Vanguard Panda, Dev-0391, Storm-0391, UNC3236, VOLTZITE, Insidious Taurus, Redfly, TAG-87 |
| **Attribution** | People's Liberation Army Cyberspace Force (PLAC) — People's Republic of China |
| **Active Since** | ~mid-2021 (earliest confirmed; likely earlier) |
| **Status** | 🔴 Active |
| **Motivation** | Strategic pre-positioning · Disruption/Sabotage (wartime trigger) · Espionage |
| **Sector Focus** | Critical Infrastructure (Energy, Water, Communications, Transportation), Military Logistics, OT/ICS Systems |
| **Admiralty Grade** | A1 |
| **TLP** | TLP:WHITE |
| **Last Updated** | 2026-04-05 |

---

## Executive Summary

Volt Typhoon is China's most strategically dangerous cyber actor — not because of what they've done, but because of what they're positioned *to* do. Attributed with high confidence to China's People's Liberation Army Cyberspace Force, Volt Typhoon has spent years quietly burrowing into US critical infrastructure — power grids, water systems, communications networks, military logistics — and then doing almost nothing. No data theft. No ransomware. Just waiting.

FBI Director Christopher Wray put it plainly in January 2024: *"China's hackers are positioning on American infrastructure in preparation to wreak havoc and cause real-world harm to American citizens and communities, if or when China decides the time has come to strike."*

The scenario DoD planners lose sleep over: China initiates military action against Taiwan, and simultaneously activates Volt Typhoon's pre-positioned access — triggering blackouts, water outages, and communications disruptions across the continental US to slow American military response and erode public support for intervention.

As of February 2026, CISA has noted intensified Volt Typhoon activity since mid-2025, characterized as consistent with "pre-conflict positioning." They are confirmed to have maintained persistent access inside some victim networks for **at least five years** without detection.

The ghost in the machine isn't coming. In many cases, it's already there.

---

## Targeting Profile

**Primary sectors:**
- ⚡ **Energy / Power Grid** — electric utilities, including grid operational technology networks
- 💧 **Water & Wastewater** — SCADA/ICS systems at treatment facilities; confirmed access to water well controls and substations
- 📡 **Communications** — commercial satellite providers, ISPs, telecommunications
- 🚢 **Transportation & Maritime** — port authorities, military logistics contractors
- 🪖 **Defense Industrial Base** — military logistics, Guam-based naval/air infrastructure (critical for Taiwan contingency)
- 🏭 **Manufacturing** — critical manufacturing supply chains

**Primary geographic focus:**
- 🇺🇸 United States (continental + Guam/territories — Taiwan contingency focus)
- 🇦🇺 Australia (confirmed probing per ASIO, Nov 2025)
- Five Eyes partner nations broadly

**Why Guam specifically:** US naval ports and air bases on Guam would be the centerpiece of any American military response to a Chinese invasion or blockade of Taiwan. Pre-positioning there is not coincidence — it's war planning.

---

## Notable Operations & Campaigns

### 2021–Present — Operation: Invisible Foothold
The ongoing, multiyear campaign targeting US critical infrastructure. Volt Typhoon gains access via edge device exploitation, establishes persistent footholds using only native OS tools and stolen credentials, then goes quiet — periodically re-accessing to refresh credentials and validate access. In multiple confirmed cases, they extracted the Active Directory NTDS.dit file (full domain compromise) **multiple times from the same victim over years** to ensure continued access.

### 2023 — Guam Exposure
Microsoft publicly attributed Volt Typhoon activity in May 2023, flagging infiltration of critical infrastructure organizations in Guam and across the US. The New York Times noted the targeting of Guam's naval and air facilities — the forward staging point for a Taiwan response.

### 2024 — KV-Botnet Disruption
FBI disrupted the **KV-Botnet** (Jan 31, 2024) — a Volt Typhoon-operated network of compromised end-of-life SOHO routers (Cisco RV320/325, NETGEAR ProSafe, others) used to proxy C2 traffic and blend into local ISP traffic. Within 37 days of the disruption, Volt Typhoon had compromised ~30% of available Cisco RV320/325 devices again.

### 2024 — CISA Advisory AA24-038A
Five Eyes joint advisory confirmed Volt Typhoon had maintained persistent access in some victim IT environments for **at least 5 years**, with pre-positioning into OT networks confirmed. Victims included water utilities with access to treatment systems, well controls, and HVAC systems in server rooms.

### 2025-2026 — Intensification
CISA supplementary advisory (Feb 2026) flagged increased Volt Typhoon activity since mid-2025 with new IOCs in water and communications sectors, assessed as "pre-conflict positioning" amid deteriorating US-China tensions.

---

## Tactics, Techniques & Procedures (TTPs)

### The Living Off the Land (LOTL) Doctrine
Volt Typhoon's defining characteristic: **zero custom malware for most operations**. They use only tools that already exist on the target system — Windows-native utilities — making signature-based detection nearly useless.

Native tools observed in use:
- `wmic` — system info gathering, remote execution
- `ntdsutil` — NTDS.dit extraction (domain credential dump)
- `netsh` — PortProxy configuration for C2 relay
- `PowerShell` — encoded command execution
- `certutil`, `mshta`, `regsvr32`, `rundll32` — LOLBin abuse
- `net.exe`, `ipconfig`, `whoami` — discovery
- `PSExec` — remote process execution
- `Advanced IP Scanner` — network discovery (legitimate admin tool co-opted)
- `AD Explorer` — Active Directory mapping

### Kill Chain Pattern

**1. Reconnaissance**
- Pre-compromise OSINT via FOFA, Shodan, Censys for exposed infrastructure
- Targeting of IT admin personal emails post-compromise
- Collection of network diagrams and OT equipment manuals (pre-positioning intelligence)

**2. Initial Access** `[T1190]`
- Exploitation of internet-facing edge devices: FortiGate, Ivanti Connect Secure, Citrix ADC, NETGEAR, Cisco appliances
- Exploitation of CVE-2022-42475 (Fortinet SSL-VPN buffer overflow) confirmed in one compromise
- Valid account abuse via stolen credentials
- Operations timed to business hours to blend with legitimate traffic

**3. Persistence** `[T1078]`
- Relies almost exclusively on valid stolen credentials — no traditional backdoor implants
- VPN sessions for persistent encrypted access `[T1133]`
- PortProxy registry modifications to relay C2 traffic `[T1090.001, T1112]`
- Periodic re-access and NTDS.dit re-extraction to stay current on credentials

**4. Privilege Escalation & Credential Access** `[T1003.003]`
- NTDS.dit extraction via Volume Shadow Copy — full domain compromise
- Mimikatz for credential dumping `[T1003]`
- Impacket for credential manipulation and protocol abuse
- Browser credential theft (Chrome, Edge User Data directories) `[T1555.003]`
- Offline password cracking of NTDS.dit hashes

**5. Defense Evasion** `[TA0005]`
- LOTL — all activity indistinguishable from legitimate admin behavior
- Malicious traffic proxied through compromised SOHO routers (KV-Botnet) to appear as local ISP traffic `[T1090.002]`
- Targeted log deletion, including Windows Event Log clearing (Event ID 1102) `[T1070.001]`
- Custom Fast Reverse Proxy (FRP) with hardcoded C2 callbacks obfuscated as legitimate processes

**6. Lateral Movement** `[TA0008]`
- RDP to Domain Controller using compromised domain admin credentials `[T1021.001]`
- Pass the Hash / Pass the Ticket `[T1550]`
- Pivot from IT to OT network segments — the defining strategic characteristic

**7. Discovery** `[TA0007]`
- Network scanning via Advanced IP Scanner, `netsh`, WMIC
- AD mapping via AD Explorer
- OT topology collection: PLC access paths, SCADA credentials, network diagrams

**8. Collection & Exfiltration** `[TA0009, TA0010]`
- OT equipment manuals, network diagrams, PuTTY profiles (containing OT system credentials)
- Files zipped using `rar.exe` (renamed as `ronf.exe`) and exfiltrated via SMB `[T1048, T1560]`
- WMIC used to stage files in temp directories before exfil

**9. Command & Control** `[TA0011]`
- KV-Botnet: compromised SOHO routers as C2 relay nodes
- Virtual Private Servers (VPS) as proxy infrastructure
- Custom FRP client with C2 callbacks to ports: **8080, 8443, 8043, 8000, 10443**
- Known FRP disguise filenames: `cisco_up.exe`, `cl64.exe`, `vm3dservice.exe`, `watchdogd.exe`, `Win.exe`, `WmiPreSV.exe`, `WmiPrvSE.exe`
- Earthworm tunneling tool

---

## MITRE ATT&CK Mapping

| Tactic | Technique | ID |
|---|---|---|
| Reconnaissance | Active Scanning / OSINT | T1590, T1591, T1589 |
| Resource Development | Acquire Infrastructure (VPS) | T1583.003 |
| Resource Development | Compromise Infrastructure (Botnet) | T1584.005 |
| Initial Access | Exploit Public-Facing Application | T1190 |
| Initial Access | External Remote Services | T1133 |
| Persistence | Valid Accounts | T1078 |
| Defense Evasion | LOTL / System Binary Proxy Execution | T1218 |
| Defense Evasion | Indicator Removal (Log Clearing) | T1070.001 |
| Defense Evasion | Proxy (Multi-hop) | T1090.002, T1090.003 |
| Credential Access | OS Credential Dumping (NTDS) | T1003.003 |
| Credential Access | Credentials from Web Browsers | T1555.003 |
| Discovery | Network Service Discovery | T1046 |
| Discovery | Remote System Discovery | T1018 |
| Lateral Movement | Remote Desktop Protocol | T1021.001 |
| Lateral Movement | Pass the Hash | T1550.002 |
| Collection | Archive Collected Data | T1560 |
| Exfiltration | Exfiltration Over Alternative Protocol | T1048 |
| C2 | Proxy (Port Knocking/PortProxy) | T1090.001 |
| C2 | Protocol Tunneling | T1572 |

---

## Infrastructure

- **KV-Botnet:** Compromised Cisco RV320/325, NETGEAR ProSafe routers (EOL devices) — primary C2 relay infrastructure, disrupted Jan 2024, rebuilt within 37 days
- **VPS infrastructure:** Leased virtual private servers as additional proxy hops
- **FRP (Fast Reverse Proxy):** Custom-compiled versions with hardcoded C2 callbacks, disguised as legitimate Windows processes
- **Earthworm:** Open-source network tunneling tool (customized versions)
- **PRTG Server Abuse:** Exploited Paessler Router Traffic Grapher servers as FRP intermediaries via PortProxy registry modification

---

## Observed CVEs

| CVE | Affected Product | CVSS | Notes |
|---|---|---|---|
| CVE-2022-42475 | Fortinet FortiGate SSL-VPN | 9.3 Critical | Buffer overflow; confirmed initial access vector |
| CVE-2021-40539 | ManageEngine ADSelfService Plus | 9.8 Critical | Confirmed exploitation |
| CVE-2021-27860 | FatPipe WARP/MPVPN | 9.8 Critical | Confirmed exploitation |
| CVE-2023-27350 | PaperCut MF/NG | 9.8 Critical | Associated exploitation activity |
| Various | Ivanti Connect Secure | Variable | Ongoing exploitation pattern |
| Various | Citrix ADC / Gateway | Variable | Ongoing exploitation pattern |
| Various | NETGEAR SOHO routers | Variable | KV-Botnet recruitment |

---

## Defensive Recommendations

1. **Patch internet-facing devices immediately** — FortiGate, Cisco, NETGEAR, Citrix, Ivanti are priority targets
2. **Audit for LOTL indicators** — abnormal ntdsutil on non-DCs, unusual WMI subscriptions, PowerShell to external IPs, LOLBin abuse (certutil, mshta) in unusual contexts
3. **Hard IT/OT segmentation** — physical or logical air gaps with strict allowlist firewall rules between IT and OT networks
4. **Monitor for credential abuse** — anomalous login hours, unusual source IPs for service accounts, PAM/PIM solutions
5. **Centralized tamper-proof logging** — Volt Typhoon clears Event ID 1102; logs must be forwarded to hardened SIEM off the compromised host
6. **Implement phishing-resistant MFA** — valid credential abuse is the primary persistence mechanism
7. **EOL device inventory and replacement** — SOHO/edge devices no longer receiving security updates are KV-Botnet recruitment targets
8. **Network behavioral analytics** — LOTL makes signature detection useless; behavioral baselines are the only reliable detection surface

---

## Intelligence Assessment

**Source Reliability:** A (Completely Reliable — CISA, NSA, FBI, Five Eyes attribution)
**Information Credibility:** 1 (Confirmed by multiple independent sources and direct IR activity)
**Admiralty Grade:** A1

**WEP Language:** We assess with high confidence that Volt Typhoon **will** activate pre-positioned access against US critical infrastructure in the event of a Taiwan Strait military crisis. Activity observed in 2025-2026 is consistent with transition from reconnaissance to readiness posture.

**Aerospace/Defense relevance:** Volt Typhoon's targeting of military logistics contractors, port authorities, and Guam infrastructure directly threatens aerospace and defense operations. Defense manufacturers and contractors supplying Taiwan-contingency military assets should treat Volt Typhoon as a Tier 1 threat.

---

## References

- CISA Advisory AA24-038A: https://www.cisa.gov/news-events/cybersecurity-advisories/aa24-038a
- CISA Advisory AA23-144A (original): https://media.defense.gov/2023/May/24/2003229517/-1/-1/0/CSA_Living_off_the_Land.PDF
- DOJ KV-Botnet disruption: https://www.justice.gov/opa/pr/us-government-disrupts-botnet-peoples-republic-china-used-conceal-hacking-critical
- Microsoft Volt Typhoon attribution (May 2023): https://www.microsoft.com/en-us/security/blog/2023/05/24/volt-typhoon-targets-us-critical-infrastructure-with-living-off-the-land-techniques/
- IISS analysis (Feb 2026): https://industrialcyber.co/industrial-cyber-attacks/iiss-notes-volt-typhoons-targeting-of-us-infrastructure-signals-disruptive-intent-beyond-espionage/
- MITRE ATT&CK G1017: https://attack.mitre.org/groups/G1017/

---

*Last Updated: 2026-05-14 | Profile by C3PO*

---

## 📡 Intelligence Update — 2026-05-14

### CISA CI Fortify Initiative — Direct Response to Volt Typhoon / Salt Typhoon Threat

CISA launched **CI Fortify**, a new initiative specifically designed to prepare critical infrastructure operators for **weeks-to-months of isolated operation** in the event of a Volt Typhoon or Salt Typhoon attack that forces IT/OT disconnection. Announced in early May 2026:

- CISA will conduct **targeted technical assessments** of critical infrastructure entities to develop survivability plans
- Goal: enable **safe service delivery after disconnection from IT networks, third-party vendors, and telecom providers**
- CISA Acting Director Nick Anderson: "The goal is service delivery that can still reach critical infrastructure after the asset owner has disconnected from IT, OT, third-party vendors, service provider connections, and third-party telecommunications equipment."
- Initial pilot engagements already underway; CISA plans to scale as additional staff are hired

**Significance:** CI Fortify is the U.S. government's formal acknowledgment that Volt Typhoon's pre-positioned access is deep enough to require infrastructure operators to plan for operating in a fully disconnected state. This is a doctrinal shift from "patch and detect" to "survive the attack."

### Expanded OT/Sector Targeting Confirmed — Oil & Gas

Multiple independent sources (including CyberWarCon 2025 analysis republished May 8, 2026) confirm that Volt Typhoon has **expanded beyond its originally documented electric/comms/water portfolio** into the **oil and gas sector**. Confirmed 2025–2026 targeting expansion:

- **Electric utility co-ops** (opportunistic broadening beyond strategic locations like Guam and West Coast ports)
- **Oil and gas infrastructure** (pipelines, processing facilities, distribution)
- Continued pre-positioning in **port authorities and maritime logistics nodes** linked to Taiwan contingency

**Assessment:** Volt Typhoon's expanding targeting scope lowers the barrier to entry in their model — shifting from highly specific strategic pre-positioning to broader "access banking" across critical infrastructure. A wider foothold provides more options for coordinated disruption timing.

### Trump-Xi Beijing Summit Context (May 14–15, 2026)

Volt Typhoon, Salt Typhoon, and Shadow-Earth-053 were **conspicuously absent from US pre-summit public readouts** ahead of the Trump-Xi Beijing summit (May 14–15, 2026), assessed as deliberate de-escalation framing to avoid complicating trade negotiations. This does **not** indicate reduced operational activity — LOTL techniques make attribution difficult and post-summit scrutiny will likely reveal whether any operations were paused or continued through the summit window.

**Tracking note:** The absence of these groups from diplomatic discussions does not reduce the threat. Assess with moderate confidence that Volt Typhoon maintained pre-positioned access through the summit period.
