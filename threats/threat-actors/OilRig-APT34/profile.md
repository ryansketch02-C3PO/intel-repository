# OilRig (APT34 / Hazel Sandstorm / Earth Simnavaz)

## Identity

| Field | Details |
|---|---|
| **Primary Alias** | OilRig |
| **Other Names** | APT34 · Hazel Sandstorm (Microsoft) · Earth Simnavaz (Trend Micro) · Helix Kitten (CrowdStrike) · Crambus · Cobalt Gypsy · EUROPIUM · Evasive Serpens · GreenBug · IRN2 · TA452 · Twisted Kitten |
| **C3PO Designation** | Scoundrel #037 |
| **Type** | State-Sponsored APT — Espionage |
| **Sponsor** | Iran — Ministry of Intelligence and Security (MOIS) |
| **Active Since** | 2014 |
| **Status** | 🔴 ACTIVE — Ongoing campaigns confirmed through Q1 2026 |
| **Threat Level** | 🔴 HIGH |
| **Primary Motivation** | Cyber espionage · Intelligence collection · Strategic positioning |
| **Sector Focus** | Government · Energy (Oil & Gas) · Defense · Telecommunications · Finance · Critical Infrastructure |
| **Geographic Focus** | Middle East (Iraq, UAE, Saudi Arabia, Israel, Jordan, Kuwait) · US · UK · Europe |
| **ATT&CK Group ID** | G0049 |
| **ATT&CK** | T1566.001 · T1071.004 · T1071.003 · T1505.003 · T1556.002 · T1068 · T1003 · T1059.001 · T1036 · T1078 · T1048 |
| **Admiralty Grade** | A1 — Confirmed by FireEye/Mandiant, Trend Micro, Check Point, ESET, CrowdStrike, Microsoft, US CISA/FBI |

---

## Overview

OilRig is Iran's most technically sophisticated and persistent espionage group, operating on behalf of the Ministry of Intelligence and Security (MOIS) since at least 2014. Where other Iranian groups (Charming Kitten, UNC1549) pursue immediate disruption or targeted individual access, OilRig plays the long game — establishing durable footholds inside government ministries, energy companies, and defense contractors, then quietly harvesting credentials and intelligence for months or years before being detected.

The group's signature is **infrastructure patience and protocol creativity**. While most APTs tunnel C2 through HTTPS, OilRig has pioneered DNS tunneling with custom Base32 encoding, email-based C2 through compromised victim mailboxes (commands arrive as legitimate-looking emails from inside the organization), and IIS backdoors that blend silently into normal web server traffic. In 2024, they demonstrated a new trick: using compromised Iraqi government email accounts as the actual C2 channel — meaning their malware receives instructions via the victim's own Microsoft Exchange infrastructure. Detection becomes nearly impossible without endpoint telemetry.

**OilRig is the quiet professional of Iranian cyber operations.** They're not dropping wipers or defacing websites. They're building persistent access to strategic intelligence targets and harvesting everything that matters to Iranian national security: diplomatic cables, energy pricing data, defense contracts, government personnel records.

The name comes from their heavy early focus on Middle Eastern oil and gas companies — fitting for a group that has likely read more Saudi Aramco strategy documents than most of Aramco's own employees.

---

## Sponsor & Attribution

| Attribute | Assessment |
|---|---|
| **Parent Agency** | MOIS — Ministry of Intelligence and Security (وزارت اطلاعات) |
| **Confidence** | A1 — Multiple US/EU government attributions; DoJ indictments; extensive technical overlap |
| **DoJ Indictments** | Multiple individuals indicted for OilRig-attributed activity (2019, 2021) |
| **OFAC Sanctions** | Multiple OilRig operators under US Treasury OFAC sanctions |
| **Relationship to IRGC** | MOIS ≠ IRGC. OilRig serves civilian intelligence (MOIS), not the Revolutionary Guard — distinct from Charming Kitten (IRGC-IO), APT33 (IRGC), and UNC1549 (IRGC). MOIS and IRGC do coordinate on some targets. |
| **Shared Overlap** | Infrastructure and targeting overlaps observed with MuddyWater — also MOIS-attributed |

---

## Campaigns & Operations

### 2024 — Iraq Government Intrusions (Veaty/Spearal)

**Period:** September 2024 onwards
**Targets:** Iraqi Prime Minister's Office, Ministry of Foreign Affairs, government ministries
**Source:** Check Point Research, September 2024

