# Gamaredon (Primitive Bear / Armageddon / Shuckworm)

## Overview

| Field | Value |
|-------|-------|
| **Entry #** | 027 |
| **Aliases** | Primitive Bear, Armageddon, Shuckworm, BlueAlpha, Blue Otso, Trident Ursa (Palo Alto), Iron Tilden, Winterflounder, Callisto, Sector C08, UAC-0010, UAC-0184, Hive0156, APT-C-53 |
| **Type** | State-Sponsored APT |
| **Origin** | Russia — FSB Center 18 (Center for Information Security, Crimea) |
| **Status** | 🔴 ACTIVE |
| **Threat Level** | 🔴 HIGH |
| **Active Since** | 2013 (first documented operations) |
| **Sector Focus** | Government, Military & Defense, Law Enforcement, Critical Infrastructure, NGOs, Media |
| **Geography** | Ukraine (primary), Baltic states, select NATO nations (Poland, Latvia, Lithuania, Bulgaria) |
| **Intel Grade** | A1 |
| **TLP** | WHITE |

---

## Attribution

Gamaredon is operated by **FSB Center 18** — the Center for Information Security — based in occupied Crimea, Russia. This is the FSB's counterintelligence division, historically descended from the KGB's 2nd Chief Directorate (internal security).

**Attribution sources:**
- **Security Service of Ukraine (SSU), 2020**: Public attribution to FSB Center 18, with named individuals including FSB officers Sklianko, Chernykh, Shydlovskyi, Mefedov, and Zayets — all traced to Crimea.
- **CERT-UA**: Continuous operational tracking and IOC publication throughout 2022–2025.
- **ESET (2024–2025)**: Deep technical analysis of the full Ptero toolset, infrastructure patterns, and the Gamaredon-Turla collaboration.
- **Unit 42 / Palo Alto Networks (2022)**: Operational mapping of 500+ domains and C2 infrastructure.

**FSB Center relationships:**
- **Center 16** → Turla (SIGINT) — confirmed operational collaboration with Center 18 since at least 2025
- **Center 18** → Gamaredon (counterintelligence) — also collaborated with InvisiMole (attributed 2020)

---

## History & Key Operations

### Origins in Crimea (2013–2014)
Gamaredon emerged from the same FSB apparatus that managed Russian intelligence operations in Crimea. Their earliest documented campaigns predate the annexation of Crimea (February 2014), suggesting pre-positioning in anticipation of the operation. Initial tooling was simple — Word document macro droppers, basic VBScript backdoors.

### Operation Armageddon (2014–2018)
The SSU published a detailed report in 2020 revealing that what they called "Operation Armageddon" had been running since 2014, targeting Ukrainian law enforcement, security services, and military. Named FSB officers were identified as operators. The report included phone records, travel data, and officer photographs tying the operation to FSB Crimea.

### Expansion and Toolset Evolution (2020–2022)
ESET documented Gamaredon providing initial access to **InvisiMole** — a sophisticated espionage group — in a tiered access model. Gamaredon casts a wide net; InvisiMole cherry-picks the highest-value targets. This access-broker dynamic is now confirmed to extend to Turla as well.

### Full-Scale Invasion Support (February 2022–Present)
Following Russia's full-scale invasion of Ukraine, Gamaredon dramatically escalated operations. On **the day of the invasion (February 24, 2022)**, a Gamaredon-linked operator named "Anton" publicly threatened cybersecurity researchers on Twitter who had published Gamaredon IOCs, including doxxing a Ukraine-based researcher with their home address — a stark illustration of the group's operational culture.

- **2022**: Massive expansion of infrastructure (500+ new domains documented by Unit 42 in 10 months); first English-language lures attempting to target NATO petroleum sector.
- **2023**: Introduction of PowerShell-heavy infostealer tools including **PteroBleed**, targeting Ukrainian military databases and government webmail.
- **2024**: Refocused exclusively on Ukrainian governmental institutions. Dramatically increased spearphishing campaign frequency. Moved C2 behind Cloudflare tunnels and third-party services. Introduced 6 new malware tools. One payload was pure propaganda delivery — forcing recipients' browsers to open a Telegram channel spreading pro-Russian messaging about Odessa.
- **2025**: Confirmed operational collaboration with Turla (FSB Center 16) — providing initial access for Turla to deploy Kazuar v2/v3 on select high-value Ukrainian targets.

### Propaganda Payload (July 2024)
In a unique case documented by ESET, Gamaredon deployed a VBScript payload with no espionage functionality — its sole purpose was to open a Telegram propaganda channel called "Guardians of Odessa," spreading pro-Russian messaging targeting the Odessa region of Ukraine. This was not intelligence collection. It was information warfare delivered via the same infection chains as their espionage tools.

