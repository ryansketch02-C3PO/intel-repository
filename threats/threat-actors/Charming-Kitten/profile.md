# 🐱 Charming Kitten (APT35) — Threat Actor Profile

## Identity

| Field | Details |
|---|---|
| **Primary Name** | Charming Kitten |
| **Aliases** | APT35, APT42, Phosphorus, Mint Sandstorm, Magic Hound, TA453, COBALT MIRAGE, Newscaster, NewsBeef, Smoke Sandstorm, Group 83, TunnelVision, BOHRIUM, iKittens, UNC788, ITG18, Parastoo |
| **Type** | Nation-State APT |
| **Sponsoring State** | Iran |
| **Attributed To** | Islamic Revolutionary Guard Corps – Intelligence Organization (IRGC-IO), Unit 50 ("Thaqib") |
| **Active Since** | ~2011–2014 |
| **Status** | 🔴 Active |
| **Motivation** | Strategic Espionage · HUMINT Collection · Influence Operations · Domestic Surveillance |
| **Scoundrel #** | #011 |

---

## Target Profile

| Field | Details |
|---|---|
| **Primary Sectors** | Government, Diplomacy, Defense Industrial Base, Aerospace & Aviation, Think Tanks, Academic Research, Media, Human Rights, NGOs |
| **Secondary Sectors** | Energy, Healthcare, Financial, Telecommunications, Technology |
| **Geographies** | United States, Israel, Gulf States (UAE, Saudi Arabia, Kuwait), Turkey, Europe, Middle East (Jordan, Lebanon), Iran (domestic dissidents) |
| **Aerospace Relevance** | **HIGH** — Directly targeted aviation regulators (Jordan Civil Aviation Commission), aerospace contractors, defense industrial base; stole aviation regulatory communications and contractor documents |

---

## Organizational Structure

APT35 is not a loosely organized hacking collective — it is a **bureaucratized state intelligence unit** operating under IRGC-IO Unit 50 ("Thaqib"), commanded by **Abbas Rahrovi (عباس راهروی)**, also known as Abbas Hosseini.

Internal leaked documents (Oct 2025) reveal:
- **Structured command hierarchy**: Campaign Coordination Unit → Lead Analysts → Operator Cells
- **Specialized teams**: Exploit Development, Credential/Access, HERV Phishing, RTM/HUMINT Liaison
- **KPI-driven operations**: Monthly performance reports, supervisor reviews, quota metrics
- **Physical facility**: Centralized IRGC-affiliated compound; badge-in/badge-out logs align with operation timestamps
- **Financial infrastructure**: Cryptocurrency handler ("Sarrafi") for off-books payments; document forgery services

---

## Tactics, Techniques & Procedures (TTPs)

### Initial Access
- Spear-phishing via elaborate fake personas (fake journalists, academics, conference organizers; multi-week rapport-building)
- Fake webinar platforms and job recruiter lures targeting aerospace/defense professionals
- Credential harvesting portals impersonating Google, Microsoft, Yahoo, institutional logins
- Watering hole attacks
- N-day/zero-day exploitation: rapid weaponization (CVE-2024-1709 ConnectWise: day-1 exploitation)
- ProxyShell chain (CVE-2021-34473/34523/31207), ProxyLogon (CVE-2021-26855)
- Log4Shell (CVE-2021-44228), Zoho ManageEngine (CVE-2022-47966)
- Telerik exploitation (CVE-2019-18935, CVE-2017-11317)
- PHP CGI RCE (CVE-2012-1823)

### Persistence
- Webshell deployment (Adminer, custom PHP); buried in `/var/vpn/themes/imgs/` and similar
- Domain admin account creation
- Backup system compromise (Acronis Cloud)
- OAuth token theft and replay for persistent cloud access
- Fast Reverse Proxy (FRP) tunneling through Azure/cloud VPS

### Credential Access
- LSASS memory dumps (Mimikatz-style)
- NTLM hash theft and credential reuse
- MFA bypass via adversary-in-the-middle phishing kits (intercepts OTP tokens in real time)
- Password spraying against Microsoft 365/Azure AD tenants (250+ tenants observed by Microsoft)
- Hyperscrape tool: silent email exfiltration from Gmail/Microsoft accounts post-compromise

