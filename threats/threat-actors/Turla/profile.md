# Turla (Snake / Uroburos / Venomous Bear)

## Overview

| Field | Value |
|-------|-------|
| **Entry #** | 026 |
| **Aliases** | Snake, Uroburos, Waterbug, Venomous Bear, Pensive Ursa (Palo Alto), Secret Blizzard (Microsoft), KRYPTON, Iron Hunter, Group 88, Blue Python, SUMMIT, UNC4210, BELUGASTURGEON, MAKERSMARK (CSE), TAG-0530, Pacifier APT, White Atlas, WhiteBear |
| **Type** | State-Sponsored APT |
| **Origin** | Russia — FSB Center 16 (SIGINT division) |
| **Status** | 🔴 ACTIVE |
| **Threat Level** | 🔴 HIGH |
| **Active Since** | 1996 (Operation Moonlight Maze) / confirmed since 2004 |
| **Sector Focus** | Government, Military, Defense, Diplomatic/Embassy, Aerospace, Energy, Research/Academia, Media, NGOs |
| **Geography** | Global — NATO states primary, with operations across 50+ countries |
| **Intel Grade** | A1 |
| **TLP** | WHITE |

---

## Attribution

Turla is operated by **Center 16** of Russia's Federal Security Service (FSB) — the FSB's main signals intelligence (SIGINT) directorate. This distinguishes them from other Russian APTs:

- **APT28 (Fancy Bear)** → GRU Unit 26165 (military intelligence)
- **APT29 (Cozy Bear)** → SVR (foreign intelligence)
- **Sandworm** → GRU Unit 74455 (destructive ops)
- **Turla** → FSB Center 16 (domestic/counterintelligence SIGINT)

Attribution evidence includes:
- **Estonian Foreign Intelligence Service (2018)**: Directly attributed to FSB
- **UK NCSC**: Attributed to FSB Center 16
- **German BfV (2022)**: Identified two former Turla programmers at Center-Inform, a company officially operating on behalf of the FSB
- **DOJ/FBI (May 2023)**: Monitored FSB officers running Snake from an FSB facility in Ryazan, Russia — court-documented in Operation MEDUSA affidavit

---

## History & Key Operations

### Operation Moonlight Maze (1996–1998)
Turla's likely origin story. A massive cyberespionage campaign targeting the US Navy, Air Force, NASA, Department of Energy, EPA, and NOAA. Stolen documents were exfiltrated via compromised computers in Moscow. Attribution to Turla's predecessors was confirmed by a 2017 Kaspersky/King's College London joint investigation tracing continuous source code lineage from 1996 to modern Turla tooling.

### Pentagon/CENTCOM (2008)
A USB-borne worm (later identified as Agent.BTZ, a Turla predecessor) infected classified and unclassified networks at US Central Command. The incident triggered a 14-month remediation effort and directly led to the creation of US Cyber Command.

### German Bundestag / Foreign Office (2014–2018)
Turla compromised the German Federal Academy of Public Administration, then pivoted through government network (IVBB) to the Foreign Office. Attackers had persistent access for over three years before detection. German public broadcasters BR and WDR published exposés linking the operation directly to FSB.

### Finnish Parliament (2020–2021)
Turla-linked malware found in Finnish parliamentary systems. Not publicly attributed by Finland, but confirmed by Kaspersky and F-Secure.

### Hijacking OilRig C2 Infrastructure (2018–2019)
In one of the most audacious operations in APT history, Turla hijacked the C2 infrastructure of **OilRig (APT34)**, an Iranian state-sponsored group. For at least 18 months, Turla rode inside OilRig's access to deliver its own malware to OilRig's targets — including Middle Eastern governments. Victims thought they were dealing with an Iranian APT. They were actually dealing with Russian FSB. (Symantec, 2019)

