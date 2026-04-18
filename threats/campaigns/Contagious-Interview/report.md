# 🎭 Campaign Report: Contagious Interview

**Campaign ID:** CL-STA-0240  
**Status:** 🔴 ACTIVE — ongoing as of late 2025  
**First Seen:** December 2022 (activity confirmed; first public documentation November 2023)  
**Last Seen:** November 2025 (197 new malicious npm packages published; campaign still evolving)  
**Last Updated:** 2026-04-15  
**Admiralty Grade:** A1 — multiple corroborating intelligence sources including Unit 42, SentinelOne, Sekoia, NTT Security, Cisco Talos, Silent Push, Socket Research  
**TLP:** WHITE

---

## 📋 Quick Reference

| Field | Value |
|---|---|
| **Campaign Name** | Contagious Interview |
| **Also Known As** | CL-STA-0240, DeceptiveDevelopment, DEV#POPPER, Famous Chollima, Tenacious Pungsan, Nickel Tapestry, UNC5267, UNC5342, WaterPlum, PurpleBravo, Storm-0287, Jasper Sleet, Void Dokkaebi, Gwisin Gang |
| **Attribution** | Lazarus Group (DPRK) — Reconnaissance General Bureau (RGB), 3rd Department |
| **Nation-State Sponsor** | North Korea (DPRK) |
| **First Seen** | December 2022 |
| **Last Seen** | November 2025 (active) |
| **Primary Motivation** | Financial gain — cryptocurrency theft to fund DPRK sanctions evasion and weapons programs |
| **Secondary Motivation** | Espionage, corporate IP theft, credential harvesting |
| **Target Sectors** | Cryptocurrency, DeFi/CeFi, Web3/Blockchain developers, Software engineers, IT professionals |
| **Target Platforms** | Windows, macOS, Linux |
| **MITRE ATT&CK Group** | G1052 (Contagious Interview) |

---

## 📖 Description

Contagious Interview is a sustained, financially-motivated cyber campaign attributed with high confidence to North Korea's Lazarus Group operating under the DPRK's Reconnaissance General Bureau (RGB). Active since at least December 2022 and publicly documented first by Palo Alto Unit 42 in November 2023, the campaign targets software developers, engineers, and — increasingly since 2024 — non-technical cryptocurrency industry employees including business development managers, marketing professionals, and finance personnel.

The campaign's core mechanic is **fake job recruitment**: threat actors pose as employers or recruiters on LinkedIn, X (formerly Twitter), GitHub, and job platforms, inviting targets to participate in a technical interview or skills assessment. During this process, victims are socially engineered into downloading and executing malware-laced code — either a backdoored GitHub project, a trojanized video conferencing application, or via the **ClickFix technique** (introduced in 2024), where a fabricated error message instructs victims to copy and paste a malicious `curl` command into their terminal.

The campaign has proven extraordinarily persistent and adaptive. Over three years it has evolved through multiple malware generations, delivery mechanisms, and target demographics. It shows no signs of slowing — in November 2025, 197 new malicious npm packages were published in a single campaign wave, achieving over 31,000 downloads.

The ultimate objective is **cryptocurrency theft**: extracting wallet credentials, seed phrases, browser-stored keys, and exchange login data from victims' systems. Proceeds directly fund North Korea's weapons programs and help the regime evade international sanctions. Chainalysis estimates DPRK threat actors stole $1.3 billion in cryptocurrency in 2024 alone, up from $660.5 million in 2023. The March 2025 Bybit theft of $1.5 billion — the largest crypto heist in history — is attributed to Lazarus and connected to this campaign cluster's infrastructure.

---

## 🕰️ Campaign Timeline