### Defense Evasion
- EDR bypass: SentinelOne, Sophos, Trend Micro, CrowdStrike (lab-tested)
- Obfuscated DLL payloads; DLL hijacking
- VPN rotation (European IPs); infrastructure compartmentalization
- VeraCrypt for encrypted data storage
- Operational timing: shifts synchronized with Tehran timezone

### Collection & Exfiltration
- Global Address List (GAL) extraction from Exchange via EWS/Autodiscover
- Sustained mailbox monitoring ("RTM") for HUMINT — ongoing access maintained months/years
- Database dumps (Adminer)
- CCTV footage and VoIP call recording theft documented
- Exfiltration volumes: 74GB+ documented from single target (Qistas operation, Dec 2024–Jan 2025)
- Data staged in organized, encrypted archives by client/project

### Infrastructure
- Domains: Cloudflare + Namecheap hosting; Tebyan datacenter VPS
- Relay nodes: Netherlands IPs (high-quality ICT infra), Singapore RIPE (AS1; operational egress)
- Comms: 3CX, Issabelle, Output Messenger (internal collaboration)
- Phishing infra: Multi-platform social media ads (Facebook, Twitter/X, Instagram, Google, Microsoft, Telegram)

---

## Malware & Tooling

| Tool | Type | Notes |
|---|---|---|
| **BellaCiao** | Dropper/Implant | Delivers tailored implants by victim geolocation; C++ variant (BellaCPP) drops SSH tunnel DLL; weaponizes Exchange/Zoho vulns for delivery |
| **POWERSTAR** | Backdoor | PowerShell-based; active in 2024–2025 campaigns against academics/policy groups |
| **PowerLess** | Backdoor | PowerShell implant that executes without invoking `powershell.exe`; evades detection |
| **Hyperscrape** | Data theft tool | Logs into and silently exfiltrates emails from Gmail/Microsoft accounts |
| **AnvilEcho** | PowerShell Trojan | Part of BlackSmith toolkit; deployed via spear-phishing (2024) |
| **Tickler** | Malware | Custom malware; also used by APT33 overlapping infrastructure |
| **CHAINSHOT** | Exploit | Targeted Israeli defense/tech (2021) |
| **DustySky** | Backdoor | Early-generation tool; Israeli targeting |
| **BASICSTAR / Sponsor** | Backdoors | Recent campaign additions |
| **RTM Project** | Custom RAT | In-development (2024); AD integration, share folder enumeration, shell capability |
| **Thaqib RAT** | RAT | Associated with IRGC IO Unit 50; evolutionary successor to Ivanti/ProxyShell toolchain |

---

## Notable Operations & Campaigns

| Date | Operation | Notes |
|---|---|---|
| 2011–2015 | "Newscaster" Campaign | Fake journalist/news site personas; social engineering at scale |
| 2018 | University Credential Theft | Mass phishing vs. US/Middle East universities |
| 2020 | COVID-19 Pharma Targeting | Phishing vs. pharmaceutical companies |
| 2021 | Israeli Defense/Tech | DustySky + CHAINSHOT targeting |
| 2022–2023 | Journalist/Human Rights | Global spear-phishing vs. activists, journalists |
| 2022–2025 | Exchange/ProxyShell Campaign | Region-wide; diplomatic, govt, corporate targets in Turkey, Lebanon, Kuwait, Saudi Arabia, South Korea |
| Early 2024 | Fake Webinar Platform | Targeted Middle East policy experts |
| 2024 | Aerospace Job Recruiter Lures | Fake job offers targeting aerospace/defense professionals (ongoing since Sept 2023) |
| 2024 | US Election Interference | Phishing/influence ops tied to 2024 US presidential election |
| July 2024 | BlackSmith/AnvilEcho | Targeted prominent Jewish religious figure |
| Feb–Mar 2024 | ConnectWise CVE-2024-1709 | Day-1 exploitation; 6-country scan campaign |
| Dec 2024–Jan 2025 | Qistas Operation | Supply chain attack; 74GB exfiltrated; full AD domain compromise; EDR bypass |
| 2025 | POWERSTAR/Tickler Campaigns | Ongoing vs. universities, think tanks, policy groups |