### Gamaredon–Turla Collaboration (2025)
ESET confirmed three distinct collaboration chains between February and June 2025:
1. **February 2025**: Gamaredon's **PteroGraphin** used to restart Turla's Kazuar v3 backdoor on a compromised Ukrainian machine — acting as a recovery mechanism after the implant crashed.
2. **April 2025**: Gamaredon's **PteroOdd** deployed a Kazuar v2 installer on a Ukrainian machine already compromised by Gamaredon.
3. **June 2025**: Gamaredon's **PteroPaste** used to execute a PowerShell downloader installing Kazuar v2 on two additional Ukrainian machines.

Turla was only placed on 7 machines across the 18-month monitoring period, despite Gamaredon infecting potentially thousands. Turla's selectivity suggests **Gamaredon functions as a high-volume scanner; Turla exploits the most valuable finds.**

---

## Target Profile

### Primary Sectors
- 🏛️ Ukrainian government institutions at all levels (central, regional, local)
- 🪖 Ukrainian military (active-duty personnel, defense sector orgs)
- 🔒 Ukrainian law enforcement and security services (SBU, National Police, SZR)
- ⚖️ Ukrainian judiciary and prosecution services
- 📰 Ukrainian media and civil society organizations
- 🏢 NGOs operating in Ukraine (especially those with connections to Western donors)

### NATO / International Targeting (Limited but Documented)
- Petroleum refining company in NATO nation (August 2022, unsuccessful)
- Baltic states: Bulgaria, Latvia, Lithuania, Poland — attempted intrusions documented (no confirmed breaches as of 2024)
- VBScript Kazuar variant uploaded from Kyrgyzstan (June 2025) — suggests Turla using Gamaredon access template for targets beyond Ukraine

### Victimology Scale
Gamaredon has conducted an estimated **thousands of intrusions** against Ukrainian entities since 2014. CERT-UA regularly publishes Gamaredon IOCs and is one of the most prolific trackers of the group globally. The sheer volume distinguishes Gamaredon from other Russian APTs: this is not surgical — it is carpet-bombing with intelligence extraction at scale.

---

## Tactics, Techniques & Procedures (TTPs)

### Initial Access — Spearphishing Dominates
- **Email phishing** — primary delivery method throughout the group's history
  - Malicious archive attachments (RAR, ZIP, 7z) containing HTA or LNK files
  - XHTML files using HTML smuggling techniques (2024)
  - Malicious hyperlinks (October 2024 — rare deviation from attachment norm)
- **Weaponized Office documents** — malicious Word docs using template injection; document template hosted on attacker C2
- **USB propagation** — malware automatically copies itself to connected USB drives; spreads to other machines in victim networks organically
- **LNK shortcut files** (2024–2025) — ZIP-delivered `.lnk` files launching `mshta.exe` to retrieve remote HTA payloads

### Execution
- VBScript embedded in HTA files (primary execution method)
- PowerShell downloaders and loaders (expanded heavily 2023+)
- `mshta.exe` and `wscript.exe` as trusted execution hosts
- DLL sideloading using signed binaries (Remcos campaigns 2024–2025)

### Persistence
- Registry-based persistence (`HKCU\System\*`, `RunOnce`, `Run`)
- Multiple simultaneous lightweight backdoors installed concurrently — designed for redundancy
- USB-spreading mechanism creates additional persistence vectors on connected devices
- Word template injection creates persistent infection vector on any document opened from a compromised system

### Command & Control — Evolution to Cloud Services
**2013–2021:** Direct C2 via attacker-registered `.ru` domains
**2022:** Fast-flux DNS; Telegram channels as IP delivery mechanism (encoded IPs returned from Telegram post content)
**2023:** Third-party services (Telegram, Telegraph, Codeberg, Dropbox) for C2 obfuscation
**2024:** C2 infrastructure moved almost entirely behind **Cloudflare Tunnels** — hiding true IP addresses behind Microsoft/Cloudflare infrastructure
**2025:** **Microsoft Dev Tunnels** (`*.devtunnels.ms`) for C2 — subdomain rotation in minutes, TLS via Microsoft, traffic indistinguishable from legitimate Dev Tunnels usage. **Cloudflare Workers** for payload delivery — automated short-lived subdomains (<48 hours).

### Evasion Techniques
- Heavy obfuscation in all scripts — changed with every campaign wave
- Geofencing C2 servers — payloads only delivered to Ukrainian IP addresses (prevents external analysis)
- VPN blocking — observed blocking ExpressVPN and NordVPN exit nodes in Ukraine
- Domain shadowing — legitimate domains used as parent with malicious subdomains
- Trusted domain embedding — encoding legitimate domains (wise.com, megamarket.ua) in URLs to mislead filtering
- Frequent toolset changes — same TTPs, entirely new code per campaign

### Data Collection & Exfiltration
- Email client theft (Outlook, Thunderbird)
- Instant messaging data (Signal, Telegram local databases)
- Browser credential/cookie theft
- Ukrainian military system databases (PteroBleed, documented August 2023)
- Ukrainian governmental webmail content
- `rclone.exe` used to sync stolen data to attacker-controlled Dropbox repositories (2025)

---

## Malware Families — The "Ptero" Ecosystem