OilRig conducted a sophisticated multi-stage intrusion against Iraqi government entities deploying two new malware families: **Veaty** and **Spearal**.

- **Spearal** — .NET backdoor using DNS tunneling for C2. Data is encoded into DNS query subdomains using a custom Base32 scheme and exfiltrated as DNS queries to attacker-controlled resolvers
- **Veaty** — .NET backdoor using email-based C2. Commands arrive via **compromised mailboxes within the target organization's own gov-iq.net domain** — the malware polls a compromised government mailbox for instructions, making C2 traffic indistinguishable from normal internal email

The attack chain began with lure files masquerading as benign documents (`Avamer.pdf.exe`, `IraqiDoc.docx.rar`). Execution triggered intermediate PowerShell or PyInstaller scripts that dropped Veaty/Spearal executables plus XML configuration files specifying C2 parameters.

A **third SSH tunneling backdoor** and the **CacheHttp.dll IIS backdoor** were also identified during the campaign.

The email C2 channel represents a significant tradecraft evolution — victim's own Exchange infrastructure becomes the C2 server, defeating network-layer C2 blocking.

---

### 2024 — UAE/Gulf Campaign (STEALHOOK + CVE-2024-30088)

**Period:** Mid–Late 2024
**Targets:** UAE and Gulf region government entities and energy sector organizations
**Source:** Trend Micro (Earth Simnavaz research), October 2024

OilRig (tracked as Earth Simnavaz by Trend Micro) targeted governmental organizations in the UAE and broader Gulf region using a newly identified backdoor: **STEALHOOK**.

**Attack chain:**
1. Web shell deployed on vulnerable internet-facing web server (initial access)
2. `ngrok` RMM tool downloaded — used to create covert tunnels bypassing perimeter security and enable lateral movement
3. **CVE-2024-30088** (Windows Kernel Elevation of Privilege — CVSS 7.0 · TOCTOU race condition) exploited via binary loaded in-memory with `RunPE-In-Memory`
4. **Password filter DLL** registered with LSA — captures plaintext passwords for every domain user who changes their password going forward
5. **STEALHOOK** backdoor deployed — retrieves harvested credentials and exfiltrates them via Exchange server as email attachments to an attacker-controlled address

Key evasion: customized .NET tools, PowerShell scripts, and IIS-based malware were designed specifically to blend with normal network traffic. Legitimate credentials and ngrok tunnels make traffic appear authorized.

---

### 2025 — Iraq C# Campaign (Dual-Channel C2)

**Period:** Late 2024 – Early 2025
**Targets:** Iraqi government entities, energy, finance, defense, telecom
**Source:** ThreatBook / CERTFA, March 2025

A follow-on campaign against Iraq deployed new **C# malware disguised as PDF documents** with significantly enhanced evasion:
- Anti-virtual machine checks
- Timestamp manipulation to complicate forensic timeline analysis
- **Dual C2 channels**: HTTP communications routed through European-hosted servers behind fake 404 error pages + SMTP/IMAP via compromised Iraqi government email accounts
- Commands concealed inside **Authorization Bearer tokens** in HTTP headers — malicious commands hide inside what looks like standard authentication traffic

---

### 2024–2025 — Infrastructure Pre-Staging (UK/Iraq Cover)

**Period:** November 2024 – April 2025
**Source:** Intertec Systems passive DNS analysis, 2025

Researchers tracked OilRig pre-operational infrastructure staging before any malware deployment:
- Domain `biam-iraq[.]org` impersonated an Iraqi academic institution
- Multiple fabricated UK technology company domains registered
- All hosted on **M247** infrastructure with consistent SSH fingerprint reuse across 5 servers
- Servers responded on port 8080 with a **static 404 "Document" page (Content-Length: 338 bytes)** — a templated APT34 server configuration now recognized as a detection signature
- Cluster of algorithmically generated `.eu` domains (plenoryvantyx[.]eu, zyverantova[.]eu, etc.) converged on shared infrastructure

This reveals OilRig's deliberate long-horizon planning — infrastructure prepared months in advance of any intrusion.

---

### Historical Notable Operations

| Operation | Year | Target | Tool |
|---|---|---|---|
| DNSpionage Campaign | 2018–2019 | Lebanon, UAE government | DNS hijacking + DNSExfiltrator |
| Operation Saffron Rose | 2014 | US defense contractors, Iranian dissidents | Credential phishing |
| Operation Outer Space | 2021 | Israeli organizations | Solar backdoor + VBS dropper |
| Operation Juicy Mix | 2022 | Israeli organizations | Mango backdoor + SC5k loader |
| SideWinder Supply Chain | 2021–2022 | Multiple | SideTwist / Saitama / MrPerfectionManager |

