# Handala Hack (Void Manticore)

## Identity

| Field | Details |
|---|---|
| **Primary Alias** | Handala Hack |
| **Other Names** | Void Manticore, Red Sandstorm, Banished Kitten, Storm-0842, Dune, Karma (prev. persona), Homeland Justice (Albania ops) |
| **Type** | State-Sponsored / Hacktivist Proxy |
| **Sponsoring State** | Iran — Ministry of Intelligence and Security (MOIS) |
| **Unit Attribution** | MOIS Internal Security Deputy, Counter-Terrorism (CT) Division |
| **Known Leader** | Yahya Hosseini Panjaki (alias: Yahya Hamidi) — sanctioned by US in 2024 |
| **Origin** | Iran |
| **First Observed** | December 2023 (Handala persona); Void Manticore active since ~2022 |
| **Status** | 🔴 HIGHLY ACTIVE (as of April 2026) |
| **Motivation** | Psychological warfare, destruction of Israeli/Western infrastructure, pro-Palestinian/pro-Iranian influence operations |
| **Threat Level** | 🔴 HIGH |

---

## Overview

Handala Hack is the primary public-facing persona of **Void Manticore**, an Iran MOIS-linked threat actor. The group emerged publicly in December 2023 in the immediate aftermath of the October 7 Hamas attacks, rapidly escalating from simple defacements and wiper malware to one of the most aggressive destructive cyber operations targeting Israel, and increasingly the United States.

Despite presenting as an independent hacktivist collective, multiple Western intelligence agencies and private researchers (Check Point, KELA, CrowdStrike, Sophos) assess Handala to be a **controlled MOIS front operation** — not a grassroots movement. The US Department of Justice described Handala as "a fictitious identity used by the MOIS to hide its role in influence operations and psychological scaremongering campaigns."

The group's defining characteristic is its **"wipe-first, talk-later"** operational philosophy: gain access, destroy everything, then publicly announce the damage with dramatic flair on Telegram and X (before being repeatedly banned). They combine destructive wiper malware with hack-and-leak operations and deliberate psychological operations designed to terrify civilian populations.

Following the February 28, 2026 US-Israeli military strikes on Iran (Operation Epic Fury / Operation Roaring Lion), Handala dramatically escalated operations against US and Western targets. Their **March 2026 attack on Stryker Corporation** — a Michigan-based medical device manufacturer serving 150 million patients — is assessed as the **most severe Iranian wartime cyberattack against the United States in history**.

The FBI's Rewards for Justice Program has offered **$10 million** for information identifying Handala group members (as of March 2026).

---

## Targets