### Ukraine Operations (2022–2025)
Following Russia's full-scale invasion, Turla intensified operations against Ukrainian defense sector targets. In 2023, Mandiant documented Turla re-registering expired Andromeda C2 domains to reach Ukrainian victims. In 2024, Microsoft documented Turla hijacking the Amadey cybercrime botnet for access. In **2025**, ESET uncovered the first documented FSB-to-FSB collaboration: **Gamaredon (Center 18) providing initial access to Turla (Center 16)** — cherry-picking high-value Ukrainian targets from Gamaredon's massive infection pool.

### Operation MEDUSA (May 9, 2023)
The FBI developed **PERSEUS** — a custom tool that impersonated Turla operators to issue shutdown commands to Snake's P2P network. Court-authorized under a warrant from the Eastern District of New York, PERSEUS caused Snake to overwrite its own vital components across hundreds of infected machines in 50+ countries. The operation neutralized Snake's specific infrastructure but did NOT remove other Turla tools, did NOT revoke stolen credentials, and did NOT eliminate Turla's capability to rebuild.

---

## Target Profile

### Sectors
- 🏛️ Government & Diplomatic (embassies are a specialty)
- 🪖 Military & Defense contractors
- ✈️ Aerospace and space research
- ⚡ Energy and natural resources
- 🎓 Research institutions and universities (used as infrastructure)
- 📰 Journalists and media (specific individuals tracked)
- 🌐 NGOs and civil society

### Geography (confirmed operations)
United States, Germany, France, Belgium, UK, Finland, Switzerland, Poland, Romania, Kazakhstan, Ukraine, Georgia, Armenia, Belarus, Uzbekistan, Tajikistan, Afghanistan, India, China, Saudi Arabia, Iraq, Iran, Jordan, Israel, and 30+ more. Effectively global.

---

## Tactics, Techniques & Procedures (TTPs)

### Initial Access
- **Spearphishing** — highly targeted, diplomatic-themed lures; malicious .hta files, weaponized Office docs, PDF lures
- **Watering Hole Attacks** — compromising websites frequented by diplomatic and government targets
- **USB/Removable Media** — Agent.BTZ precedent; still employed for air-gapped targets
- **Supply Chain / Third-Party Hijacking** — hijacking other threat actors' C2 infrastructure (OilRig, Andromeda, Amadey, SideCopy) to reach targets without burning own infrastructure

### Execution & Persistence
- **Epic Turla** — first-stage backdoor; spearphishing/watering hole delivered; leads to Carbon/Cobra
- **Carbon/Cobra** — sophisticated second-stage peer-to-peer framework with encrypted comms
- **Snake** — flagship modular rootkit backdoor; active since 2004; encrypted custom P2P protocol; survives reboots and many remediation attempts; operates as kernel-mode driver
- **Kazuar** — .NET-based backdoor; multiple versions (v2, v3 documented 2024–2025); used post-Snake disruption
- **KOPILUWAK** — JavaScript-based reconnaissance utility; deployed after compromising legacy C2
- **QUIETCANARY** — lightweight .NET backdoor for file collection and exfiltration
- **ComRAT** — fourth-generation backdoor using Gmail web interface as C2 channel

### C2 & Evasion (distinctive techniques)
- **Satellite Internet Hijacking** — Turla intercepted unencrypted satellite DVB streams, identified legitimate IPs, spoofed them as their own uplinks. Exfil packets sent to these satellite IPs were received by Turla's antenna. Impossible to block without disrupting legitimate satellite service.
- **Gmail-based C2** — ComRAT uses the Gmail web UI (via HTTP) for command delivery. Blocking Gmail blocks your organization.
- **Google Drive/Dropbox C2** — routing commands through legitimate cloud platforms
- **Encrypted P2P routing** — Snake creates a global network of unwitting victim machines as relay nodes; traffic appears as normal peer-to-peer; virtually impossible to attribute the final C2
- **Hijacking expired C2 domains** — re-registering lapsed botnet C2 domains to inherit existing malware infections
- **Process injection** — hiding within legitimate host processes
- **Living off the land** — using native Windows tools where possible

### Exfiltration
- Data staged locally, then exfiltrated via Snake's encrypted P2P network through relay nodes in multiple countries
- Audio exfiltration from air-gapped systems (documented unique capability)
- Keylogger deployed alongside Snake to harvest credentials for persistent re-entry

