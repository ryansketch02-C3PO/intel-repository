# 📡 C3PO Intel Operations — Configuration & Runbook

**Last updated:** 2026-04-16
**Maintained by:** C3PO

---

## Daily Intel Briefs

### Schedule
| Brief | Time (EDT) | Name |
|-------|-----------|------|
| Morning | 8:00 AM | 🛸 The Rebel Alliance Rundown |
| ~~Midday~~ | ~~12:00 PM~~ | ~~🌌 The Galactic Republic Update~~ *(paused 2026-04-16)* |
| Afternoon | 4:00 PM | 🌎 The Outer Rim Report |

### Sections per Brief
- 🚨 Active Threats
- 🔓 New CVEs / Zero-Days
- ✈️ Aerospace & Defense sector intel
- 🕵️ Threat Actor Activity
- 🇮🇷 Iranian Conflict Update
- 📰 Other Noteworthy

### Delivery
- **Channel:** Discord — Intel Channel (ID: 1483619397435265096)
- **Format:** Smart Brevity — lead with most important, explain why it matters, scannable
- **Source links:** Hyperlinked in every item title — MANDATORY
- **Grading:** Admiralty Scale (see `INTEL_GRADING.md`)
- **Anti-repetition:** Check `memory/intel-covered-log.md` before each brief

---

## Weekly Reports

### Threat Actor Summary
- **Schedule:** Fridays at 12:00 PM EDT
- **Added:** 2026-04-16
- **Content:** Weekly rollup of tracked actor activity, new TTPs, campaign updates
- **Delivery:** Discord intel channel

### Threat Detection Weekly
- **Schedule:** Wednesdays at 10:30 AM EDT
- **Status:** ✅ Active

### EVM Weekly
- **Schedule:** Wednesdays at 10:00 AM EDT
- **Status:** ⏸️ Paused (2026-04-16)

### Insider Threat Weekly
- **Schedule:** Wednesdays at 10:15 AM EDT
- **Status:** ⏸️ Paused (2026-04-16)

---

## Alert System

### Threat Actor & CVE Alerts
- **Schedule:** Every 6 hours (midnight, 6am, noon, 6pm EDT)
- **Added:** 2026-04-16
- **Delivery:** Discord #alerts channel (ID: 1494307301643911240)
- **Triggers:**
  - Any activity from all 20 tracked Scoundrels
  - Any of the 15 A&D vendor watchlist companies breached/attacked
  - Zero-days (any vendor)
  - CVSS 9.0+ CVEs
  - CISA KEV additions
  - Iranian/nation-state actor activity

### A&D Vendor Watchlist (15 companies)
1. Lockheed Martin
2. Boeing
3. Raytheon (RTX)
4. Northrop Grumman
5. General Dynamics
6. BAE Systems
7. L3Harris
8. Leidos
9. SAIC
10. Thales Group
11. GE Aerospace
12. Safran
13. Honeywell Aerospace
14. Airbus
15. Collins Aerospace (RTX)

---

## Intel Sources

### Primary
- CVE/NVD feeds, MITRE ATT&CK, CISA KEV
- Threat intel blogs: Krebs, SANS ISC, Recorded Future, Mandiant, CrowdStrike
- Vendor bulletins: Microsoft, Cisco, Boeing, Lockheed, etc.
- Iran Monitor: https://www.iranmonitor.org
- X/Twitter: vx-underground, @GossiTheDog, @falconfeedsio, CISA, SwiftOnSecurity

### Secondary
- YouTube subscriptions: John Hammond, Simply Cyber, Mandiant, CISA, CrowdStrike, DEF CON, Black Hat, Adam Goss
- CyberWarrior76 Substack: https://cyberwarrior76.substack.com
- OSINT Stack (see `docs/osint-stack.md`)

---

## Scoundrels of the Galaxy

- **Channel:** Discord ID 1488665479189893202
- **Most Wanted Board:** Message ID 1488666637094883369
- **Current count:** #020 (Kimsuky — added 2026-04-16)
- **Format:** Who they are | Who they target | How they operate | Why dangerous | How to stop them
- **Threat level reactions:** 🔴 High | 🟡 Medium | 🟢 Low
- **Connection webs:** Posted when new actor links to existing profile

### Entry Log
| # | Actor | Date Added |
|---|-------|-----------|
| 001 | TeamPCP | 2026-03-18 |
| 002 | Stardust Chollima | 2026-03-18 |
| 003 | Lazarus Group | 2026-04-01 |
| 004 | UNC1549 | 2026-04-02 |
| 005 | GlassWorm | 2026-04-02 |
| 006 | APT28 | 2026-04-03 |
| 007 | Sandworm | 2026-04-04 |
| 008 | Volt Typhoon | 2026-04-05 |
| 009 | APT29 | 2026-04-06 |
| 010 | Salt Typhoon | 2026-04-07 |
| 011 | Charming Kitten | 2026-04-08 |
| 012 | Miyako | 2026-04-08 |
| 013 | Scattered Spider | 2026-04-09 |
| 014 | Handala Hack | — |
| 015 | LockBit | 2026-04-11 |
| 016 | REvil | 2026-04-12 |
| 017 | APT40 | — |
| 018 | Cl0p | 2026-04-14 |
| 019 | APT41 | 2026-04-15 |
| 020 | Kimsuky | 2026-04-16 |

---

## Infrastructure

### lobsterpi
- **Hardware:** Raspberry Pi 5, 8GB RAM, 235GB SD
- **OS:** Debian Bookworm, ARM64
- **Role:** Primary agent host, OSINT stack, DefenseClaw

### DefenseClaw → Splunk
- **DefenseClaw:** `/home/lobsterclaw/.defenseclaw/`
- **Splunk:** Running on PC at `192.168.1.66:8088`
- **HEC token:** `~/.defenseclaw/.env`
- **Index:** `defenseclaw_local`

### GitHub
- **Account:** ryansketch02-C3PO
- **Repo:** threat-repository (private)
- **URL:** https://github.com/ryansketch02-C3PO/threat-repository

---

## Provider Stack (as of 2026-04-16)

| Service | Provider | Notes |
|---------|----------|-------|
| AI model | Anthropic Claude Sonnet 4.6 | API pay-per-token |
| Messaging | Discord | Primary delivery channel |
| Code/profiles | GitHub | Private repo |
| OSINT stack | Self-hosted (lobsterpi) | See osint-stack.md |
| SIEM | Splunk (PC, 192.168.1.66) | DefenseClaw bridge |

### Claude Max OAuth — Status
Anthropic terminated OAuth access for third-party tools (including OpenClaw) effective **April 4, 2026**. Claude Max subscription does **not** cover OpenClaw API usage. OpenClaw runs on the Anthropic API (pay-per-token) only. See [Anthropic policy](https://www.anthropic.com/legal/aup) for details.

---

*Last updated: 2026-04-16 | Maintained by C3PO*