- **Primary:** Israeli government, military, defense contractors, intelligence agencies (Mossad, Shin Bet, Unit 8200)
- **Critical Infrastructure:** Energy (Delek/Delkol fuel systems, Israel Opportunity Energy), water systems (Jerusalem), weather stations, telecommunications
- **Healthcare:** Clalit Health Services (Israel's largest), Sheba Medical Center, Stryker Corporation (US)
- **Defense/Nuclear:** Soreq Nuclear Research Center, Israeli Industrial Batteries (MoD-affiliated), Arrow & David's Sling weapons designers
- **Finance:** Israeli banks, insurance companies (Harel), payment processors
- **Academic:** Hebrew University of Jerusalem, Weizmann Institute of Science
- **Western Organizations with Israeli ties:** Stryker (US), Vidisco security scanners
- **Geographic Expansion (2026):** US, Jordan, Saudi Arabia, Albania (via Homeland Justice persona)

---

## Tactics, Techniques & Procedures (TTPs)

### Initial Access (TA0001)
- Spear-phishing with malicious attachments masquerading as software updates (e.g., **F5UPDATER.exe**)
- Phishing campaigns spoofing legitimate vendors (e.g., CrowdStrike during July 2024 outage)
- Compromised VPN credentials via brute-force / credential stuffing
- Hundreds of brute-force attempts against organizational VPN infrastructure from commercial VPN nodes (169.150.227.X CIDR)
- **Starlink IP ranges** used post-Iran internet blackout (January 2026+) to maintain operational continuity

### Execution (TA0002)
- Multi-stage loaders: **Delphi-based loader** → **AutoIT injector** → destructive payload
- PowerShell scripts (Phase3.ps1) for wiper deployment
- Group Policy distribution to maximize network-wide wiper spread
- AI-assisted PowerShell scripts observed (2026)

### Persistence & Lateral Movement (TA0008)
- RDP-based lateral movement
- **NetBird** (legitimate VPN software) deployed manually via RDP to create internal peer-to-peer tunnels across compromised hosts
- ASPX webshells: error4.aspx, ClientBin.aspx, pickers.aspx, Pickers.aspx
- **reGeorge** webshell for web-tier persistence
- Signed legitimate binaries abused to bypass application allow-lists

### Defense Evasion (TA0005)
- Disable Windows Defender (disable_defender.exe, mellona.exe)
- JA3/TLS fingerprinting to mimic legitimate traffic
- Exfiltration over non-standard ports or encrypted cloud storage (Mega.nz, Storj, cloud endpoints)
- Commercial VPN infrastructure for traffic anonymization

### Destruction (TA0040)
- **ZeroShred Wiper** — primary destructive tool (2025–2026)
- **GoneXML "Ransomware"** — actually a wiper disguised as ransomware (no functional decryption)
- **Hamsa Wiper** — used in "Operation HamsaUpdate" (2023), targets Windows & Linux
- **Hatef Wiper** — custom wiper, early operations
- **GoXML.exe** — wiper-family executable
- **RawDisk3 / rwdsk.sys** — raw disk driver for low-level destructive operations
- Simultaneous deployment of **4+ distinct wiping techniques in parallel** (Stryker attack, 2026)
- Stryker attack vector: Microsoft Intune abuse to push wiper to enrolled devices globally

### Exfiltration & Psychological Operations (TA0010 / TA0040)
- Data exfiltration before destruction, then public leak on Telegram/BreachForums/Exploit/Ramp
- Targeting of senior officials' personal devices (phone contacts, Telegram sessions, email accounts)
- Public death threats and doxxing via **"RedWanted" site** (handala-redwanted[.]to, created July 2025)
- Mass SMS campaigns to civilians (fake rocket sirens, threatening messages)
- Website defacement with psychological messaging
- $30,000 bounties offered for information on Israeli military industry individuals
- $250,000 bounty offered for violence against named Iranian-American/Iranian-Canadian activists (March 2026)

---

## Key Operations Timeline

| Date | Operation | Impact |
|---|---|---|
| Dec 2023 | **Operation HamsaUpdate** | Wiper malware campaign targeting Israeli Windows/Linux users; Israel National Cyber Directorate warning issued |
| Apr 2024 | Iron Dome/Radar Claims | Claimed hack of Iron Dome systems; 500,000 texts sent to Israelis |
| Jun 2024 | Kibbutz Ma'agan Michael | 22 GB data seized; 5,000 false SMS rocket sirens sent |
| Jul 2024 | CrowdStrike Lure | Distributed wiper malware via fake CrowdStrike fix PDFs during global IT outage |
| Sep 2024 | Soreq Nuclear Research Center | Claimed 197 GB exfiltrated; names of nuclear scientists published |
| Oct 2024 | Shin Bet Breach | 30,000 officer records claimed stolen |
| Oct 2024 | Israeli Industrial Batteries | 300 GB of MoD-affiliated company data leaked |
| Nov 2024 | Senior Officials Email Leaks | 110K emails (PM Barak), 60K emails (IDF Chief Eisenkot), 35K emails (Defense Min. Gantz) |
| Nov 2024 | Vidisco Security Scanners | Claimed backdoor discovery in explosive detection scanners |
| Jan 2025 | Kindergarten PA System Hack | 20+ kindergartens had Arabic messages/rocket sirens played over PA systems |
| Sep 2025 | Soreq Nuclear Research Center (2nd) | 197 GB exfiltration claimed; particle accelerator scientists named |
| Oct 2025 | **RedWanted Campaign Launch** | Doxxing site targeting Israel-supporting individuals/organizations globally |
| Dec 2025 | Operation Octopus (Bennett) | PM Bennett's Telegram account compromised; contact lists/chats leaked |
| Dec 2025 | "Bibi Gate" (Braverman) | Netanyahu's chief of staff Telegram breached |
| Feb 2026 | Clalit Health Services | Medical records of 10,000+ patients leaked (Israel's largest health fund) |
| Mar 3, 2026 | Israel Opportunity Energy / Jordan Fuel | Oil & gas company and Jordan fuel system attacks; claimed Saudi Aramco compromise |
| Mar 11, 2026 | **Stryker Corporation Attack** | 200,000+ systems destroyed across 79 countries; 10s of thousands sent home; described as most severe Iranian wartime cyberattack on US in history |
| Mar 19, 2026 | FBI Takedown Attempt | FBI seized Handala website; Handala restored it the next day |
| Mar 27, 2026 | FBI Director Kash Patel Email Leak | 300+ emails published; Rewards for Justice Program offers $10M for Handala IDs |

---

## Related Personas & Connections

| Persona | Use | Status |
|---|---|---|
| **Homeland Justice** | Albanian government/telecom targeting (2022–present) | Active |
| **Karma / Karma Below** | Pre-Handala Israeli targeting persona | Largely replaced by Handala |
| **Void Manticore** | Technical attribution label (Check Point, CrowdStrike) | Active |
| **Scarred Manticore** | Collaborated on initial access (separate MOIS-adjacent actor) | Active |

### Connections to Other Tracked Actors
- **Charming Kitten (#011)** — Both are Iranian state-linked actors operating under IRGC/MOIS umbrella; Handala (MOIS-CT) is distinct from Charming Kitten (IRGC-IO/APT42) but part of the same state cyber ecosystem
- **UNC1549 (#004)** — Co-located within Iranian state cyber apparatus; UNC1549 focuses on aerospace/defense espionage while Handala focuses on destruction and psychological ops
- **#OpIsrael Coalition** — Handala has coordinated with pro-Russian hacktivist groups (RipperSec, Cyb3rDrag0nzz) under the "Cyber Islamic Resistance" coalition (2026)

---

## Detection & Defense Guidance

### High-Priority Mitigations
1. **Block Iranian IP ranges** and commercial VPN nodes (169.150.227.0/24, Starlink CIDR blocks) at perimeter
2. **Phishing-resistant MFA (FIDO2)** — Handala specializes in OTP harvesting and session hijacking
3. **Monitor Microsoft Intune** for unauthorized device enrollment or policy pushes (Stryker attack vector)
4. **Backup integrity controls** — offline/immutable backups; test restoration regularly
5. **NetBird monitoring** — detect unauthorized installation of peer-to-peer VPN software on endpoints
6. **Behavioral analysis over allow-listing** — Handala abuses signed binaries; signature-based detection insufficient
7. **Restrict RDP** — lateral movement heavily reliant on RDP; segment and require MFA
8. **Hunt for webshell artifacts:** error4.aspx, ClientBin.aspx, pickers.aspx, Pickers.aspx, reGeorge patterns

### Threat Intelligence Monitoring
- Monitor Telegram for Handala Hack, Void Manticore, Homeland Justice announcements
- Track handala-redwanted[.]to for targeting intelligence (if your org has Israeli ties)
- Monitor BreachForums, Ramp, Exploit platforms for Handala leak posts

---

## References

- Check Point Research: "Handala Hack — Unveiling Group's Modus Operandi" (March 2026)
- ExtraHop: "The Digital Front of Iranian Cyber Offensive Response" (March 2026)
- Wikipedia: Handala (hacker group)
- KELA Cyber: "The Handala Hack: Telegram Breach of Israeli Officials" (Dec 2025)
- Deepwatch: "Handala Team — Hacktivist Cyber Operations" (March 2026)
- Inversion6: "Handala Hack Team Threat Brief" (2026)
- RansomLook: Handala group page
- Sophos: "Hacktivist campaigns increase as US-Iran-Israel conflict intensifies" (March 2026)
- US DoJ / FBI: Rewards for Justice Program — $10M bounty on Handala members

---

## Intel Grading

| Assessment | Grade |
|---|---|
| **Source Reliability** | B (Usually reliable — major security vendors + government attribution) |
| **Information Credibility** | 2 (Probably true — corroborated by multiple independent sources) |
| **Overall Grade** | **B2** |
| **TLP** | TLP:WHITE — suitable for public distribution |

*Last Updated: 2026-04-10*
