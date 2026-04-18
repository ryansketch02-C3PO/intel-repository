# Sandworm (APT44) — Threat Actor Profile

## Identity

| Field | Value |
|---|---|
| **Primary Name** | Sandworm |
| **Designations** | APT44, FROZENBARENTS, Seashell Blizzard, Voodoo Bear, IRIDIUM, Iron Viking, TeleBots, ELECTRUM, UAC-0082, UAC-0113, TEMP.Noble, Quedagh, Blue Echidna |
| **Attribution** | GRU Unit 74455 — Main Centre for Special Technologies (GTsST), Russian Federation |
| **Active Since** | ~2009 |
| **Status** | 🔴 Active |
| **Motivation** | Destruction/Sabotage · Espionage · Influence Operations |
| **Sector Focus** | Critical Infrastructure (Energy/Power Grid), Government, Defense, Telecommunications, Transportation, Logistics |
| **Admiralty Grade** | A1 |
| **TLP** | TLP:WHITE |
| **Last Updated** | 2026-04-04 |

---

## Executive Summary

Sandworm is Russia's most destructive cyber unit — bar none. Attributed with high confidence to GRU Unit 74455 (the Main Centre for Special Technologies, GTsST) and designated APT44 by Mandiant in April 2024, Sandworm has been responsible for nearly every major destructive cyberattack in the past decade. They took down Ukraine's power grid — twice. They deployed NotPetya, the most financially damaging cyberattack in history ($10 billion+ in global losses). They sabotaged the Winter Olympics. They coordinate cyber attacks with Russian artillery and missile strikes in real time.

Sandworm isn't just an espionage group — they're a full-spectrum military cyber unit capable of espionage, sabotage, and information warfare. Where APT28 steals intel, Sandworm destroys infrastructure. They are the Kremlin's blunt instrument, and as of 2025-2026, they're running the highest operational tempo in their history.

---

## Targeting Profile

**Primary sectors:**
- ⚡ **Energy / Power Grid** — the ICS/OT crown jewel. Ukraine grid attacks, Western Europe incident (Dec 2025)
- 🏛️ **Government** — Ukraine, NATO members, former Soviet states
- 🛡️ **Defense & Military** — drone manufacturers, logistics supply chains, communications networks
- 📡 **Telecommunications** — ISPs providing connectivity to Ukrainian military and civilians
- 🚢 **Transportation & Logistics** — supply chains transiting lethal aid to Ukraine
- 🎙️ **Media, Journalism, Civil Society** — OPCW, Bellingcat, investigative reporters
- 🗳️ **Democratic Processes** — electoral systems across NATO and prospective NATO members

**Primary geographic focus:**
- 🇺🇦 Ukraine (primary wartime mandate)
- 🇵🇱 Poland, 🇰🇿 Kazakhstan, Baltic states
- Global critical infrastructure where Russian national interests intersect

---

## Notable Operations

### 2015 — Ukraine Power Grid Attack #1
First ever confirmed destructive cyberattack on power grid infrastructure. Used **BlackEnergy** malware to compromise Ukrainian energy companies. Took down 30 substations, left ~225,000 customers without power. Coordinated with phone floods against utility call centers.

### 2016 — Ukraine Power Grid Attack #2 (Industroyer/Crashoverride)
Second grid attack, more sophisticated. Deployed **INDUSTROYER** — the first malware purpose-built to speak ICS protocols (IEC-104, IEC-101, IEC-61850 GOOSE). Caused a 75-minute blackout in Kyiv. Then deployed **KillDisk** wiper to cover tracks.

### 2017 — NotPetya (ETERNALPETYA)
Deployed via a trojanized update to Ukrainian accounting software M.E.Doc. Spread globally via EternalBlue + Mimikatz credential theft. Encrypted MBR and destroyed data. $10+ billion in damage — the most destructive malware ever deployed. Victims included Maersk ($300M loss), Merck ($870M), FedEx, Mondelez. Timed to coincide with Ukraine's Constitution Day.

### 2018 — Olympic Destroyer (SOURGRAPES)
Deployed during the opening ceremony of the 2018 Pyeongchang Winter Olympics, disrupting systems and embarrassing South Korea/IOC. **False-flagged as Lazarus Group** using fabricated code fragments — one of the most sophisticated deception operations ever observed in cyber. Retaliation for Russia's doping suspension from the Games.