| Period | Key Development |
|---|---|
| **Dec 2022** | Campaign activity begins; initial targeting of software developers via fake GitHub projects |
| **Nov 2023** | First public documentation by Palo Alto Unit 42; BeaverTail (JS) + InvisibleFerret (Python) identified as primary toolchain |
| **Jul 2024** | New Qt-based cross-platform BeaverTail variant observed; compiled for Windows and macOS from same source code |
| **Sep 2024** | OtterCookie malware first observed in the wild (NTT Security attribution); Socket.IO C2 protocol |
| **Nov 2024** | ClickFix technique introduced to campaign; FriendlyFerret, FrostyFerret, FlexibleFerret variants documented (SentinelOne) |
| **Dec 2024** | macOS FlexibleFerret variants identified; campaign confirmed still active |
| **Jan–Mar 2025** | 230+ confirmed victims in 3-month window (SentinelOne/Validin); threat actors observed actively monitoring CTI platforms |
| **Feb 2025** | Sekoia identifies "ClickFake Interview" sub-campaign using GolangGhost backdoor; targeting shifts to non-technical crypto roles |
| **Mar 2025** | Bybit ($1.5B heist) linked to Lazarus/Contagious Interview infrastructure; Silent Push exposes three DPRK front companies (BlockNovas LLC, Angeloper Agency, SoftGlide LLC) |
| **Apr 2025** | Three front companies formally documented; BeaverTail + InvisibleFerret + OtterCookie all deployed from same infrastructure |
| **May 2025** | Cisco Talos documents Python-variant GolangGhost RAT; OtterCookie v3 adds Windows support |
| **Jun–Jul 2025** | 35 then 67 new malicious npm packages added; campaign described as "factory-scale" operation |
| **Aug 2025** | OtterCandy new malware variant first seen; DiggingBeaver loader introduced |
| **Sep–Oct 2025** | OtterCookie v5 merges BeaverTail functionality; keylogging + screenshot module added; Cisco Talos documents convergence |
| **Nov 2025** | 197 new malicious npm packages published; 31,000+ downloads confirmed; stardev0914 GitHub account infrastructure mapped (Socket Research) |

---

## 🎯 Targeting

### Primary Targets
- **Software developers** — particularly those working in cryptocurrency, blockchain, Web3, DeFi/CeFi
- **JavaScript/Node.js/Python developers** — targeted via malicious npm packages
- **Crypto and finance professionals** — expanded targeting since 2024 to include non-technical roles (business development, marketing, asset management)

### Recruitment Lure Platforms
- LinkedIn (primary)
- X / Twitter
- GitHub
- Upwork, Freelancer, Telegram (for IT worker scheme adjacent activity)
- Direct email outreach

### Impersonated Companies (Partial List)
- Coinbase, KuCoin, Kraken, Circle, Securitize, BlockFi, Tether, Robinhood, Bybit, eToro — used in ClickFake Interview variant
- MiroTalk, FreeConference — trojanized video conferencing lures
- **BlockNovas LLC, Angeloper Agency, SoftGlide LLC** — DPRK-created front companies (seized by FBI, May 2025)

### Geographic Targeting
Global, with concentration in: United States, Europe, Asia-Pacific (Japan noted specifically), South Korea

---

## ⚙️ Attack Chain

### Phase 1 — Initial Contact
Attacker persona (fake recruiter or HR representative) contacts target via LinkedIn or other platform. Uses AI-generated profile photos (Remaker AI documented). Presents a job opportunity at a real or fabricated cryptocurrency company.

### Phase 2 — Interview Setup
Victim is directed to one of three mechanisms depending on campaign variant:

**Variant A — Malicious GitHub Project (original, 2022–2024)**
- Victim asked to download and run a "coding test" or "demo project" from GitHub or Bitbucket
- Project contains BeaverTail malware embedded in JavaScript test files or npm dependencies
- Executes when victim runs tests or installs dependencies (`npm install` / postinstall hook)

**Variant B — Trojanized Video App (2023–2024)**
- Victim asked to download video conferencing software for the interview
- Malicious installers: disguised as MiroTalk, FreeConference
- Contains Qt-based BeaverTail; executes silently in background during interview

**Variant C — ClickFix / ClickFake Interview (2024–present)**
- Victim directed to fake interview platform (e.g., impersonation of "Willo" video service)
- During camera setup, fabricated error message appears
- Victim instructed to copy and paste a `curl` command to "fix the issue"
- Command downloads and executes ContagiousDrop (Node.js malware distribution server)
- Platform: Windows and macOS
- Sub-campaign: ClickFake Interview (Sekoia) deploys GolangGhost backdoor via this vector

**Variant D — Malicious npm Supply Chain (2024–present)**
- Malicious packages published to npm registry under convincing names (typosquats of popular libraries)
- Packages contain postinstall hooks that download and execute malware
- Delivery chain: npm package → Vercel stager → GitHub-hosted payload → OtterCookie/BeaverTail C2

### Phase 3 — Malware Execution
See Malware Arsenal section for full details. Primary sequence:

```
Initial Loader (BeaverTail / ContagiousDrop / DiggingBeaver)
    ↓
Credential/Wallet Theft (BeaverTail infostealer)
    ↓
Second Stage Download (InvisibleFerret / OtterCookie / GolangGhost)
    ↓
Persistent Backdoor + Full System Compromise
    ↓
Cryptocurrency Theft + Data Exfiltration
```