---

## Connection Web

| Actor | Relationship |
|---|---|
| **UNC1549** (#004) | Operational overlap confirmed; both IRGC-aligned; share targeting of aerospace/defense; IOC database entries link them directly. UNC1549 uses similar job-lure TTPs against same aerospace sector. |
| **APT33 (Peach Sandstorm)** | Sister IRGC unit; Tickler malware overlap; both target aerospace/aviation; infrastructure crossover documented |
| **Pioneer Kitten (Fox Kitten)** | Fellow IRGC APT; operational overlap noted in 2024 Iranian threat cluster activity |
| **IRGC IO Unit 50 ("Thaqib")** | APT35 is a subordinate subdivision of Unit 50; shared command, tooling pipeline, and comms infrastructure |

---

## Threat Assessment

| Field | Assessment |
|---|---|
| **Threat Level** | 🔴 HIGH |
| **Sophistication** | HIGH — Custom malware, EDR evasion, rapid CVE weaponization, multi-year persistence |
| **Persistence** | VERY HIGH — Maintains access months to years; HUMINT-grade sustained collection |
| **Aerospace Relevance** | HIGH — Proven track record targeting aviation regulators, aerospace contractors, DIB supply chain |
| **Current Activity** | ELEVATED — Active per April 2026 reporting; increased ops tempo post-June 2025 US strikes on Iranian nuclear infrastructure |
| **Admiralty Grade** | B2 |

---

## Defensive Recommendations

1. **Enforce phishing-resistant MFA (FIDO2/hardware keys)** — Standard OTP/push MFA is bypassed by their AiTM phishing kits
2. **Patch Exchange/OWA/EWS** — ProxyShell/ProxyLogon remain active in playbooks; monitor for ASPX webshell artifacts
3. **Patch and segment Ivanti, F5, Pulse Secure, NetScaler** — Priority remote access appliances in their targeting
4. **Threat hunt for LSASS dumps, scheduled tasks, anomalous OAuth grants**
5. **Block known IOC IPs/domains** (see iocs.md)
6. **LinkedIn/social media awareness** — Sophisticated persona-based approaches target aerospace professionals directly
7. **Monitor for Hyperscrape-style email access patterns** — Authenticated access from unusual IPs/UAs

---

## Sources

- CloudSEK TRIAD Analysis — APT35 Leaked Docs (Oct 2025): https://www.cloudsek.com/blog/an-insider-look-at-the-irgc-linked-apt35-operations
- DomainTools Intelligence — APT35 Internal Leak Report (Jan 2026): https://dti.domaintools.com/research/threat-intelligence-report-apt35-internal-leak-of-hacking-campaigns-against-lebanon-kuwait-turkey-saudi-arabia-korea-and-domestic-iranian-targets
- Brandefense APT35 Profile (Jan 2026): https://brandefense.io/wp-content/uploads/2026/01/brandefense.io-apt35-irans-persistent-cyber-espionage-force-apt35.pdf
- Sysdig TRT — Iranian Cyber Threats Bulletin (Jun 2025): https://www.sysdig.com/blog/sysdig-threat-bulletin-iranian-cyber-threats
- PolySwarm — 2024 Iran Nexus Threat Actor Recap: https://blog.polyswarm.io/2024-recap-iranian-threat-actor-activity
- Shieldworkz — Iranian Cyber Groups Strategic Analysis (Apr 2026): https://shieldworkz.com/blogs/decoding-the-strategic-quiet-of-iranian-cyber-groups
- Rewterz — BellaCPP IOCs (Dec 2024): https://rewterz.com/threat-advisory/iran-linked-apt35-spreads-new-bellaciao-malware-variant-active-iocs
- Picus Security — Iranian Threat Actors (Mar 2026): https://www.picussecurity.com/resource/iranian-threat-actors-what-defenders-need-to-know
- MITRE ATT&CK G0059: https://attack.mitre.org/groups/G0059/

---

*Profile created: 2026-04-08 | Last updated: 2026-04-08*
