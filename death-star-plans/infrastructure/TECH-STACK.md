# 🤖 C3PO Intelligence Platform — Tech Stack

> A plain-English breakdown of everything running under the hood.

---

## The Big Picture

```
┌─────────────────────────────────────────────────────────────┐
│                        YOU (Ryan)                           │
│                   Discord / Web Chat                        │
└──────────────────────────┬──────────────────────────────────┘
                           │
                           ▼
┌─────────────────────────────────────────────────────────────┐
│                    C3PO (AI Agent)                          │
│           Powered by Claude Sonnet 4.6 (Anthropic)         │
│                  Running on OpenClaw                        │
└────────┬──────────────┬──────────────┬──────────────────────┘
         │              │              │
         ▼              ▼              ▼
   ┌──────────┐  ┌───────────┐  ┌───────────────┐
   │ Discord  │  │  GitHub   │  │  Splunk SIEM  │
   │ Channels │  │   Repo    │  │  (Home PC)    │
   └──────────┘  └───────────┘  └───────────────┘
         │
         ▼
   ┌──────────────────────────────┐
   │     OSINT Stack (lobsterpi) │
   │  SpiderFoot · Tor · Shodan  │
   │  theHarvester · recon-ng    │
   └──────────────────────────────┘
```

---

## Layer 1 — The Brain (AI)

| What | Details |
|------|---------|
| **Model** | Claude Sonnet 4.6 by Anthropic |
| **What it does** | Reads threat intel, writes briefs, researches actors, makes decisions |
| **How it's billed** | Pay-per-token Anthropic API (not a subscription) |
| **Why Claude** | Best reasoning quality for complex threat analysis |

**Plain English:** This is the analyst brain. It reads the news, understands context, writes the briefs, and runs all the workflows automatically.

---

## Layer 2 — The Platform (OpenClaw)

| What | Details |
|------|---------|
| **Software** | OpenClaw — AI agent framework |
| **Running on** | Raspberry Pi 5 (lobsterpi), always-on |
| **What it does** | Connects the AI to Discord, GitHub, the shell, schedules jobs |
| **Key feature** | Cron scheduler — runs tasks automatically on a timer |

**Plain English:** OpenClaw is the operating system for C3PO. Without it, the AI would just be a chatbot. OpenClaw gives it hands — it can post to Discord, run commands, push to GitHub, and wake itself up on a schedule.

---

## Layer 3 — Delivery (Discord)

| Channel | Purpose |
|---------|---------|
| **#intel** | Daily threat briefs (8am + 4pm EDT) |
| **#alerts** | Real-time threat actor & CVE alerts (every 6 hours) |
| **#scoundrels-of-the-galaxy** | Deep-dive threat actor profiles |

**Scheduled outputs:**

```
8:00 AM EDT  →  🛸 Rebel Alliance Rundown       (daily)
4:00 PM EDT  →  🌎 Outer Rim Report             (daily)
Every 6hrs   →  🚨 Threat Alerts                (silent if nothing new)
Friday noon  →  🕵️  Weekly Threat Actor Summary  (weekly)
Sat/Sun 2pm  →  ☀️  Twin Suns Digest             (weekends)
```

**Plain English:** Discord is the delivery mechanism. Every piece of intelligence C3PO produces gets formatted and posted automatically — you never have to ask for it.

---

## Layer 4 — Intelligence Collection (OSINT Stack)

All running free on the Raspberry Pi 5:

| Tool | What It Does |
|------|-------------|
| **SpiderFoot** | Automated OSINT engine — scans domains, IPs, emails across 200+ sources |
| **theHarvester** | Fast enumeration of subdomains and exposed emails from public sources |
| **recon-ng** | Modular recon framework for structured, repeatable investigations |
| **Tor + TorBot** | Dark web access — crawl .onion sites anonymously |
| **Shodan CLI** | Finds exposed infrastructure and vulnerable devices across the internet |
| **nmap** | Port and service scanning |

**Web tools (free, no install):**
Intelligence X · VirusTotal · ThreatFox · MalwareBazaar · DeHashed · Shodan · Censys · DNSDumpster · Have I Been Pwned

**Plain English:** This is the investigative toolkit. When C3PO needs to dig deeper on a threat actor or vulnerability, these tools do the legwork — scanning infrastructure, crawling the dark web, and enriching IOCs.

---

## Layer 5 — Knowledge Base (GitHub)

| What | Details |
|------|---------|
| **Repo** | `threat-repository` (private) |
| **URL** | https://github.com/ryansketch02-C3PO/threat-repository |
| **What's stored** | Threat actor profiles, IOCs, docs, runbooks |
| **Structure** | `threat-actors/[NAME]/profile.md` + `iocs.md` per actor |
| **Auto-updated** | C3PO pushes new profiles and updates automatically |

**Plain English:** GitHub is the library. Every threat actor C3PO researches gets a permanent, structured file with profile, TTPs, and IOCs. It's version-controlled, searchable, and always up to date.

---

## Layer 6 — SIEM (Splunk)

| What | Details |
|------|---------|
| **Software** | Splunk Enterprise |
| **Running on** | Home PC at `192.168.1.66` |
| **Bridge** | DefenseClaw (installed on lobsterpi) forwards events via HEC |
| **Index** | `defenseclaw_local` |

**Plain English:** Splunk is the security dashboard. Intel events and IOCs flow from the Pi into Splunk where you can query, visualize, and correlate them. The Pi collects and forwards — the PC does the heavy SIEM work.

---

## Hardware

| Device | Role |
|--------|------|
| **Raspberry Pi 5 (8GB)** — *lobsterpi* | Always-on agent host, OSINT stack, DefenseClaw bridge |
| **Home PC** | Splunk Enterprise SIEM, Maltego CE (GUI tools) |

---

## What It All Costs

| Component | Cost |
|-----------|------|
| OpenClaw | Self-hosted (free) |
| Anthropic API | Pay-per-token (~$15–30/month typical) |
| OSINT Stack | Free (all open source) |
| GitHub | Free (private repo) |
| Splunk | Free (developer license) |
| Discord | Free |
| Hardware | Already owned |
| **Total ongoing** | **~$15–30/month** |

---

## In One Sentence

> C3PO is an always-on AI threat intelligence analyst running on a Raspberry Pi — it automatically collects OSINT, researches threat actors, monitors for alerts, and delivers formatted intelligence briefs to Discord three times a day, for about the cost of a Netflix subscription.

---

*Last updated: 2026-04-16 | Maintained by C3PO*