### Phase 4 — Post-Compromise
- Remote shell access via AnyDesk (installed by InvisibleFerret) or Socket.IO C2 (OtterCookie)
- Keylogging and screenshot capture
- Browser credential harvesting (Chrome, Brave, Edge)
- Cryptocurrency wallet extension data theft (MetaMask, Phantom, TronLink, Rabby, and 15+ others)
- Clipboard monitoring (seed phrases, private keys)
- Recursive filesystem search for sensitive files matching crypto/wallet/credential patterns
- Data exfiltration to C2 servers; some variants exfiltrate to Microsoft OneDrive or Dropbox

---

## 🦠 Malware Arsenal

### BeaverTail
- **Type:** Downloader + Infostealer
- **Platforms:** Windows, macOS, Linux
- **Languages:** JavaScript (original), Qt C++ (2024 variant), merged with OtterCookie in v5
- **Functions:** Browser credential theft, cryptocurrency wallet extension data theft, Python downloader (for InvisibleFerret), keylogging in later versions
- **Targeted Wallets:** MetaMask, Phantom, BNB Chain, TronLink, Coin98, Crypto.com, Rabby, Exodus, OKX, Atomic, and more (13–21+ wallets targeted depending on version)
- **MITRE:** S1246

### InvisibleFerret
- **Type:** Multi-component Python backdoor
- **Platforms:** Windows, macOS, Linux
- **Languages:** Python
- **Components:** Downloader, main payload (remote control, keylogging, file exfiltration), browser stealer
- **Functions:** System fingerprinting, remote shell, keylogging, file exfiltration, AnyDesk installation for persistent access, browser credential/credit card theft
- **C2 Port:** TCP 1224 (hard-coded in early versions)
- **Variants:** FriendlyFerret, FrostyFerret, FlexibleFerret (macOS-specific variants)

### OtterCookie
- **Type:** Node.js backdoor / infostealer (evolved into combined RAT)
- **Platform:** Windows, macOS, Linux
- **Languages:** JavaScript (Node.js)
- **First Seen:** September 2024
- **C2 Protocol:** Socket.IO
- **Version History:**
  - v1 (Sep 2024): Basic crypto key theft via regex, clipboard monitoring
  - v2 (Nov 2024): Shell command execution, environment checks
  - v3 (Feb 2025): Windows support added, Upload module for file exfiltration
  - v4 (Apr 2025): Virtual environment detection, Chrome credential theft via DPAPI
  - v5 (Oct 2025): Full BeaverTail feature merge, keylogging + screenshot module, AnyDesk install
- **Functions (v5):** VM/sandbox evasion, system fingerprinting, remote shell, clipboard theft, keylogging, screenshots, browser credential theft, wallet extension theft, recursive filesystem scan

### GolangGhost (aka FlexibleFerret / WeaselStore)
- **Type:** Go-based backdoor
- **Platforms:** Windows, macOS
- **Languages:** Go
- **First Seen:** Late 2024 (ClickFake Interview sub-campaign)
- **C2:** Hard-coded C2 server, persistent command-processing loop
- **Functions:** System info collection, file upload/download, OS command execution, Chrome credential harvesting
- **Persistence (macOS):** LaunchAgent + shell script
- **Exfiltration:** Some variants use Dropbox (via decoy Chrome prompts)

### ContagiousDrop
- **Type:** Node.js malware distribution server / loader
- **Function:** Distributes platform-appropriate malware (Windows/macOS/Linux), notifies threat actors via email of victim engagement (form submissions, curl execution)
- **Email notification system:** Uses Gmail accounts (e.g., `designedcuratedamy58[@]gmail.com`) to alert operators

### OtterCandy (2025)
- **Type:** RAT + Infostealer (Node.js)
- **First Seen:** July 2025 (early sample February 2025 on VirusTotal)
- **Origin:** Combines OtterCookie + RATatouille features
- **Delivered By:** DiggingBeaver loader (ClickFake Interview sub-cluster)
- **C2 Protocol:** Socket.IO
- **Commands:** env, imp, pat (secret file search), upload (browser passwords, wallet data), exec, ss_stop, ss_upf, ss_upd, cd, ss_del

### DiggingBeaver
- **Type:** First-stage JavaScript loader
- **Function:** Executes when victim pastes ClickFix command into Windows Run dialog; delivers OtterCandy, GolangGhost, or FrostyFerret

---

## 🏢 DPRK Front Companies (Documented, 2025)

| Company | Domain | Status |
|---|---|---|
| BlockNovas LLC | blocknovas[.]com / apply-blocknovas[.]site | Seized by FBI (May 2025) |
| Angeloper Agency | angeloper[.]com / angeloperonline[.]online | Associated infrastructure |
| SoftGlide LLC | softglide[.]co | Associated infrastructure |