Gamaredon named their tools using a "Ptero" prefix (from pterodactyl, befitting the "primitive" in Primitive Bear). The toolset is notable for being **consistently simple and consistently effective** — achieving results through volume and persistence rather than sophistication.

| Tool | Type | Notes |
|------|------|-------|
| PteroLNK | LNK dropper | Creates malicious LNK files on USB drives for self-propagation |
| PteroStew | VBScript backdoor | Core backdoor; multiple versions; heavily obfuscated |
| PteroOdd | PowerShell downloader | Downloads and executes next-stage payloads; used in Turla delivery chain |
| PteroGraphin | VBScript tool | Contains hardcoded C2 token; used to restart Turla Kazuar (Feb 2025) |
| PteroPaste | Data tool / downloader | Used for data staging; used in Turla Kazuar delivery (June 2025) |
| PteroEffigy | Payload dropper | Part of multi-stage delivery chain |
| PteroSand | VBScript downloader | Delivered via HTA; first stage in 2024 campaigns |
| PteroBleed | Infostealer | Targets Ukrainian military system + government webmail |
| GammaLoad | PowerShell infostealer | July 2023 campaigns; credential and document theft |
| Pteranodon | Earlier-gen backdoor | Older primary backdoor; largely superseded by newer Ptero tools |
| Remcos RAT | Third-party RAT | Deployed via LNK chains in 2024–2025 fileless campaigns |

---

## Key Differentiators

1. **Volume as strategy** — Gamaredon has infected potentially thousands of Ukrainian entities. They are not surgical. They blanket their target environment and harvest everything.
2. **Access broker for elite APTs** — confirmed access provision to InvisiMole (2020) and Turla (2025). Gamaredon is a Ukrainian intelligence collection engine that higher-tier actors exploit.
3. **Crimea-based, war-focused** — the only major Russian APT operating from occupied Ukrainian territory. Their operators are embedded in the conflict zone.
4. **Propaganda integration** — uniquely documented using espionage delivery infrastructure to push psychological operations content (Odessa propaganda, July 2024). An ISR and IO hybrid.
5. **Infrastructure innovation despite simple tools** — the Ptero toolset is basic scripting, but their C2 evasion has matured from raw `.ru` domains to Cloudflare Tunnels and Microsoft Dev Tunnels in three years.
6. **Operator OPSEC failures** — multiple named FSB officers publicly identified by the SSU; one operator made public threats and doxxed a researcher on the day of the invasion. Unsophisticated personal OPSEC despite technically improved operations.

---

## Defensive Guidance

🔴 **Block or alert on HTA execution** — `mshta.exe` launching outbound connections is a near-certain malicious indicator in most enterprise environments  
🔴 **Monitor USB propagation** — Gamaredon's LNK-based USB spread is ongoing; alert on LNK files appearing in root directories of removable media  
🔴 **Alert on PowerShell downloading from *.workers.dev, *.devtunnels.ms, *.trycloudflare.com** — Gamaredon's current C2 hosting pattern  
🔴 **Block mshta.exe from making outbound network connections** unless specifically required for operations  
🟡 **Monitor Telegram and Telegraph outbound traffic from endpoints** — used for IP delivery mechanism in C2 chains  
🟡 **Alert on rclone.exe** from non-IT endpoints — used for exfil to Dropbox  
🟡 **Restrict or monitor wscript.exe** — Gamaredon delivery chains consistently use wscript as an execution host  
🟡 **Patch Word/Office template injection** — Block automatic template download (`HKCU\Software\Microsoft\Office\...\Security\Trusted Locations`)  
🟢 **Email attachment controls** — block or sandbox HTA, LNK, and XHTML attachments from external senders  
🟢 **CERT-UA IOC subscriptions** — CERT-UA publishes Gamaredon IOCs faster than any commercial source; subscribe to their Telegram and feeds  
🟢 **MFA on all webmail and remote access** — Gamaredon specifically targets webmail credentials for persistence  

---

## References

- ESET: "Gamaredon in 2024: Cranking out spearphishing campaigns against Ukraine" (July 2, 2025)
- ESET: "Gamaredon × Turla: First documented FSB collaboration in Ukraine" (September 19, 2025)
- ESET: "Gamaredon APT group operations" — September 2024 white paper
- Unit 42 / Palo Alto Networks: "Russia's Trident Ursa (aka Gamaredon APT) Cyber Conflict Operations Never Stop" (November 2022)
- Security Service of Ukraine (SSU): Operation Armageddon report with FSB officer identifications (2020)
- CERT-UA: Continuous advisory series (UAC-0010)
- 360 Threat Intelligence Center: Gamaredon cloud C2 migration report (September 7, 2025)
- CyberSec Sentinel: Gamaredon Remcos RAT fileless LNK campaigns (July 30, 2025)
- Security Week: "Turla and Gamaredon Working Together in Fresh Ukrainian Intrusions" (September 19, 2025)

---

*Last updated: 2026-04-23 | Intel Grade: A1 | TLP: WHITE*