### 2022 — Invasion Day Wiper Wave
On February 24, 2022, the day of Russia's full-scale reinvasion, APT44 deployed **WhisperGate**, **HermeticWiper**, **IsaacWiper**, and **CaddyWiper** against dozens of Ukrainian targets simultaneously.

### April 2022 — Industroyer2 + CaddyWiper Attack Chain
Deployed **INDUSTROYER.V2** against Ukrainian high-voltage substations — designed to speak IEC-104 and physically trip circuit breakers. Simultaneously pre-positioned **CaddyWiper** on the same machine to erase all traces after execution.

### October 2022 — MicroSCADA OT Attack (Novel Technique)
Used a native MicroSCADA binary (LotL technique) to issue unauthorized commands to substation circuit breakers — causing a power outage that **coincided with mass Russian missile strikes on Ukrainian cities**. First documented cyber-kinetic coordination at this level. Followed up with new CADDYWIPER variant to destroy forensic artifacts.

### October 2022 — Prestige Ransomware (Poland & Ukraine)
Deployed **PRESSTEA (Prestige)** fake ransomware against logistics entities in Poland and Ukraine — a direct signal that Sandworm was willing to strike NATO soil. Rare escalation.

### 2024 — Kapeka Backdoor
CERT-UA and WithSecure disclosed **Kapeka** — a novel, sophisticated backdoor deployed in Eastern Europe, attributed to Sandworm. Used to maintain long-term access against critical infrastructure targets.

### 2025 — ZEROLOT & STING Wiper Campaign
ESET documented high-tempo wiper deployments against Ukrainian energy, logistics, and grain sector organizations. **ZEROLOT** abused Active Directory Group Policy for distribution — same GPO-for-wipers playbook, new variant. **STING** deployed alongside.

### December 2025 – January 2026 — Poland Energy/Industrial Incident
Confirmed APT44 intrusion activity targeting Polish energy and industrial organizations. Represents continued escalation of Sandworm's willingness to operate against NATO member critical infrastructure.

---

## Technical Arsenal

### Custom Malware (Unique to APT44)

| Malware | Type | Notes |
|---|---|---|
| **BlackEnergy** | ICS disruption / botnet | First-gen grid attack tool |
| **INDUSTROYER (Crashoverride)** | ICS-specific | Speaks IEC-104, IEC-101, IEC-61850 GOOSE protocols; designed to destroy power grid |
| **INDUSTROYER.V2** | ICS-specific (updated) | Streamlined, faster to deploy |
| **ETERNALPETYA (NotPetya)** | Wiper disguised as ransomware | MBR encryption, EternalBlue propagation |
| **SOURGRAPES (Olympic Destroyer)** | Destructive | False-flagged as Lazarus; targeted Olympics |
| **CADDYWIPER** | Data wiper | Erases user data and partition info; widely deployed |
| **AWFULSHRED** | Linux wiper | Linux-targeted destruction |
| **SOLOSHRED** | Solaris wiper | ICS/OT environment targeting |
| **SwiftSlicer** | Destructive malware | 2023, Ukraine |
| **RansomBoggs** | .NET ransomware | Ukraine targeting |
| **PRESSTEA (Prestige)** | Fake ransomware | Poland/Ukraine logistics |
| **Kapeka** | Backdoor | Novel 2024, Eastern Europe |
| **ZEROLOT** | Wiper | 2025, deployed via GPO; Ukrainian energy |
| **STING** | Wiper | 2025 |
| **Infamous Chisel** | Android malware | Targets Ukrainian military devices |
| **GOGETTER** | Golang tunneler | SOCKS5 proxy, Yamux multiplexer |

### Open-Source / Commodity Tools Used
CHISEL (tunneler), REGEORG.NEO / Neo-REGEORG (webshell), WEEVELY (webshell), DCRat / DARKCRYSTALRAT, SMOKELOADER, RADTHIEF (Rhadamanthys Stealer), SDelete, WinRAR, TANKTRAP (GPO wiper spreader), ArguePatch