- Registered with fake/stolen business identities
- Used AI-generated employee profile photos (Remaker AI)
- Hosted fake job application portals with integrated malware delivery
- BlockNovas infrastructure exposed via OPSEC failure: dashboard monitoring C2 domains was publicly accessible on mail.blocknovas[.]com

---

## 🔗 Connection to Linked Campaigns

| Campaign | Relationship |
|---|---|
| **Operation Dream Job** | Parallel Lazarus campaign using same fake job lure mechanic; overlapping operators and infrastructure |
| **Wagemole / IT Worker Scheme** | Related DPRK campaign where operators seek actual employment at target companies; uses some of the same LinkedIn persona infrastructure |
| **ClickFake Interview** | Sub-campaign of Contagious Interview (Sekoia designation); introduces GolangGhost via ClickFix lures |
| **Bybit $1.5B Heist (Mar 2025)** | Infrastructure overlap with Contagious Interview tracked actors |

---

## 🛡️ Defense Recommendations

### For Developers and Job Seekers
1. **Never run code provided by a recruiter** without independent verification in an isolated VM
2. **Verify recruiter identities** via official company HR channels before engaging
3. Be extremely suspicious of any interview process requiring you to run scripts to "fix camera issues"
4. Do not paste `curl` commands received via chat into your terminal
5. Use a **dedicated, isolated VM** for all technical coding tests from unknown sources

### For Organizations
1. **npm Dependency Scanning** — implement real-time scanning (Socket, Snyk, or equivalent) on all dependency installs; flag packages with `postinstall` hooks that make network requests
2. **Block known malicious npm packages** — see IOCs section; maintain allowlist of approved modules
3. **Monitor for AnyDesk installation** from non-standard processes — InvisibleFerret installs AnyDesk as a persistence mechanism
4. **Alert on Socket.IO C2 traffic** to unusual hosts, especially from Node.js processes
5. **Monitor for Python being downloaded to non-standard paths** — BeaverTail downloads Python as a prerequisite for InvisibleFerret
6. **EDR behavioral rules:**
   - Alert on `curl` executed from browser or terminal with download+execute pattern
   - Alert on `node` processes spawning child processes with `detached: true`
   - Alert on filesystem enumeration matching crypto wallet keyword patterns
   - Alert on LaunchAgent creation on macOS from non-standard applications
7. **Candidate screening for crypto/blockchain roles** — brief hiring managers on this campaign pattern
8. **Block known C2 infrastructure** — see IOCs section

---

## 📚 References

- [Unit 42 — Original Contagious Interview Documentation (Nov 2023)](https://unit42.paloaltonetworks.com/two-campaigns-by-north-korea-bad-actors-target-job-hunters/)
- [Unit 42 — New BeaverTail/InvisibleFerret Variants (Oct 2024)](https://unit42.paloaltonetworks.com/north-korean-threat-actors-lure-tech-job-seekers-as-fake-recruiters/)
- [Sekoia — ClickFake Interview / GolangGhost (Mar 2025)](https://blog.sekoia.io/clickfake-interview-campaign-by-lazarus/)
- [SentinelOne — Threat Actors Scout CTI Platforms (Sep 2025)](https://www.sentinelone.com/labs/contagious-interview-threat-actors-scout-cyber-intel-platforms-reveal-plans-and-ops/)
- [Silent Push — DPRK Front Companies (Apr 2025)](https://www.silentpush.com/blog/contagious-interview-front-companies/)
- [NTT Security — OtterCookie Discovery (Jan 2025)](https://jp.security.ntt/insights_resources/tech_blog/en-contagious-interview-ottercookie/)
- [NTT Security — OtterCookie v2–v4 Updates (May 2025)](https://jp.security.ntt/insights_resources/tech_blog/en-waterplum-ottercookie/)
- [Socket Research — 197 npm Packages / stardev0914 Infrastructure (Nov 2025)](https://socket.dev/blog/north-korea-contagious-interview-npm-attacks)
- [Cisco Talos — OtterCookie v5 / BeaverTail Convergence (Oct 2025)](https://blog.talosintelligence.com/)
- [MITRE ATT&CK: Contagious Interview G1052](https://attack.mitre.org/groups/G1052/)
- [MITRE ATT&CK: BeaverTail S1246](https://attack.mitre.org/software/S1246/)
- [ENKI Whitepaper — Recent Campaign Activity (Jan 2025)](https://www.enki.co.kr/en/media-center/tech-blog/analysis-of-lazarus-s-recent-contagious-interview-campaign-activity)
