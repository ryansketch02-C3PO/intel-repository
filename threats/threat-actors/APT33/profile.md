# APT33 — Peach Sandstorm / Elfin / Refined Kitten

## Identity

| Field | Detail |
|---|---|
| **Primary Name** | APT33 |
| **Aliases** | Peach Sandstorm (Microsoft), Elfin (Symantec/Broadcom), Refined Kitten (CrowdStrike), HOLMIUM (Microsoft legacy), MAGNALLIUM (Dragos), Cobalt Trinity (SecureWorks), TA451 (Proofpoint), ATK 35 (Thales), G0064 (MITRE) |
| **Origin** | Iran |
| **Sponsor** | Islamic Revolutionary Guard Corps (IRGC) |
| **Active Since** | 2013 (publicly documented 2017 by FireEye/Mandiant) |
| **Scoundrel #** | 024 |
| **Threat Level** | 🔴 HIGH |
| **MITRE ATT&CK** | [G0064](https://attack.mitre.org/groups/G0064/) |

---

## Mission

APT33 is Iran's aerospace and defence specialist. Where Charming Kitten (#011) hunts journalists and dissidents, APT33 hunts **jet engines, satellite systems, missile technology, and petrochemical IP**. Every target maps directly to what Iran cannot acquire through legitimate channels under international sanctions. The group operates as a dual-purpose instrument of Iranian statecraft: intelligence collection today, potential destructive sabotage tomorrow.

Primary objectives:
1. Steal aerospace, defence, and energy IP to advance Iran's domestic military and industrial programmes
2. Map critical infrastructure networks for potential future destructive operations
3. Gather strategic intelligence on rival nations' military and industrial capabilities (especially Saudi Arabia and the US)

---

## Target Profile

**Primary sectors (highest priority):**
- ✈️ Aerospace and aviation (commercial and military)
- 🛰️ Satellite and space systems
- 🏭 Defence contractors and DIB (Defence Industrial Base)
- ⚡ Energy, oil, gas, and petrochemicals

**Secondary sectors:**
- Government agencies
- Education (used as infrastructure procurement vector — not the end target)
- Pharmaceuticals
- Telecommunications

**Primary geographies:**
- 🇺🇸 United States
- 🇸🇦 Saudi Arabia
- 🇦🇪 United Arab Emirates
- 🇰🇷 South Korea
- 🇬🇧 United Kingdom / Western Europe

**Note on education targeting:** APT33 compromises university accounts specifically to create fraudulent Azure "for Students" subscriptions, which are then used as C2 infrastructure for attacks against their actual high-value aerospace/defence targets. Compromise of a university is not the end goal — it is a logistics step.

---

## Evolution of TTPs

### Phase 1: Spearphishing Era (2013–2022)

Initial access via job-recruitment-themed spearphishing emails. Lures impersonated Boeing and other aerospace employers. Delivered malicious `.hta` (HTML Application) files and weaponised `.rar` archives. Key CVEs exploited:
- **CVE-2017-11774** — Microsoft Outlook Home Page abuse (CVSS 7.8, on CISA KEV)
- **CVE-2018-20250** — WinRAR ACE path traversal (CVSS 7.8, on CISA KEV)

Custom malware from this era:
- **TURNEDUP** — primary custom backdoor, linked to "xman_1365_x" handle and Iran's Nasr Institute
- **DROPSHOT** — dropper that delivers either TURNEDUP (espionage) or SHAPESHIFT (destruction)
- **SHAPESHIFT / STONEDRILL** — disk wiper with in-memory injection and anti-emulation; shares code with Shamoon; not directly observed deployed but staged capability

### Phase 2: Cloud-First Pivot (2023–present)

A "paradigm shift" per Microsoft. Password spraying has replaced spearphishing as the primary initial access vector. Attackers spray thousands of Microsoft 365 and Azure Active Directory (Entra ID) tenants simultaneously, using TOR exit nodes for anonymisation.

**Signature indicator:** `go-http-client` user agent in authentication logs

Post-compromise cloud TTPs:
- **AzureHound** and **Roadtools** for cloud environment reconnaissance
- **Golden SAML attacks** — steal/abuse AD FS token-signing keys to forge authentication tokens, bypassing MFA
- **Fraudulent Azure subscriptions** created under compromised education accounts for C2 hosting — malicious traffic blends with legitimate Microsoft cloud activity
- **AD Explorer** (Sysinternals) for Active Directory snapshots
- **AnyDesk** for RMM-based persistence
- **Tickler backdoor** (2024) — multi-stage C/C++ malware, persists as `SharePoint.exe` in Run registry key, C2 over attacker-controlled Azure
- **FalseFont backdoor** (2023) — deployed against DIB targets post-password-spray compromise

### Why this matters for A&D

APT33's cloud-native model is specifically designed to evade perimeter-based defences. A defence contractor running standard network monitoring won't see a Microsoft Azure authentication request from an APT33-controlled Azure tenant as anomalous. The group is operating at the identity and cloud layer — exactly where most legacy security tooling has the least visibility.

---

## MITRE ATT&CK Techniques

| Tactic | Technique | Detail |
|---|---|---|
| Initial Access | T1110.003 — Password Spraying | Mass spray against M365/Azure AD; `go-http-client` UA; TOR-anonymised |
| Initial Access | T1566 — Phishing | Job-lure spearphishing with .hta and archive attachments (legacy) |
| Initial Access | T1078.004 — Valid Accounts: Cloud | Post-spray use of compromised cloud credentials |
| Execution | T1059.001 — PowerShell | POWERTON backdoor; payload delivery and reverse shells |
| Persistence | T1547.001 — Registry Run Keys | Tickler persists as `SharePoint.exe` in Run key |
| Persistence | T1053.005 — Scheduled Task | Legacy persistence for TURNEDUP and payloads |
| Credential Access | T1110.003 — Password Spraying | Primary initial access method |
| Credential Access | T1003 — Credential Dumping | Mimikatz, LaZagne, ProcDump against LSASS |
| Credential Access | T1550.003 — Web Session Cookie | Golden SAML token abuse for MFA bypass |
| Discovery | T1087 — Account Discovery | AD Explorer snapshots post-compromise |
| Discovery | T1046 — Network Service Discovery | Cloud environment mapping via AzureHound/Roadtools |
| Lateral Movement | T1021.002 — SMB/Windows Admin Shares | SMB for lateral movement post-compromise |
| C2 | T1071.001 — Web Protocols | HTTP C2 on non-standard ports (808, 880); Azure-hosted C2 |
| Exfiltration | T1048.003 — Exfil over FTP | Unencrypted FTP for large data transfers |
| Impact | T1485 — Data Destruction | SHAPESHIFT wiper capability (staged, not yet broadly deployed) |

---

## Malware & Tools

| Tool | Type | Notes |
|---|---|---|
| **Tickler** | Custom backdoor (2024) | Multi-stage C/C++; `SharePoint.exe` persistence; Azure C2; current primary tool |
| **FalseFont** | Custom backdoor (2023) | Deployed against DIB targets; mimics HR software |
| **TURNEDUP** | Custom backdoor (2013+) | Source code linked to Nasr Institute; "xman_1365_x" PDB handle |
| **DROPSHOT** | Custom dropper | Delivers TURNEDUP or SHAPESHIFT; APT33-exclusive |
| **SHAPESHIFT / STONEDRILL** | Disk wiper | In-memory injection; Shamoon code similarities; destructive capability |
| **POWERTON** | PowerShell backdoor | In-memory execution; persistent remote access |
| **DarkComet, Remcos, QuasarRAT, NanoCore, Pupy** | Commodity RATs | Used in 2018–2019 for deniability |
| **Mimikatz, LaZagne** | Credential tools | LSASS dumping, credential harvesting |
| **AzureHound, Roadtools** | Cloud recon tools | Azure AD / Entra ID environment mapping |
| **AnyDesk** | Legitimate RMM | Used for persistent remote access post-compromise |
| **AD Explorer** | Legitimate sysadmin tool | Active Directory snapshots |
| **Rclone** | Exfil tool | Data staging and exfiltration |

---

## Infrastructure

- **C2 hosting:** Attacker-controlled Azure subscriptions (created via compromised education accounts)
- **Domain masquerading:** Typosquatted domains impersonating Boeing and other aerospace employers (e.g., `boeing.servehttp[.]com`)
- **TOR exit nodes:** Used to anonymise password spray origins
- **azurewebsites[.]net** subdomains observed in Tickler C2 activity

---

## Detection Opportunities

1. **Authentication logs:** `go-http-client` user agent in Azure AD / Entra ID sign-in logs — high-confidence APT33 indicator
2. **Failed authentication spikes:** Multiple accounts showing failed login attempts from a narrow IP range — password spray signature
3. **Golden SAML:** Monitor AD FS token-signing certificate access; alert on SAML assertion issuance from unusual sources
4. **AzureHound / Roadtools execution:** EDR detections for these cloud recon tools
5. **SharePoint.exe in Run registry key:** Not a legitimate Microsoft binary — high-fidelity Tickler indicator
6. **AnyDesk deployments** not approved by IT — persistence mechanism

---

## Connection Web

| Actor | Relationship |
|---|---|
| **APT34 / OilRig (#TBD)** | Shares infrastructure; fellow IRGC-linked group; different focus (Middle East gov/energy) |
| **APT35 / Charming Kitten (#011)** | Iranian sibling group; APT35 targets individuals/dissidents; APT33 targets organisations |
| **MuddyWater / Mango Sandstorm (#022)** | MOIS-linked (different Iranian agency — Ministry of Intelligence vs IRGC); overlapping target sectors |
| **UNC1549 (#004)** | Overlapping aerospace/defence targeting; LinkedIn social engineering shared TTP |
| **Handala Hack / Void Manticore (#014)** | Fellow Iranian actor; Handala focuses on destructive ops against Israel specifically |

---

## Assessment

APT33 is the highest-priority Iranian cyber threat to the aerospace and defence sector. With the current Iran-US conflict escalating and the ceasefire expiring April 21, 2026, APT33 is assessed at **elevated operational tempo**. Their cloud-first attack model is purpose-built to evade legacy perimeter defences, and their destructive capability (SHAPESHIFT) provides Iran with the option to escalate from espionage to infrastructure sabotage with minimal retooling.

Any organisation in the US/allied aerospace, satellite, defence, or energy supply chain should treat APT33 as an active and specific threat — not a theoretical one.

---

*Profile created: 2026-04-19 | Last updated: 2026-04-19*
*Sources: Mandiant, Microsoft MSTIC, Symantec, CrowdStrike, Dragos, Picus Security, Hedgehog Security, BrandDefense, ThreatIntelReport, MITRE ATT&CK*
