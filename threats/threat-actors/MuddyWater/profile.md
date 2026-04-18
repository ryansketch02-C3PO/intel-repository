# MuddyWater (Mango Sandstorm) — Iranian MOIS Espionage APT

## Identity

| Field | Details |
|---|---|
| **Primary Name** | MuddyWater |
| **Aliases** | Seedworm, Mango Sandstorm, Static Kitten, MERCURY, TEMP.Zagros, Earth Vetala, TA450, Boggy Serpens |
| **Nation-State** | 🇮🇷 Iran |
| **Sponsor** | Ministry of Intelligence and Security (MOIS) — Iran's civilian foreign intelligence service |
| **MITRE ATT&CK ID** | [G0069](https://attack.mitre.org/groups/G0069/) |
| **Active Since** | 2017 |
| **Type** | Nation-State APT / Cyber Espionage |
| **Threat Level** | 🔴 HIGH |
| **Primary Objective** | Long-term espionage, credential harvesting, intelligence collection for MOIS |
| **Secondary Objective** | Pre-positioning for partner destructive operations (Handala / Void Manticore) |
| **Admiralty Grade** | A1 — formally attributed by CISA, FBI, NSA, CNMF, NCSC-UK in joint advisory AA22-055A |

---

## Overview

MuddyWater doesn't blow things up. It watches. It collects. It waits.

Named by Palo Alto Networks in 2017 because early campaigns were difficult to attribute — "muddy water" obscuring the true picture — the group has spent seven-plus years quietly embedding itself in government networks, telecom providers, defense contractors, and critical infrastructure across the Middle East and, increasingly, the West. Formally confirmed as **"a subordinate element of MOIS"** in a 2022 joint advisory signed by five intelligence agencies across two countries.

What sets MuddyWater apart from noisier Iranian actors like Handala or APT33 is **operational patience**. They don't rush. They get in, stay in, and collect — sometimes for months before anyone notices. The Stryker attack in early 2026 is a perfect illustration: MuddyWater spent weeks doing the quiet access work (credential harvesting, cloud admin enumeration, Intune mapping), then handed off to Handala who pulled the trigger. Two teams, one operation. MuddyWater is MOIS's **key that opens the door**.

Their toolkit has evolved significantly since 2017 — from PowerShell-only scripts to a mature multi-tool arsenal that now includes Rust-based backdoors, blockchain-resistant C2 (ChainShell), AI-assisted malware development, and procurement of capabilities from Russian criminal marketplaces. This is not a static threat.

**Relevance to aerospace/defense:** MuddyWater targeted **US banks, airports, and defense-adjacent software companies** in February–March 2026. The Tortoiseshell/UNC1549 cluster — which is IRGC rather than MOIS — runs parallel aerospace campaigns. Between the two, Iran has near-comprehensive coverage of the defense industrial base as a targeting priority.

---

## Targeting Profile

### Primary Sectors
- Government & defense ministries
- Telecommunications providers
- Defense contractors and aerospace organizations
- Financial institutions (banks, fintech)
- Oil & gas and energy
- Aviation and airport infrastructure
- Healthcare and NGOs

### Geographic Focus
| Region | Countries |
|---|---|
| Middle East (core) | Israel, Saudi Arabia, UAE, Turkey, Kuwait, Jordan, Bahrain, Iraq |
| North Africa | Egypt, Tunisia |
| South Asia | Pakistan, India |
| **North America (expanding)** | **United States, Canada** — significant since 2026 |
| Europe | UK, Germany, Portugal (opportunistic) |
| Maritime | Malaysia, UAE ports |

Geographic expansion mirrors Iranian geopolitical pressure points: wherever Iran has a grievance, MuddyWater has a target.

---

## TTPs — How They Operate

### Initial Access
| Technique | MITRE ID | Detail |
|---|---|---|
| Spearphishing with malicious attachments | T1566.001 | Macro-laden .doc/.xls, PDF lures, VBScript/JavaScript droppers |
| Phishing via compromised third-party mailboxes | T1566 | Uses legitimate email accounts to bypass domain reputation filters |
| Malicious links via cloud file shares | T1566.002 | OneHub, Sync, TeraBox, OneDrive, Dropbox — payload hosting |
| Exploitation of internet-facing vulnerabilities | T1190 | Exchange, VPN, perimeter appliances (see CVEs below) |
| Password spraying against OWA and VPN portals | T1110.003 | Systematic, low-noise credential attacks |
| "Honeytrap" social engineering | T1566 | Fake recruiter and romantic contact approaches |

### Execution
| Technique | MITRE ID | Detail |
|---|---|---|
| PowerShell (obfuscated) | T1059.001 | Base64 + string reversal + GZip stacking — defeats most AMSI |
| AMSI bypass/patching | T1562.001 | Kills PowerShell script inspection at runtime |
| DLL side-loading | T1574.002 | PowGoop hijacks `GoogleUpdate.exe` via `Goopdate.dll` |
| LOLBAS execution chains | T1218 | regsvr32 → mshta → wscript → cmstp |
| Scheduled tasks | T1053.005 | Persistence and delayed execution |

### Persistence
| Technique | MITRE ID | Detail |
|---|---|---|
| Registry Run Keys | T1547.001 | `HKCU\Software\Microsoft\Windows\CurrentVersion\Run` |
| DLL side-loading | T1574.002 | Malicious DLL loaded by trusted process |
| Scheduled tasks | T1053.005 | Recurring execution of implants |
| Legitimate RMM tools as persistent C2 | T1219 | Atera, SimpleHelp, ScreenConnect, AnyDesk, NetBird, Syncro |
| Hidden admin accounts | T1136.001 | Created post-compromise for persistence |

### Defense Evasion
| Technique | MITRE ID | Detail |
|---|---|---|
| Obfuscated scripts | T1027 | Layered Base64 + reversal + GZip + char substitution |
| AMSI patching | T1562.001 | Runtime kill of PowerShell script inspection |
| Masquerading filenames | T1036 | "OutlookMicrosift," Windows Defender-adjacent names |
| Timestomping | T1070.006 | Artifacts timestamped to match legitimate system files |
| Registry-based payload storage | T1027.011 | No files on disk — payload lives in registry |
| Living off the land | T1218 | Built-in Windows binaries only |

### Credential Access
| Technique | MITRE ID | Detail |
|---|---|---|
| Credential harvesting via Mimikatz | T1003.001 | LSASS memory dump |
| HackBrowserData | T1555.003 | Browser-stored credentials |
| LaZagne | T1555 | Multi-source credential extraction |
| Keylogging (POWERSTATS) | T1056.001 | Screenshots + keylog capability |
| Password spraying | T1110.003 | Initial access and lateral movement |

### Lateral Movement
| Technique | MITRE ID | Detail |
|---|---|---|
| ZeroLogon exploitation | T1210 | CVE-2020-1472 — still in active use |
| Kerberoasting | T1558.003 | Domain credential escalation |
| DCSync | T1003.006 | Domain controller credential dump |
| RDP hijacking | T1563.002 | Lateral movement via hijacked sessions |
| Internal spearphishing | T1534 | Using compromised accounts to target colleagues |

### C2 Channels
| Channel | Tool | Notes |
|---|---|---|
| Telegram Bot API | Small Sieve, GRAMDOOR, Dindoor | Blends with legitimate Telegram traffic — hard to block |
| DNS tunneling | Mori | High-entropy subdomain queries |
| HTTP/S beaconing | POWERSTATS, PowGoop | 10–60 min intervals — slow enough to avoid thresholds |
| Blockchain (Ethereum smart contract) | ChainShell | C2 address resolved from chain — sinkholing impossible |
| Cloud storage | Rclone → Wasabi, Backblaze B2, Put.io | Exfiltration blends with backup traffic |
| Legitimate RMM | SimpleHelp, ScreenConnect, Atera | Traffic indistinguishable from IT operations |
| Firebase | Staging pages | Hosts C2 staging behind legitimate Google infrastructure |

### Collection & Exfiltration
| Technique | Detail |
|---|---|
| `makecab.exe` compression | Uses native Windows cabinet tool — trusted binary, small archives |
| Rclone to cloud storage | Wasabi, Backblaze B2, Put.io — blends with legitimate backup |
| Screenshot capture | POWERSTATS built-in |
| Diplomatic communications | Priority intelligence collection target |
| Infrastructure schematics | MOIS collection requirement |
| Personnel / biometric data | EgyptAir Riyadh ZKTeco biometric dump (sdrhi server, 2026) |

---

## Malware Arsenal

| Tool | Type | Language | Notes |
|---|---|---|---|
| **POWERSTATS** (Powermud) | RAT / Backdoor | PowerShell | Flagship implant since 2017; screenshots, credential theft, C2 |
| **PowGoop** | DLL Loader | PowerShell | Side-loads via `GoogleUpdate.exe`; decrypts `config.txt` for C2 |
| **Small Sieve** | Backdoor | Python | Telegram Bot API C2; delivered as `gram_app.exe` |
| **Mori** | Backdoor | C++ (`FML.dll`) | DNS tunneling C2 |
| **Canopy / STARWHALE** | Backdoor | WSF | Dropped via Excel lures |
| **BugSleep** | Backdoor | C | 2024; targets Israeli organizations; file transfer + command exec |
| **MuddyViper** | Backdoor | Unknown | Israel-focused; emerged during Israel-Iran conflict |
| **DCHSpy** | Surveillanceware | Android | Deployed within days of Oct 2023 Hamas attack against Israeli targets |
| **Phoenix** | Backdoor | Unknown | Oct 2025; deployed via compromised mailboxes across MENA |
| **Dindoor** | Backdoor | Deno (JavaScript) | 2026; targets US/Canadian orgs; Telegram C2 |
| **Fakeset** | Backdoor | Python | 2026; deployed alongside Dindoor |
| **RustyWater** | Backdoor | Rust | 2026; async C2, process injection, layered encryption |
| **CHAR** | Backdoor | Unknown | Feb 2026 Operation Olalampo; modular post-exploitation |
| **GhostFetch / HTTP_VIP / GhostBackDoor** | Backdoor family | Unknown | Feb 2026 Operation Olalampo |
| **ChainShell** | Agent | Node.js | 2026; blockchain-based C2 (Ethereum smart contract); thin-shell, server-delivered capabilities |
| **UDPGangster** | Backdoor | Unknown | UDP-based; disrupted by Trellix in Nov 2025 (Egypt financial target) |
| **StealthCache** | Implant | Unknown | 2025-2026 toolkit |
| **Fooder** | Loader | Unknown | 2025; stages further payloads |

---

## Notable Campaigns

| Campaign | Date | Targets | Highlights |
|---|---|---|---|
| **Operation Quicksand** | Oct 2020 | Israel | PowGoop + ZeroLogon (CVE-2020-1472) lateral movement |
| **Operation Earth Vetala** | 2021 | UAE, Saudi Arabia, Israel | RemoteUtilities abuse + custom tooling |
| **Technion Attack** | Feb 2023 | Technion Institute, Israel | Ransomware false flag persona "DarkBit"; worked with DEV-1084 |
| **Atera Agent Campaign** | Oct 2023–Apr 2024 | MENA region (broad) | Legitimate RMM as backdoor; BugSleep debut |
| **BugSleep Campaign** | Jul 2024 | Israel (tech, gov) | New C backdoor; Israeli-focused surge during conflict |
| **Phoenix Campaign** | Oct 2025 | 100+ MENA gov entities | Compromised mailbox delivery; custom Phoenix backdoor |
| **Operation Olalampo** | Feb 2026 | Middle East, North Africa | CHAR, GhostFetch, HTTP_VIP, GhostBackDoor; Telegram C2 |
| **Dindoor/Fakeset Campaign** | Feb–Mar 2026 | US banks, airports, defense-adjacent software, Canadian NGO, Israeli defense subsidiary | New backdoors; Rclone → Wasabi exfil; **US targeting escalation** |
| **Stryker Attack (Access Phase)** | Feb–Mar 2026 | Stryker (medical device / defense supply chain) | MuddyWater gained access + pre-positioned; Handala completed destruction via Intune API |
| **ChainShell / CastleRAT Campaign** | Mar 2026 | Israeli targets | Blockchain C2; Russian MaaS (CastleRAT from TAG-150); remained active after exposure |

---

## MOIS Two-Team Operational Model

This is critical context for understanding MuddyWater's true role:

```
TEAM ONE — MuddyWater (Access & Persistence)
  ↓ Long dwell time, minimal noise
  ↓ Password spraying, phishing, perimeter exploitation
  ↓ RMM tools, credential harvesting, AD/cloud recon
  ↓ PIM inventory, Intune estate mapping
  ↓ Hands off access to Team Two

TEAM TWO — Handala / Void Manticore (#014)
  ↓ Inherits access from MuddyWater
  ↓ Deploys wiper malware (Handala Wiper, BiBi)
     OR uses legitimate admin tools (Intune API at Stryker)
  ↓ Destructive operation — maximum impact, minimal traceability
```

**Implication for defenders:** If you find MuddyWater in your network, assume destructive follow-on is planned. The dwell time between MuddyWater access and Handala destruction at Stryker was approximately 3-4 weeks.

---

## Exposed Infrastructure (sdrhi Server — March 2026)

On March 6, 2026, researchers identified an exposed MOIS operational server (`sdrhi`) at `157.20.182.49`, Germany-hosted, running Python SimpleHTTP on port 8888 with an open directory. Contents revealed:

- Farsi-language code comments + Israeli IP range target lists
- Passport, credit card, and payroll data from previous victims
- ZKTeco biometric dumps from EgyptAir Riyadh
- Neo-reGeorg ASPX webshell on Portuguese Immigration Service Exchange server
- Custom asyncio-based OWA brute forcers
- Modified FortiOS exploit creating super_admin accounts
- Weaponized Nuclei templates
- Flask exfiltration servers
- Rclone-to-Wasabi exfil pattern

MuddyWater did not stand down after exposure — new delivery installers were compiled March 11, updated malware appeared March 16, fresh lures spotted March 20.

---

## Threat Context — Why This Matters for Aerospace/Defense

1. **Active US targeting since Feb 2026** — airports, banks, defense-adjacent software companies explicitly confirmed
2. **Supply chain access** — the Stryker attack targeted a medical device company with defense supply chain involvement; same playbook applies to aerospace contractors
3. **Handala handoff risk** — any MuddyWater foothold in a defense-sector org is a potential Handala pre-position for destructive wiper operations
4. **Expanding geographic scope** — North American targeting is no longer exceptional; it's the new normal
5. **Resilience under exposure** — MuddyWater continued operations and retooled rapidly after the sdrhi server was burned in March 2026

---

## Attribution Evidence

| Evidence Type | Detail |
|---|---|
| **Government advisory** | CISA/FBI/NSA/CNMF/NCSC-UK Joint Advisory AA22-055A (Feb 2022): "subordinate element of MOIS" |
| **USCYBERCOM attribution** | Formal attribution to Iran, February 2022 |
| **Farsi strings in malware** | Developer comments and strings in operational tooling |
| **IRST working hours** | Activity concentrated Sunday–Thursday, UTC+3:30 — Iran's business week |
| **2019 OPSEC failure** | Group-IB identified Tehran-based IP directly linked to MuddyWater infrastructure |
| **sdrhi server contents** | Farsi code comments, Israeli IP range lists, MOIS collection targets |
| **Lyceum/OilRig TTP overlap** | Unit 42 documented shared tooling and infrastructure with MOIS-linked clusters |

---

## Detection Priorities

### Highest-Value Detection Rules

```yaml
# 1. PowerShell ScriptBlock Logging — MuddyWater fingerprint
EventID: 4104
Pattern: Base64 string + string reversal + GZip decompression in same script block
Alert: IMMEDIATE — near-certain MuddyWater obfuscation chain

# 2. PowGoop DLL side-loading
Process: GoogleUpdate.exe
LoadedDLL: NOT ["Goopdate.dll from %PROGRAMFILES%\Google\Update\"]
Alert: HIGH — PowGoop hallmark

# 3. Unauthorized RMM installation
Process: msiexec.exe OR installer
Product: [SimpleHelp, AnyDesk, Atera, ScreenConnect, NetBird, Syncro]
Condition: NOT in approved software inventory
Alert: HIGH — immediate investigation required

# 4. Telegram API C2 from non-Telegram processes
Connection: api.telegram.org:443
InitiatingProcess: NOT [telegram.exe, known-telegram-clients]
Alert: HIGH — Small Sieve / GRAMDOOR / Dindoor C2

# 5. Mori DNS tunneling
DNS: High-entropy subdomain queries
QueryLength: >50 chars
Frequency: Burst pattern from same host
Alert: MEDIUM — DNS tunneling indicator

# 6. makecab.exe in temp directories
Process: makecab.exe
OutputPath: %TEMP% OR %APPDATA% OR user profile directories
Alert: MEDIUM — pre-exfiltration staging

# 7. Rclone execution
Process: rclone.exe OR process matching rclone behavioral signature
Destination: wasabi, backblaze, put.io endpoints
Alert: HIGH — exfiltration in progress

# 8. ChainShell indicators
Process: node.exe
ScheduledTask: "Virtual*Guy*" naming pattern
Directory: %LOCALAPPDATA%\Nodejs\
Alert: HIGH — ChainShell deployment
```

---

## Mitigations

| Control | Priority | Impact |
|---|---|---|
| **Disable Office macros from internet-sourced files** (GPO) | 🔴 IMMEDIATE | Cuts primary initial access vector |
| **MFA on all external-facing systems** (Exchange OWA, VPN, RMM) | 🔴 IMMEDIATE | Defeats password spraying and OWA brute force |
| **PowerShell Constrained Language Mode + ScriptBlock Logging (Event ID 4104)** | 🔴 IMMEDIATE | Catches obfuscated POWERSTATS; forces attackers into less stealthy execution |
| **Authorized RMM tool inventory + alerting on deviations** | 🔴 HIGH | Detects SimpleHelp/Atera/AnyDesk deployed as backdoors |
| **Patch perimeter appliances** (Exchange, VPN, FortiOS) | 🔴 HIGH | MuddyWater actively exploits known CVEs |
| **Enable Attack Surface Reduction (ASR) rules** | 🟡 HIGH | Block Office child processes + obfuscated script execution |
| **Deploy LAPS** | 🟡 HIGH | Limits lateral movement from compromised local admin |
| **Behavioral EDR (LOTL + DLL side-load detection)** | 🔴 HIGH | Signature-based AV misses LOLBAS and registry-stored payloads |
| **Block Rclone at network perimeter** | 🟡 MEDIUM | Disrupts primary exfiltration mechanism |
| **Monitor PIM activations** | 🟡 HIGH | MuddyWater maps PIM estate for Handala handoff |
| **Patch ZeroLogon (CVE-2020-1472)** | 🔴 IMMEDIATE (if not done) | Still actively exploited for lateral movement |

---

## Disclosure & Timeline

| Date | Event |
|---|---|
| Feb–Oct 2017 | First campaigns — Saudi Arabia, Iraq, Israel, UAE, Pakistan, India, US |
| 2018 | 130+ victims across 30 organizations compromised |
| Oct 2020 | Operation Quicksand — Israel; PowGoop + ZeroLogon |
| 2021 | Operation Earth Vetala — UAE, Saudi Arabia, Israel |
| Feb 2022 | **CISA/FBI/NSA/CNMF/NCSC-UK formally attribute MuddyWater to MOIS** (AA22-055A) |
| Feb 2023 | Technion attack — false ransomware persona "DarkBit"; DEV-1084 collaboration |
| Oct 2023–Apr 2024 | Atera Agent campaign — MENA broad targeting; BugSleep debut |
| Oct 2023 | DCHSpy Android surveillanceware deployed within days of Hamas attack |
| Jul 2024 | BugSleep campaign — expanded Israeli targeting |
| Oct 2025 | Phoenix backdoor campaign — 100+ MENA government entities |
| Feb 2026 | Operation Olalampo — CHAR/GhostFetch toolkit; Telegram C2 |
| Feb–Mar 2026 | Dindoor/Fakeset campaign — **US banks, airports, defense-adjacent orgs** |
| Feb–Mar 2026 | Stryker attack (access phase) — MuddyWater pre-positions; Handala destroys via Intune |
| Mar 6, 2026 | sdrhi MOIS operational server exposed at 157.20.182.49 — continued operations regardless |
| Mar–Apr 2026 | ChainShell / CastleRAT campaign — blockchain C2; Russian MaaS procurement |

---

## References

- [CISA Joint Advisory AA22-055A — MuddyWater Attribution](https://www.cisa.gov/news-events/cybersecurity-advisories/aa22-055a)
- [MITRE ATT&CK — MuddyWater (G0069)](https://attack.mitre.org/groups/G0069/)
- [Trellix — The Iranian Cyber Capability 2026](https://www.trellix.com/blogs/research/the-iranian-cyber-capability-2026/)
- [ThreatHunter.ai — Stryker, Handala, MOIS, and MuddyWater: The Full Kill Chain](https://www.threathunter.ai/blog/stryker-handala-mois-muddywater-unified-brief-detection-pack-v3/)
- [Cryptika / CyberSecurityNews — MuddyWater Turns to Russian MaaS (ChainShell Campaign)](https://cybersecuritynews.com/muddywater-turns-to-russian-malware-as-a-service/)
- [Ankura CTIX — MuddyWater Dindoor/Fakeset Campaign](https://ankura.com/insights/ankura-ctix-flash-update-march-24-2026)
- [Wikipedia — MuddyWater](https://en.wikipedia.org/wiki/MuddyWater_(hacker_group))
- [SecurityScientist.net — 12 Questions About MuddyWater](https://www.securityscientist.net/blog/12-questions-and-answers-about-muddywater/)