---

## Malware Arsenal

OilRig maintains one of the most extensive and frequently refreshed custom malware portfolios of any Iranian APT. New implants are routinely introduced per campaign while older tools are retired or refactored.

| Malware | Type | C2 Method | First Seen | Notes |
|---|---|---|---|---|
| **Veaty** | .NET backdoor | Email (compromised victim mailboxes) | 2024 | Downloads files, executes commands via target's own Exchange |
| **Spearal** | .NET backdoor | DNS tunneling (custom Base32) | 2024 | Encodes data in DNS query subdomains |
| **STEALHOOK** | Credential exfiltration | Exchange (email attachments to attacker) | 2024 | Harvests domain credentials via password filter DLL |
| **CacheHttp.dll** | IIS backdoor | HTTP (OnGlobalPreBeginRequest events) | 2024 | Hides in IIS web server process; evolution of RGDoor |
| **Menorah** | Backdoor | HTTP | 2023 | Based on SideTwist; host fingerprinting, file listing, shell command exec |
| **PowerExchange** | Exchange backdoor | Exchange Web Services API | 2023 | Monitors mailbox for commands embedded in email subjects |
| **MrPerfectionManager** | .NET backdoor | Email (SMTP/IMAP) | 2022 | Commands delivered via email; uploads via email attachments |
| **Saitama** | DNS tunneling agent | DNS | 2022 | Disguised as Outlook update |
| **Mango** | C#/.NET backdoor | HTTP + Exchange API | 2022 | Operation Juicy Mix (Israeli targets) |
| **Solar** | C#/.NET backdoor | HTTP + Exchange API | 2021 | Operation Outer Space (Israeli targets) |
| **SideTwist** | Backdoor | HTTP | 2021 | Parent of Menorah |
| **Shark** | Backdoor | DNS + HTTPS tunneling | 2020 | |
| **Marlin** | Backdoor | IMAP | 2020 | Uses IMAP4 for C2 communication |
| **RDAT** | Backdoor | HTTP + DNS + Exchange | 2018–2020 | Multi-protocol; first observed during DNSpionage |
| **Karkoff** | Backdoor | Email | 2018 | Email-based C2; predecessor to MrPerfectionManager |
| **TONEDEAF** | Backdoor | HTTP/HTTPS | 2019 | Multi-operation use |
| **LONGWATCH** | Keylogger | Local storage | 2017+ | Data staged for exfil by other tools |
| **RGDoor** | IIS backdoor | HTTP | 2017 | Predecessor to CacheHttp.dll |
| **DNSExfiltrator** | DNS exfil tool | DNS | 2018–2019 | DNSpionage campaign |

---

## Tactics, Techniques & Procedures (TTPs)

### Signature Techniques

**1. Compromised Email Infrastructure as C2**
OilRig's most distinctive technique: deploying backdoors that use the **victim's own email accounts** to receive commands. The attacker sends commands to a compromised mailbox inside the target organization — the malware polls the inbox and executes instructions. Traffic is indistinguishable from normal internal email at the network layer. Implemented across: Karkoff, MrPerfectionManager, PowerExchange, Veaty.

**2. DNS Tunneling with Custom Protocols**
Multiple custom DNS tunneling implementations across successive tool generations (RDAT, Spearal, Saitama). Each uses proprietary encoding schemes (Base32, Base64 variants) embedded in DNS query subdomains — not Iodine or standard tunneling tools, which are better detected.

**3. Password Filter DLL Registration**
Registering a malicious DLL with the Windows LSA (Local Security Authority) that implements password filter callbacks. Every time any domain user changes their password, the DLL captures the new plaintext password before hashing and exfiltrates it. Silently harvests credentials at the domain level over time.

**4. IIS Backdoors**
Persistent backdoors embedded as IIS modules that monitor all incoming HTTP requests for embedded commands. Malicious traffic is hidden within normal web requests. The evolution from RGDoor (2017) to CacheHttp.dll (2024) demonstrates consistent investment in this technique.

**5. Templated Infrastructure Fingerprint**
Static 404 "Document" page served on port 8080 with fixed Content-Length of 338 bytes. SSH fingerprint reuse across staging servers. Consistent domain registration patterns (P.D.R. Solutions registrar, regway[.]com nameservers). These are now reliable detection signatures.