---

## Malware Families

| Malware | Type | Notes |
|---------|------|-------|
| Snake (Uroburos) | Rootkit/backdoor | Flagship; P2P network; neutralized by MEDUSA May 2023; may be rebuilt |
| Carbon/Cobra | Second-stage framework | Modular P2P; sophisticated encrypted comms |
| Kazuar | .NET backdoor | Active post-2023; v2 and v3 deployed 2024–2025 in Ukraine |
| ComRAT (Agent.BTZ evolution) | RAT | Uses Gmail web UI for C2; 4th gen |
| Epic Turla (Wipbot/Tavdig) | First-stage loader | Delivers Carbon/Cobra |
| KOPILUWAK | JS recon utility | Post-Andromeda hijack deployment |
| QUIETCANARY | .NET data collector | File staging and exfil |
| HyperStack | RPC backdoor | Lateral movement in enterprise networks |
| Capibar | .NET backdoor | 2023 Ukraine deployments |

---

## Key Differentiators

1. **Oldest active APT in Western intelligence reporting** — traceable lineage from Moonlight Maze (1996) to 2025 Gamaredon collaboration
2. **Third-party hijacking as a doctrine** — OilRig, Andromeda, Amadey, SideCopy, now Gamaredon. Turla routinely rides inside other actors' infrastructure rather than burning its own
3. **Satellite C2** — unique capability documented since 2015; used for high-value targets
4. **Survived law enforcement disruption** — MEDUSA killed Snake in May 2023. Kazuar v2/v3 were documented in active use in Ukraine by 2024–2025. They rebuilt.
5. **Extreme patience** — penetrations measured in years; German Foreign Office compromised for 3+ years before detection; Snake persisted on some machines through active remediation attempts
6. **FSB-internal collaboration** — 2025 ESET discovery of Gamaredon (Center 18) + Turla (Center 16) joint operations suggests FSB is coordinating its cyber units at an operational level

---

## Defensive Guidance

🔴 **Hunt for Kazuar artifacts** — check for `scrss.ps1`, unusual PowerShell downloaders, .NET assemblies loading from temp directories  
🔴 **Monitor satellite uplink traffic** for anomalous DVB stream packets  
🔴 **Alert on unusual Gmail/Google Drive API traffic** from non-browser processes  
🔴 **Review all installed Windows services** for unfamiliar kernel-mode drivers  
🔴 **Treat all remediation as partial** — Turla installs keyloggers alongside primary implants; stolen credentials survive malware removal  
🟡 **Audit USB policy** — especially for air-gapped environments; Agent.BTZ-style USB propagation remains in playbook  
🟡 **Block or alert on anonymous P2P-style encrypted traffic** that doesn't match known application profiles  
🟡 **Apply CISA AA23-129A detection signatures** — Snort/YARA rules for Snake artifacts (even post-MEDUSA, some systems may still be infected)  
🟢 **MFA on all external-facing systems** — Turla's keylogger steals credentials; MFA raises the cost of replay attacks  
🟢 **Network segmentation** — Turla's P2P relay model assumes lateral movement; segment to limit blast radius  

---

## References

- DOJ Press Release: Operation MEDUSA (May 9, 2023)
- CISA Advisory AA23-129A — Hunting Russian Intelligence Snake Malware (May 2023)
- ESET: Gamaredon-Turla Collaboration in Ukraine (September 2025)
- Symantec: Turla Hijacks OilRig Infrastructure (2019)
- Mandiant: Turla Ukraine Andromeda Operations (January 2023)
- Microsoft MSTIC: Turla Amadey Hijacking (2024)
- EuRepoC: Turla APT Profile (December 2022)
- Estonian Foreign Intelligence Service Annual Report (2018)
- FBI Affidavit: Eastern District of New York Docket No. 23-MJ-0428 (CLP)

---

*Last updated: 2026-04-22 | Intel Grade: A1 | TLP: WHITE*