### Hacktivist Front Personas
- **XakNet Team** — early wartime persona
- **CyberArmyofRussia_Reborn** — most operationally linked; Google TAG confirmed APT44 operators created and control it
- **Solntsepek** — primary vehicle for claiming disruptive attacks, leaking stolen data

---

## Tactics, Techniques & Procedures (TTPs)

### Initial Access
- **Edge infrastructure exploitation** — VPNs, routers, Zyxel firewalls (CVE-2023-28771, CVE-2023-33009/10)
- **Spear phishing** — Ukraine defense entities, journalists, NATO ministries
- **Trojanized software** — M.E.Doc update (NotPetya), torrent-distributed installers on Ukrainian forums
- **Supply chain compromise** — software developer access → downstream CI compromise + wiper deployment
- **Webmail exploitation** — Exim (CVE-2019-10149), Zimbra, Exchange credential harvesting

### Execution & Persistence
- **GPO abuse (Going for the GPO)** — domain-wide wiper deployment via Group Policy
- **Systemd persistence** — GOGETTER tunneler persists across reboots
- **Scheduled tasks** — Industroyer2 timed for execution

### Lateral Movement
- Living-off-the-land (LOTL) — native binaries, no custom tools where possible
- MicroSCADA native binary abuse (LotLBin) for OT environment access

### Impact
- Wiper malware deployment
- ICS/OT direct protocol attacks — physically tripping substations
- Coordination with kinetic strikes (cyber + missile attacks simultaneously)
- Ransomware-disguised wipers
- Influence operations (Telegram persona amplification)

---

## Exploited Vulnerabilities

| CVE | Description | Severity |
|---|---|---|
| CVE-2014-4114 | MS Windows OLE RCE (PowerPoint) | HIGH |
| CVE-2019-10149 | Exim MTA Remote Command Execution | CRITICAL |
| CVE-2022-30190 | MSDT Follina RCE | HIGH |
| CVE-2023-28771 | Zyxel Firewall OS Command Injection | CRITICAL |
| CVE-2023-33009 | Zyxel Firewall Buffer Overflow | CRITICAL |
| CVE-2023-33010 | Zyxel Firewall Buffer Overflow | CRITICAL |
| WinRAR vulnerability (2023) | Used for SMOKELOADER delivery in drone sector targeting | HIGH |

---

## Connections

- **APT28 / Fancy Bear (#006)** — GRU siblings. APT28 = Unit 26165; Sandworm = Unit 74455. Different units, shared GRU parent. APT28 steals and leaks; Sandworm destroys. Have coordinated on operations (Olympic Destroyer false-flag nearly attributed to APT28). Finding one should trigger a hunt for the other.
- **Lazarus Group (#003)** — Sandworm attempted to false-flag Olympic Destroyer as Lazarus using fabricated code fragments. Competing nation-state actors.
- **NTC Vulkan** — Leaked "Vulkan Files" documents showed a Russian contractor building cyber operation frameworks specifically for Sandworm's parent unit.

---

## Key Intelligence Assessments

- **Mandiant (A1):** APT44 is "Russia's preeminent cyber sabotage unit" — responsible for virtually all GRU destructive ops
- **CISA/NSA/UK NCSC:** Multiple joint advisories; Seashell Blizzard (Microsoft designation) confirmed for active edge exploitation campaigns
- **ESET (B1):** High-tempo wiper operations through Q2-Q3 2025, including ZEROLOT via GPO
- **CERT-UA:** Ongoing tracking as UAC-0082, UAC-0099, UAC-0113 subclusters

---

## Sources

- Mandiant APT44 Report (April 2024): https://cloud.google.com/blog/topics/threat-intelligence/apt44-unearthing-sandworm
- Mandiant Sandworm OT Attack (October 2022): https://cloud.google.com/blog/topics/threat-intelligence/sandworm-disrupts-power-ukraine-operational-technology/
- ESET APT Activity Report Q2-Q3 2025: https://www.eset.com/us/about/newsroom/research/eset-research-apt-report-russian-cyberattacks-in-ukraine-intensify-sandworm-unleashes-new-destructive-wiper/
- WithSecure Kapeka Analysis (April 2024): https://labs.withsecure.com/publications/kapeka
- MITRE ATT&CK G0034: https://attack.mitre.org/groups/G0034/