### Full ATT&CK Mapping

| Tactic | ID | Technique |
|---|---|---|
| Initial Access | T1566.001 | Spearphishing Attachment |
| Initial Access | T1566.002 | Spearphishing Link |
| Initial Access | T1190 | Exploit Public-Facing Application (webshell via vulnerable web server) |
| Execution | T1059.001 | PowerShell |
| Execution | T1059.003 | Windows Command Shell |
| Execution | T1059.006 | Python (PyInstaller-wrapped payloads) |
| Execution | T1106 | Native API |
| Persistence | T1505.003 | Web Shell |
| Persistence | T1505.004 | IIS Components (CacheHttp.dll, RGDoor) |
| Persistence | T1053.005 | Scheduled Task |
| Persistence | T1098 | Account Manipulation |
| Privilege Escalation | T1068 | Exploitation for Privilege Escalation (CVE-2024-30088) |
| Privilege Escalation | T1556.002 | Password Filter DLL |
| Defense Evasion | T1036 | Masquerading (PDF-named executables) |
| Defense Evasion | T1027 | Obfuscated Files or Information |
| Defense Evasion | T1562.001 | Anti-VM Checks |
| Defense Evasion | T1070 | Indicator Removal |
| Defense Evasion | T1078 | Valid Accounts (stolen credentials) |
| Credential Access | T1003 | Credential Dumping (Mimikatz, LaZagne) |
| Credential Access | T1556.002 | Password Filter DLL (persistent credential capture) |
| Credential Access | T1110.003 | Password Spray |
| Discovery | T1082 | System Information Discovery |
| Discovery | T1083 | File and Directory Discovery |
| Discovery | T1016 | System Network Configuration Discovery |
| Lateral Movement | T1021.001 | Remote Desktop Protocol |
| Lateral Movement | T1021.002 | SMB |
| Collection | T1114 | Email Collection (Exchange) |
| Collection | T1560 | Archive Collected Data |
| Command & Control | T1071.004 | DNS (custom Base32 tunneling) |
| Command & Control | T1071.003 | Mail Protocols (SMTP/IMAP via compromised victim accounts) |
| Command & Control | T1090 | Proxy (ngrok tunnel) |
| Command & Control | T1132 | Data Encoding (Base32/Base64 in DNS subdomains) |
| Exfiltration | T1041 | Exfiltration Over C2 Channel |
| Exfiltration | T1048 | Exfiltration Over Alternative Protocol (DNS) |
| Exfiltration | T1048.003 | Exfiltration Over Unencrypted Non-C2 Protocol |

---

## Connections to Other Tracked Actors

