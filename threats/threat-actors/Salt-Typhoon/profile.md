# 🧂 Salt Typhoon — Threat Actor Profile

**Scoundrel #010**

---

## Identity

| Field | Details |
|---|---|
| **Primary Name** | Salt Typhoon |
| **Aliases** | Earth Estries (Trend Micro), GhostEmperor (Kaspersky), FamousSparrow (ESET), UNC2286 (Mandiant), OPERATOR PANDA (CrowdStrike), UNC4841 (partial overlap) |
| **MITRE ATT&CK ID** | [G1045](https://attack.mitre.org/groups/G1045/) |
| **Type** | Nation-State APT |
| **Origin** | People's Republic of China (PRC) |
| **Sponsor** | MSS — Ministry of State Security (with PLA SIGINT coordination) |
| **Active Since** | 2019 (possibly earlier under alias clusters) |
| **Status** | 🔴 Highly Active |
| **Motivation** | Long-term SIGINT collection · Counterintelligence · Pre-positioning for wartime disruption |
| **Threat Level** | 🔴 CRITICAL |

---

## Summary

Salt Typhoon is the MSS's premier telecommunications espionage unit — a long-dwell, low-noise threat actor that infiltrated the backbone of the global communications ecosystem. What started as regional espionage campaigns against hotels and Southeast Asian governments evolved into one of the most consequential cyber operations in US history: the systematic compromise of at least nine major US telecom carriers, including AT&T, Verizon, T-Mobile, Lumen, and Charter Communications.

The FBI has called it **the most significant espionage breach in US history by a nation-state hacking group.** The group accessed CALEA lawful intercept systems — the wiretap infrastructure that the US government itself uses for surveillance — giving them visibility into the communications of senior US officials, presidential campaign staff, and congressional representatives. As of 2026, they may still be inside.

Salt Typhoon doesn't steal and leave. It installs itself into routers and backbone infrastructure and **lives there**, collecting intelligence for months or years. The mission is SIGINT at scale, with a secondary mandate to pre-position for potential wartime disruption of US communications infrastructure.

---

## Organizational Structure

Salt Typhoon operates under MSS direction but uses a contractor hybrid model — multiple private Chinese tech companies act as cutouts:

| Company | Role |
|---|---|
| **Sichuan Juxinhe Network Technology** | Infrastructure ops, domain management, C2 staging — sanctioned by US Treasury Jan 2025 |
| **Beijing Huanyu Tianqiong IT** | Front company providing C2/monitoring tools (Shadow Network platform) |
| **Sichuan Zhixin Ruijie** | Dual-use telecom/router monitoring tools with offensive capability |

Overlap with **i-SOON** (Anxun Information Technology) contractor ecosystem has also been confirmed. The group operates as multiple distinct teams targeting different regions simultaneously.

**Key indicted individuals:**
- **Yin Kecheng** — Infrastructure operator; $2M FBI reward
- **Zhou Shuai** — Affiliated with Shanghai Heiying IT; data laundering and access resale

---

## Target Profile

**Primary targets:**
- Telecommunications providers (backbone & edge infrastructure)
- Internet service providers (ISPs) and data centers
- US government and law enforcement lawful intercept systems

**Secondary targets:**
- National Guard and military networks
- Government agencies, foreign ministries, congressional staff
- Defense contractors and think tanks
- Hotels, transportation, NGOs (counterintelligence-adjacent)

**Geographic focus:** United States (primary), UK, EU, Five Eyes allies, Taiwan, Southeast Asia, globally (80+ countries, 200+ confirmed victims as of Aug 2025)

**Confirmed victims include:**
- AT&T, Verizon, T-Mobile, Lumen, Charter Communications, Windstream, Consolidated Communications
- Digital Realty (assessed likely victim), Comcast (likely victim — NSA assessment)
- Viasat (Feb 2025), unnamed Canadian telecom
- US Army National Guard network (Mar–Dec 2024)
- Congressional committee staff systems (Jan 2026)
- Telecoms in Singapore, Norway, Australia, UK, EU

---

## Tactics, Techniques & Procedures (TTPs)

### Initial Access
- Exploit public-facing edge devices: VPN appliances, firewalls, routers (especially Cisco IOS XE, Ivanti, Sophos, FortiGate, Palo Alto)
- Target unpatched CVEs — in several cases, using vulns disclosed years earlier
- Credential brute force and password cracking of network device configs

### Persistence
- Deploy custom router/firmware rootkits (Demodex kernel rootkit — extremely difficult to detect/remove)
- SSH authorized_keys modification on Linux-level router access (`T1098.004`)
- Create covert Linux accounts via `/etc/passwd` and `/etc/shadow` modification
- GRE tunnel creation for persistent covert channels
- ACL manipulation to ensure continued access post-discovery

### Collection
- **Network sniffing**: Deploy JumbledPath sniffer at network chokepoints to capture traffic in real-time
- **Lawful intercept system access**: Compromised CALEA infrastructure, enabling wiretap of US officials' calls
- **Credential harvesting**: Dump TACACS+ authentication traffic, crack weak-encrypted configs
- **Call Detail Records (CDRs)**: Mass exfiltration of subscriber metadata
- VoIP infrastructure configs, network diagrams, IR playbooks

### Defense Evasion
- Living-off-the-land binaries (LOLBINs): PsExec, WMI, PowerShell, robocopy
- DLL side-loading via legitimate AV software (Norton, Bkav, IObit)
- Log clearing: .bash_history, auth.log, lastlog, wtmp, btmp (`T1070.002`)
- PowerShell downgrade attacks and script encryption
- Kernel-mode rootkits (Demodex) for defense evasion at OS level

### Lateral Movement
- Abuse trusted ISP-to-ISP peering connections to pivot into partner telcos
- SSH via compromised loopback/switches to bypass ACLs (`T1021.004`)
- Modified router configs as pivots to downstream/upstream network segments

### Command & Control
- Cobalt Strike beacons over HTTPS
- Dual-channel C2 combining dedicated infra with legitimate services (GitHub, Gmail, AnonFiles, File.io, LightNode VPS)
- GRE and IPsec tunnels for covert exfiltration channels
- Beacon intervals designed to blend into telecom backbone traffic

### Exfiltration
- Config files via FTP/TFTP (`T1048.003`)
- Data staged inside compromised routers before exfiltration
- Network Device Configuration Dump (`T1602.002`)

---

## Malware & Tools

| Tool | Type | Notes |
|---|---|---|
| **Demodex** | Kernel rootkit | Long-dwell persistence; survives reboots; extremely hard to detect |
| **SparrowDoor** | Backdoor | Modular, multi-stage; used in FamousSparrow-era campaigns |
| **GhostSpider** | Backdoor | Custom backdoor targeting telecom and gov infrastructure |
| **JumbledPath** | Network sniffer | Custom tool for packet capture at network chokepoints |
| **Cobalt Strike** | Framework | Commercial tool used for post-exploitation |
| PsExec, WMI, PowerShell | LOLBINs | Living-off-the-land execution |

---

## Key Exploited CVEs

| CVE | System | Notes |
|---|---|---|
| CVE-2018-0171 | Cisco IOS Smart Install | Initial access to Cisco routers |
| CVE-2021-26855 | Microsoft Exchange (ProxyLogon) | Initial access via SSRF |
| CVE-2022-3236 | Sophos Firewall | Code injection |
| CVE-2023-20198 | Cisco IOS XE Web UI | Privilege escalation |
| CVE-2023-46805 | Ivanti Connect Secure | Auth bypass |
| CVE-2024-21887 | Ivanti Connect Secure | Command injection |
| CVE-2025-5777 | Citrix NetScaler Gateway | Initial access (2025) |

---

## Notable Campaigns

| Timeframe | Campaign | Victims | Impact |
|---|---|---|---|
| 2019–2021 | GhostEmperor / FamousSparrow | SE Asian govts, hotels worldwide | Kernel rootkit deployment; recognized years later as Salt Typhoon |
| 2022–2023 | EU ISP Router Hijacking | Dutch, German, French ISPs | Firmware implants for SIGINT collection |
| 2023–2024 | US Telecom Campaign | AT&T, Verizon, T-Mobile, Lumen, 5+ more | CALEA wiretap access; CDRs; comms of senior US officials including Trump/Vance |
| Mar–Dec 2024 | US National Guard Network | US state Army National Guard | Network maps, VPN configs, IR playbooks; connections to all 50 states |
| 2023–2024 | UK Infrastructure | Gov, military, transport, telecom | SIGINT collection against Five Eyes partner |
| Feb 2025 | Canadian Telecom | Unnamed carrier | Breach confirmed |
| Jun 2025 | Viasat | US satellite internet provider | Expansion into satellite comms |
| Jan 2026 | Congressional Staff Email | House Foreign Affairs, Intelligence, Armed Services committees | Congressional oversight compromised |

---

## MITRE ATT&CK Mapping

| Tactic | Technique | ID |
|---|---|---|
| Initial Access | Exploit Public-Facing Application | T1190 |
| Persistence | Account Manipulation: SSH Authorized Keys | T1098.004 |
| Persistence | Create Account | T1136 |
| Defense Evasion | Impair Defenses: Disable/Modify Firewall | T1562.004 |
| Defense Evasion | Indicator Removal: Clear Linux Logs | T1070.002 |
| Credential Access | Brute Force: Password Cracking | T1110.002 |
| Discovery | Data from Network Device Config | T1602.002 |
| Discovery | Gather Victim Network Info: Topology | T1590.004 |
| Lateral Movement | Remote Services: SSH | T1021.004 |
| Collection | Network Sniffing | T1040 |
| Command & Control | Protocol Tunneling (GRE) | T1572 |
| Exfiltration | Exfiltration Over Unencrypted Protocol | T1048.003 |
| Develop Capabilities | Malware | T1587.001 |

---

## Geopolitical Context

Salt Typhoon's mission sits at the intersection of MSS counterintelligence and PLA strategic planning:

- **MSS mandate**: Spy on US government officials, intercept communications, identify intelligence community personnel
- **PLA mandate**: Pre-position inside telecom backbone for potential wartime disruption — if kinetic conflict erupts over Taiwan, Salt Typhoon's router implants are already in place to degrade US command-and-control communications

The FBI's characterization of this as "the most egregious national security breach in US history by a nation-state hacking group" reflects the depth of access: not just stolen documents, but **real-time interception of government communications** and the ability to disrupt the entire communications fabric of the nation at will.

US response has been complicated by: telecom companies invoking legal privilege to avoid disclosing compromise; the Trump administration disbanding the Cyber Safety Review Board mid-investigation; and ongoing uncertainty about whether Salt Typhoon has been fully evicted.

---

## Admiralty Scale Assessment

| Component | Grade | Notes |
|---|---|---|
| **Source Reliability** | A | US government agencies, CISA/NSA/FBI joint advisories, major vendor reports |
| **Information Credibility** | 1 | Confirmed by DOJ indictments, Treasury sanctions, congressional hearings |
| **Overall** | **A1** | Highest confidence |
| **TLP** | WHITE | Publicly documented |

---

## Defensive Recommendations

1. **Patch edge devices aggressively** — Salt Typhoon exploits known CVEs, often years old. Cisco, Ivanti, Sophos, Fortinet, and Palo Alto appliances are priority targets
2. **Audit SSH authorized_keys** on network devices — unauthorized entries are a confirmed persistence mechanism
3. **Monitor TACACS+ authentication traffic** for anomalous captures or config dumps
4. **Inspect GRE tunnel configurations** for unauthorized additions
5. **Implement out-of-band management** for network devices to limit exposure
6. **Hunt for Demodex rootkit** on Linux-based network appliances — kernel-level persistence that survives typical remediation
7. **Review CALEA/lawful intercept system access** — if you're a telco, assume this was targeted
8. **Network segmentation** to limit ISP-to-ISP lateral movement paths
9. **Deploy NDR** to detect anomalous traffic patterns on backbone infrastructure

---

## References

- [MITRE ATT&CK G1045](https://attack.mitre.org/groups/G1045/)
- [CISA AA25-239A — Joint Advisory on Salt Typhoon](https://www.cisa.gov/)
- [FBI $10M Bounty Announcement (Apr 2025)](https://www.fbi.gov/)
- [DomainTools — Inside Salt Typhoon (Jan 2026)](https://dti.domaintools.com/research/inside-salt-typhoon-chinas-state-corporate-advanced-persistent-threat)
- [Check Point Research — GhostEmperor](https://research.checkpoint.com)
- [Vectra AI Threat Briefing — Salt Typhoon (2025)](https://www.vectra.ai/resources/vectra-ai-threat-briefing-salt-typhoon)
- [Wikipedia — Salt Typhoon](https://en.wikipedia.org/wiki/Salt_Typhoon)

---

*Last Updated: 2026-04-07 | Maintained by C3PO*