| Actor | Relationship | Notes |
|---|---|---|
| **MuddyWater (#022)** | Same parent agency (MOIS) | Both MOIS-attributed; targeting overlap in Middle East; documented infrastructure overlap; different operational teams |
| **Charming Kitten (#011)** | Iran nexus — different parent agency | Charming Kitten = IRGC-IO; OilRig = MOIS. Both target defense researchers and government personnel; some coordination on shared targets |
| **APT33 (#024)** | Iran nexus — different parent agency | APT33 = IRGC; OilRig = MOIS. Both target energy sector (OilRig: espionage; APT33: access pre-position for destructive). Share targeting overlap in Middle East energy |
| **UNC1549 (#004)** | Iran nexus — IRGC vs MOIS | Both target aerospace/defense in Israel and broader Middle East. Complementary rather than competitive; likely serve different intelligence consumers |
| **Handala Hack (#014)** | Iran nexus — different mandate | Handala = destructive/hacktivist proxy. OilRig = durable espionage. Both target Israel and Gulf states. When OilRig prepares access, Handala's disruptive ops sometimes follow in same target set |

---

## Defensive Recommendations

| Priority | Action |
|---|---|
| 🔴 CRITICAL | **Monitor Exchange for unauthorized API access** — OilRig's email C2 is nearly invisible at network level; endpoint/Exchange telemetry is the only reliable detection path for PowerExchange, Veaty, MrPerfectionManager |
| 🔴 CRITICAL | **Hunt for password filter DLL registration** — Monitor `HKLM\SYSTEM\CurrentControlSet\Control\Lsa\Notification Packages`; any unexpected DLL = likely compromise |
| 🔴 HIGH | **Enable DNS query logging + anomaly detection** — Long DNS subdomains (>30 chars), high query rate to uncommon domains, Base32-pattern subdomains are Spearal/Saitama IOCs |
| 🔴 HIGH | **Restrict/audit ngrok and similar tunneling tools** — Block `*.ngrok.io` and `*.ngrok-free.app` at perimeter; flag any ngrok process as high-priority alert |
| 🔴 HIGH | **Patch CVE-2024-30088** (Windows Kernel EoP — June 2024 Patch Tuesday) — actively exploited by OilRig for privilege escalation |
| 🟡 HIGH | **Audit IIS modules** — OilRig's IIS backdoors register as legitimate modules; periodic audit of `%windir%\System32\inetsrv\config\ApplicationHost.config` for unexpected entries |
| 🟡 MEDIUM | **Block/alert on port 8080 HTTP responses with Content-Length 338** — OilRig infrastructure detection signature (as of March 2025) |
| 🟡 MEDIUM | **Monitor M247 ASN traffic** — Repeated use of M247 hosting for staging infrastructure |
| 🟡 MEDIUM | **YARA hunt for Veaty/Spearal XML config files** — XML config files with DNS C2 server specifications are left in predictable locations |

---

## Activity Timeline

| Date | Event |
|---|---|
| 2014 | Group first observed; early campaigns targeting Middle East energy and finance |
| 2017 | RGDoor IIS backdoor introduced; LONGWATCH keylogger observed |
| 2018–2019 | DNSpionage campaign — Lebanon + UAE; DNS hijacking of government domains |
| 2019 | Operation SolarDeflection; US DoJ indictments for OilRig-attributed activity |
| 2021 | Operation Outer Space — Israeli targets; Solar backdoor |
| 2022 | Operation Juicy Mix — Israeli targets; Mango + SC5k loader |
| 2023 | Menorah malware discovered; PowerExchange backdoor targeting UAE government Exchange servers |
| Sep 2024 | Iraq government campaign: Veaty + Spearal; email C2 via compromised gov mailboxes |
| Oct 2024 | UAE/Gulf campaign: STEALHOOK + CVE-2024-30088 + password filter DLL; Earth Simnavaz designation by Trend Micro |
| Nov 2024–Apr 2025 | Pre-operational infrastructure staging: Iraqi/UK cover domains on M247; SSH fingerprint reuse pattern identified |
| Early 2025 | Iraq C# campaign: anti-VM malware, dual HTTP+email C2, Bearer token obfuscation |
| 2026 | Ongoing operations assessed per Trellix Iranian Cyber Capability report (April 2026) |

---

## References

- [FireEye / Mandiant — APT34: New Targeted Attack in the Middle East (2017)](https://www.mandiant.com/resources/apt34-new-targeted-attack-middle-east)
- [Check Point Research — Iranian Threat Actor OilRig Targets Iraqi Government (Sep 2024)](https://research.checkpoint.com/2024/iranian-threat-actor-oilrig-targets-iraqi-government/)
- [Trend Micro — Earth Simnavaz (APT34) Levies Advanced Cyberattacks Against UAE and Gulf Regions (Oct 2024)](https://www.trendmicro.com/en_us/research/24/j/earth-simnavaz-cyberattacks.html)
- [CERTFA Radar — APT34 Targets Iraqi Government with Dual-Channel C2 (Mar 2025)](https://radar.certfa.com/en/threats/view/55c66e5b/)
- [Intertec Systems — Uncover APT34-Like Infrastructure Before It Becomes a Threat (2025)](https://www.intertecsystems.com/threat-report-and-advisories/malware/uncover-apt34-like-infrastructure-before-it-becomes-a-threat/)
- [Trellix — The Iranian Cyber Capability 2026 (Apr 2026)](https://www.trellix.com/blogs/research/the-iranian-cyber-capability-2026/)
- [ESET — OilRig Operations Outer Space and Juicy Mix (2022)](https://www.eset.com/int/about/newsroom/press-releases/research/eset-research-unveils-new-oilrig-espionage-campaigns/)
- [SC World — New Menorah Malware bolsters OilRig's cyberespionage efforts (2023)](https://www.scworld.com/brief/new-menorah-malware-bolsters-oilrig-apts-cyberespionage-efforts)
- [Malpedia — OilRig actor profile](https://malpedia.caad.fkie.fraunhofer.de/actor/oilrig)
- [MITRE ATT&CK — G0049 OilRig](https://attack.mitre.org/groups/G0049/)

---

*Profile created: 2026-05-08 | Author: C3PO | Admiralty Grade: A1 | TLP: WHITE*
