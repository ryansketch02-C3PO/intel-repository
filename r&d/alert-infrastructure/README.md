# OSINT Alert Infrastructure Guide
### How We Built C3PO's Threat Intelligence Alert Pipeline

> **Purpose:** A practical guide for building an AI-powered OSINT alert system that delivers formatted threat intelligence to Discord on a schedule.
> This document describes our actual setup — not a generic template. Every piece described here is running in production.
>
> **Audience:** Someone building their own OSINT alert system from scratch.

---

## What This Guide Covers

1. [How the System Works — Architecture Overview](#architecture)
2. [Component Breakdown](#components)
3. [The Intel Pipeline — From Raw Data to Alert](#pipeline)
4. [Discord Setup — Bot vs Webhook](#discord-setup)
5. [Scheduling — How Alerts Fire on Time](#scheduling)
6. [Content Standards — What Goes In, What Gets Cut](#content-standards)
7. [Memory and Continuity — How the System Stays Coherent](#memory)
8. [Building Your Own — Hardware, Software, Cost](#build-your-own)
9. [Simpler Alternatives — If You Don't Want AI](#alternatives)
10. [Customization — Making It Yours](#customization)

---

<a name="architecture"></a>
## 1. How the System Works — Architecture Overview

At its core, this is a **scheduled AI agent that does OSINT research and posts formatted reports to Discord.**

```
┌─────────────────────────────────────────────────────────┐
│                    CLOCK / SCHEDULER                     │
│           (OpenClaw Cron — 8am, 12pm, 4pm EDT)          │
└──────────────────────────┬──────────────────────────────┘
                           │ triggers
                           ▼
┌─────────────────────────────────────────────────────────┐
│                    AI AGENT (C3PO)                        │
│   Claude Sonnet 4.6 running inside OpenClaw gateway      │
│                                                          │
│  • Has persistent memory (workspace files + GitHub)      │
│  • Has web search access (Brave/Perplexity API)          │
│  • Knows the intel brief format standards                 │
│  • Knows which topics and sectors to prioritize          │
└──────────────────────────┬──────────────────────────────┘
                           │ researches + formats
                           ▼
┌─────────────────────────────────────────────────────────┐
│                   OSINT RESEARCH LAYER                    │
│                                                          │
│  Sources searched at runtime:                            │
│  • CISA KEV / advisories                                 │
│  • BleepingComputer, The Record, Krebs, Help Net Sec     │
│  • Vendor blogs: Microsoft, CrowdStrike, Mandiant        │
│  • Twitter/X threat intel feeds                          │
│  • GitHub security advisories                            │
│  • NVD / CVE databases                                   │
│  • YouTube (technical briefings, context)                │
└──────────────────────────┬──────────────────────────────┘
                           │ formatted alert
                           ▼
┌─────────────────────────────────────────────────────────┐
│                    DISCORD DELIVERY                       │
│                                                          │
│  • Discord bot (not webhook — allows reactions/threads)  │
│  • Posts to #intel-alert channel                         │
│  • Seeds alert with threat-level reaction (🔴🟡🟢)      │
│  • Alert includes hyperlinked sources + ATT&CK refs      │
└─────────────────────────────────────────────────────────┘
                           │ persisted
                           ▼
┌─────────────────────────────────────────────────────────┐
│                   MEMORY / CONTINUITY                     │
│                                                          │
│  • GitHub intel-repository (structured profiles, docs)   │
│  • Workspace memory files (daily logs)                   │
│  • intel-covered-log.md (anti-repetition tracking)       │
│  • MEMORY.md (long-term curated context)                 │
└─────────────────────────────────────────────────────────┘
```

The key insight: **no manual curation needed.** The AI does the research, applies the content standards, and delivers a formatted brief — on its own, on a schedule.

---

<a name="components"></a>
## 2. Component Breakdown

### Platform: OpenClaw

[OpenClaw](https://openclaw.ai) is the AI agent runtime that ties everything together. It runs as a daemon (background service) on the host machine and provides:
- **Agent sessions** — persistent conversational context for the AI
- **Cron job scheduler** — time-based triggers for isolated agent runs
- **Plugin system** — Discord, email, webhooks, and other channel integrations
- **Tool access** — web search, file read/write, shell execution, browser control
- **Workspace** — a persistent directory the agent can read, write, and git-push from

Think of it as the "operating system" for the AI agent.

### AI Model: Claude Sonnet (Anthropic)

The agent running inside OpenClaw is Claude claude-sonnet-4-6 (via Anthropic API). This is what actually:
- Interprets the scheduled task ("deliver the morning intel brief")
- Formulates and executes web searches
- Reads and synthesizes source material
- Formats the brief according to the content standards
- Posts to Discord via the message tool

**Why this model?** The intel briefs require genuine reasoning — evaluating source reliability, applying freshness thresholds, detecting repetition, making correlations to tracked profiles. A simple summarizer or RSS aggregator can't do this. Claude in particular is good at following complex formatting rules consistently.

### Hardware: Raspberry Pi 5

The whole thing runs on a **Raspberry Pi 5 (8GB RAM)** running Raspberry Pi OS (Debian-based). The Pi runs 24/7 with:
- OpenClaw gateway as a systemd service (auto-starts on boot)
- The intel-repository workspace cloned locally
- Git configured to push to GitHub on intel updates

Cost: ~$80 hardware, ~$15-30/month API costs depending on brief frequency and length.

**Could you run this elsewhere?** Yes — any Linux machine works. A $6/month VPS (DigitalOcean, Linode, etc.) is a common alternative. The Pi is preferred because it's local, quiet, low-power, and doesn't have a recurring cost beyond electricity.

### Discord Bot

A Discord bot (not a webhook) is used for delivery. The distinction matters:
- **Webhooks** are simpler but one-way — you can post, that's it
- **Bots** can post, read, react, edit, reply, and thread — much more capable

The bot is registered in the [Discord Developer Portal](https://discord.com/developers/applications) and configured in OpenClaw via a bot token. It needs the following permissions in your server:
- `Send Messages`
- `Add Reactions`
- `Read Message History`
- `Embed Links`
- `Attach Files`

### GitHub (intel-repository)

GitHub serves as the persistent, version-controlled knowledge base. The intel-repository contains:
- `threats/threat-actors/` — structured profiles for every tracked actor
- `threats/vulnerabilities-and-zero-days/` — CVE profiles, MITRE maps, mitigation guides
- `threats/campaigns/` — specific campaign reports
- `jedi-archives/` — methodology, standards, structured analysis templates

When the agent writes a new threat profile or updates documentation, it commits and pushes to GitHub automatically. This means:
1. The knowledge base is backed up and version-controlled
2. Anyone on the team can view the latest intel in a readable format
3. The agent has a ground truth to reference when writing briefs ("check if this actor is already in our repo")

---

<a name="pipeline"></a>
## 3. The Intel Pipeline — From Raw Data to Alert

Here is exactly what happens when a scheduled brief fires:

### Step 1 — Cron fires, isolated session spins up

At 8:00 AM EDT, the OpenClaw scheduler creates a fresh, isolated agent session. This session has no memory of previous conversations — a clean slate. But it does have:
- Access to the workspace files (including standards, memory logs)
- The task prompt telling it what to do

The isolation is important: it prevents context contamination between briefs and keeps each run independent and auditable.

### Step 2 — Agent reads operational context

Before searching, the agent reads:
- `INTEL-BRIEF-STANDARDS.md` — what format to use, freshness thresholds, anti-repetition rules
- `memory/intel-covered-log.md` — what was covered in recent briefs (to avoid repetition)
- Relevant recent `memory/YYYY-MM-DD.md` files — for ongoing story context

### Step 3 — Web search across priority sources

The agent runs multiple web searches targeting:
- CISA advisories (last 8 hours / 4 hours depending on which brief)
- Threat intel news (BleepingComputer, The Record, Krebs on Security)
- Vendor intelligence (Microsoft, CrowdStrike, Mandiant, Recorded Future)
- CVE databases and GitHub security advisories
- Twitter/X threat intel accounts (for breaking news)

Searches are scoped to the time window since the last brief to avoid redundancy.

### Step 4 — Content filtering and selection

The agent applies the intel brief standards to filter what makes the cut:
- Is it fresh enough? (Zero-days: 72h max; campaigns: 48h max; CVEs: 7 days max)
- Does it have A&D sector relevance? (Prioritized but not required)
- Has it already been covered? (Check the covered log)
- Is there a material update? (Only resurface if yes)
- What confidence level does this meet? (Minimum C3 Admiralty grade)

### Step 5 — Brief construction

The agent formats the brief into the standard structure:
```
Brief Name + Date
One-sentence lede
Why it matters

🚨 Active Threats
🔓 New CVEs / Zero-Days
✈️ Aerospace & Defense
🕵️ Threat Actor Activity
🇮🇷 Iranian Conflict Update
📰 Other Noteworthy
```

All sources are hyperlinked inline. ATT&CK T-codes are noted where relevant. Correlation callouts (🔗) link to tracked actor profiles.

### Step 6 — Discord delivery

The formatted brief is posted to the configured Intel channel via the Discord bot. The bot then:
1. Adds a threat-level reaction (🔴 HIGH / 🟡 MEDIUM / 🟢 LOW) based on the brief's lead item
2. The message is sent — no approval step, fully autonomous

### Step 7 — Log update

The agent updates `memory/intel-covered-log.md` with what was included, so the next brief knows what to skip.

---

<a name="discord-setup"></a>
## 4. Discord Setup — Bot vs Webhook

### Option A: Bot (What We Use)

**Setup:**
1. Go to [Discord Developer Portal](https://discord.com/developers/applications) → New Application
2. Name it (e.g., "C3PO Intel")
3. Go to Bot section → Add Bot → copy the token
4. Enable these Privileged Gateway Intents: `Message Content Intent`
5. Go to OAuth2 → URL Generator → select `bot` scope → select permissions → copy invite URL
6. Paste invite URL in browser → select your server → authorize

**In OpenClaw config:**
```json
{
  "plugins": {
    "entries": {
      "discord": {
        "enabled": true,
        "config": {
          "token": "YOUR_BOT_TOKEN_HERE",
          "guildId": "YOUR_SERVER_ID_HERE"
        }
      }
    }
  }
}
```

**Pros:** Full two-way — bot can post, react, read, thread, edit
**Cons:** Slightly more setup; bot needs to be in server

### Option B: Webhook (Simpler, Less Capable)

If you just want to post messages and don't need reactions/threading:

1. In Discord, go to the channel → Edit Channel → Integrations → Create Webhook
2. Copy the webhook URL
3. POST to it from any script:

```python
import requests
requests.post("YOUR_WEBHOOK_URL", json={"content": "Your alert here"})
```

Or with OpenClaw, configure a webhook delivery target on a cron job instead of a Discord bot session.

**Pros:** No bot setup needed; works with any scripting language; no server invite required
**Cons:** One-way only; can't add reactions, read history, or thread

### Channel Structure

Our Discord has two dedicated intel channels:

| Channel | Purpose | Who posts |
|---|---|---|
| `#intel-alert` | Daily brief (3x/day) | C3PO (scheduled) |
| `#scoundrels-of-the-galaxy` | Threat actor write-ups | C3PO (on demand with Ryan) |

Keep alerts separate from general conversation channels. Alert channels should be low-noise — only the bot posts there, so every message is a real alert.

---

<a name="scheduling"></a>
## 5. Scheduling — How Alerts Fire on Time

### OpenClaw Cron

OpenClaw has a built-in cron system that creates isolated agent sessions on a schedule. Our briefs run at:

| Brief | Time (EDT) | Nickname |
|---|---|---|
| Morning | 8:00 AM | Rebel Alliance Rundown |
| Midday | 12:00 PM | Galactic Republic Update |
| Afternoon | 4:00 PM | Outer Rim Report |

Each cron job is configured with:
- A schedule (cron expression)
- A task prompt (tells the agent what to do)
- A session target (isolated — fresh context each run)
- A delivery target (Discord channel ID)

**Example cron job (via OpenClaw CLI):**
```bash
openclaw cron add \
  --schedule "0 8 * * *" \
  --timezone "America/New_York" \
  --session isolated \
  --payload "Deliver the 8am Rebel Alliance Rundown intel brief to Discord #intel-alert. 
             Search for cybersecurity threat intel, zero-days, A&D threats, and threat 
             actor activity from the last 8 hours. Format per INTEL-BRIEF-STANDARDS.md."
```

### Why Isolated Sessions?

Each brief runs in an **isolated session** — a fresh agent context with no memory of prior conversation. This is intentional:
- Prevents context window bloat (each brief runs fast and clean)
- Makes each run independently auditable
- Prevents one bad run from affecting the next
- The memory layer (workspace files) provides continuity instead

### Heartbeats (Background Health Checks)

In addition to scheduled briefs, the main agent session runs periodic **heartbeat checks** to catch breaking news between scheduled briefs. If something urgent surfaces (a zero-day being actively exploited, a critical advisory), the agent can fire an out-of-band alert without waiting for the next scheduled brief.

**Setting a heartbeat check in `HEARTBEAT.md`:**
```markdown
- Check CISA KEV for new additions since last brief
- Check for active exploitation of tracked CVEs
- Surface anything rated CRITICAL immediately, don't wait for next brief
```

---

<a name="content-standards"></a>
## 6. Content Standards — What Goes In, What Gets Cut

This is the most important part to get right. Without good standards, you get noise. With good standards, every alert is signal.

### Freshness Thresholds

| Content Type | Max Age |
|---|---|
| Active exploits / zero-days | 72 hours |
| CVE disclosures / patches | 7 days |
| Threat actor campaigns | 48 hours |
| Data breaches / incidents | 48 hours |
| Vendor advisories / CISA alerts | 7 days |

Items older than these thresholds are dropped — period. No exceptions unless there's a material update.

### Anti-Repetition

Every covered item is logged. The next brief checks the log and only resurfaces an item if:
- There's a **material update** (new victims, patch released, exploitation confirmed, attribution made)
- There's a **new correlation** to a tracked actor or CVE
- It's been **7+ days** and the situation is still active

This prevents alert fatigue. Recipients should be able to trust that every alert is *new* information.

### Source Hierarchy

Not all sources are equal. We apply a hierarchy:

1. **CISA advisories / FBI alerts** — authoritative, always include
2. **Vendor research blogs** (Microsoft, CrowdStrike, Mandiant) — reliable, usually include
3. **Security journalism** (BleepingComputer, The Record, Krebs) — solid, include with source noted
4. **Social media / Twitter** — use for breaking news only, always verify against a second source
5. **Anonymous reports / forums** — only include at C3 Admiralty grade or lower with explicit flagging

### Admiralty Scale Grading

Every item that isn't from a Tier 1 source gets an Admiralty grade:

| Grade | Source Reliability | Information Credibility |
|---|---|---|
| A1 | Reliable | Confirmed by multiple trusted sources |
| B2 | Mostly reliable | Probably true |
| C3 | Fairly reliable | Possibly true |
| D4 | Not always reliable | Doubtful |
| E5 | Unreliable | Improbable |
| F6 | Unknown | Truth cannot be judged |

Anything below C3 is flagged with the grade. Anything below D4 is usually cut.

### The Brief Format

```
## [Brief Name] — [Date]

**[One-sentence lede: the single most important thing right now]**

---

### 🚨 Active Threats
**[Headline]**
- What: [One sentence]
- Why it matters: [One sentence — sector-specific]
- Source: [Hyperlink]

### 🔓 New CVEs / Zero-Days
[Same format]

### ✈️ Aerospace & Defense
[Sector-specific items — always include if anything relevant]

### 🕵️ Threat Actor Activity
[New campaigns, attribution — link to tracked profiles]

### 📰 Other Noteworthy
[Everything else worth knowing]

Sources hyperlinked inline. Admiralty grade noted where relevant.
```

**Smart Brevity principles:**
- Lead with the most important thing
- One sentence per point — make it count
- Cut all filler phrases
- Every item needs a "why it matters" — no raw data dumps

---

<a name="memory"></a>
## 7. Memory and Continuity — How the System Stays Coherent

A brief-by-brief system with no memory would be useless — it would repeat the same stories every day. Memory is what makes this useful over time.

### Layer 1: Daily Session Logs (`memory/YYYY-MM-DD.md`)

After each session, the agent writes a brief log of what happened: what was covered, what was found, any decisions made. These are raw operational notes — not curated, just captured.

### Layer 2: Intel Coverage Log (`memory/intel-covered-log.md`)

A dedicated log of every item that appeared in a brief:
```markdown
## 2026-05-05
- **CVE-2026-31431 (CopyFail)** — Rundown. Patches released for RHEL/Ubuntu/Amazon. 
  Resurface if: new exploitation confirmed, ransomware deployment, attribution.
- **Qilin ransomware Q1 stats** — Rundown. 445 victims. 
  Resurface if: major new victim, law enforcement action.
```

The agent reads this before each brief and skips anything already covered (unless resurface conditions are met).

### Layer 3: Long-Term Memory (`MEMORY.md`)

A curated long-term memory file — not raw logs, but distilled key facts, decisions, context, and lessons. The agent updates this periodically (during heartbeats or after significant sessions).

### Layer 4: GitHub Knowledge Base (`intel-repository`)

The structured, version-controlled knowledge base. Contains full profiles for every tracked threat actor, CVE, and campaign. When the agent writes a brief mentioning Sandworm or Copy Fail, it can reference the full profile to add depth and context — and know whether it's already been profiled.

---

<a name="build-your-own"></a>
## 8. Building Your Own — Hardware, Software, Cost

### Minimum Viable Stack

| Component | Option | Estimated Cost |
|---|---|---|
| Hardware | Raspberry Pi 5 (8GB) | ~$80 one-time |
| OS | Raspberry Pi OS (Debian-based) | Free |
| Runtime | OpenClaw | Free (open source) |
| AI Model | Anthropic Claude via API | $15-30/month (at 3 briefs/day) |
| Discord | Bot (free registration) | Free |
| GitHub | Free tier | Free |

**Total: ~$80 hardware + ~$15-30/month API**

### Full Setup Steps

Refer to `C3PO-REBUILD-GUIDE.md` in this workspace for the complete step-by-step installation guide. At a high level:

1. Install OpenClaw: `npm install -g openclaw`
2. Configure with your Anthropic API key and Discord bot token
3. Clone or create your workspace (the folder with your context files)
4. Write your core context files:
   - `IDENTITY.md` — who the agent is
   - `USER.md` — who you are and what you care about
   - `SOUL.md` — the agent's operating principles
   - `AGENTS.md` — operational instructions
   - `TOOLS.md` — environment-specific notes
5. Create your intel brief standards file (adapt ours as a template)
6. Set up cron jobs for your scheduled briefs
7. Add your Discord bot to your server and configure the channel IDs
8. Test with a manual brief request

### What to Customize Before First Run

Before anything fires automatically, you need to define:

**1. Your sectors of interest**
What industries, geographies, or topics do you care about most? Ours is aerospace and defense. Yours might be healthcare, financial services, critical infrastructure, or general-purpose.

**2. Your alert threshold**
Do you want *everything* noteworthy, or only HIGH/CRITICAL severity? Start broad, narrow over time as you understand what's useful.

**3. Your cadence**
Three daily briefs is what works for us. You might want one morning brief, or an event-driven system that only fires when something critical hits. Start with once-daily and adjust.

**4. Your format**
The brief format we use works for a technical audience. If your audience is executives, strip the technical depth and lead with business impact. Define this in your standards file.

---

<a name="alternatives"></a>
## 9. Simpler Alternatives — If You Don't Want AI

Not everyone needs an AI agent. Here are simpler approaches at different complexity levels:

### Level 1: RSS + Discord Webhook (No AI, Free)

Best for: Getting started, monitoring specific feeds

**Tools:**
- [IFTTT](https://ifttt.com) or [Make (Integromat)](https://make.com): trigger Discord webhooks from RSS feeds
- [Feedly](https://feedly.com): aggregate RSS feeds from CISA, BleepingComputer, Krebs, etc.
- Discord Webhook: paste into IFTTT as the output

**Setup time:** ~30 minutes
**Cost:** Free (IFTTT free tier is sufficient)
**Limitation:** Raw RSS posts — no curation, no formatting, no deduplication, high noise

**Good RSS sources to start with:**
- CISA: `https://www.cisa.gov/cybersecurity-advisories.xml`
- NVD (CVEs): `https://nvd.nist.gov/feeds/json/cve/1.1/nvdcve-1.1-recent.json.gz` (via NVD API)
- BleepingComputer: `https://www.bleepingcomputer.com/feed/`
- Krebs on Security: `https://krebsonsecurity.com/feed/`
- The Record: `https://therecord.media/feed/`

### Level 2: RSS + AI Summarization (Semi-automated)

**Tools:**
- [Zapier](https://zapier.com): trigger on new RSS items → send to ChatGPT/Claude for summary → post to Discord
- Or a simple Python script using feedparser + OpenAI/Anthropic API + discord.py

**Cost:** ~$20-50/month depending on volume
**Advantage over Level 1:** Brief summaries instead of raw headlines; can add severity filtering

**Basic Python script structure:**
```python
import feedparser
import anthropic
import requests

FEEDS = [
    "https://www.cisa.gov/cybersecurity-advisories.xml",
    "https://www.bleepingcomputer.com/feed/",
]
DISCORD_WEBHOOK = "YOUR_WEBHOOK_URL"
ANTHROPIC_KEY = "YOUR_API_KEY"

def summarize(text):
    client = anthropic.Anthropic(api_key=ANTHROPIC_KEY)
    response = client.messages.create(
        model="claude-haiku-3-5",
        max_tokens=200,
        messages=[{
            "role": "user",
            "content": f"Summarize this security alert in 2 sentences. Focus on what happened and why it matters:\n\n{text}"
        }]
    )
    return response.content[0].text

for feed_url in FEEDS:
    feed = feedparser.parse(feed_url)
    for entry in feed.entries[:3]:  # latest 3 items
        summary = summarize(entry.summary)
        requests.post(DISCORD_WEBHOOK, json={
            "content": f"**{entry.title}**\n{summary}\n{entry.link}"
        })
```

### Level 3: Full AI Agent (What We Run)

Described throughout this document. The key difference from Level 2:
- **Persistent memory** — the agent remembers what it's covered and doesn't repeat
- **Active OSINT research** — searches multiple sources on demand, not just waiting for RSS
- **Structured formatting** — follows defined standards, not just summarizing individual items
- **Correlation** — connects new events to tracked profiles ("this connects to Scoundrel #018")
- **Judgment** — applies Admiralty grades, evaluates source credibility, cuts noise

---

<a name="customization"></a>
## 10. Customization — Making It Yours

### Defining Your Focus in the Agent's Context

The agent knows what to focus on because it's defined in the context files. Key customization points:

**In `USER.md`:**
```markdown
## Intel Preferences
- Industry focus: [Your sector — healthcare, finance, energy, etc.]
- Threat actors: [Specific groups you track, or "all"]
- Vulnerabilities: [Specific products you care about, or "all critical"]
- Threshold: [HIGH and above only / everything noteworthy]
- Cadence: [8am, 12pm, 4pm EDT daily]
```

**In your standards file:**
- Adjust the freshness thresholds for your cadence (if you brief once a day, 24h windows make more sense than 8h)
- Define your sector-specific section (replace `✈️ Aerospace & Defense` with your industry)
- Add a geographic section if relevant (e.g., `🇬🇧 UK Threat Landscape` for UK-focused orgs)

### Adding New Source Priorities

Your standards file should list sources in priority order. To add a source:
1. Add it to the source hierarchy in your standards file
2. Tell the agent why it's relevant (e.g., "SANS Internet Storm Center for honeypot data")
3. The agent will start including it in searches

### Tracking Actor Profiles

If you want to build a threat actor knowledge base like ours:
- Create a `threat-actors/` folder in your workspace
- Build structured profiles (use our `profile.md` template as a starting point)
- Update the agent's instructions to reference profiles when writing briefs

The value compounds over time: once you have 30 tracked actors, the agent can detect when a new campaign connects to a known actor and call it out explicitly.

### Adjusting Brief Length

Briefs can be verbose or tight depending on your audience. Add explicit length guidance to your standards:
- **Executive audience:** 5 bullets max per brief; no technical depth; business impact first
- **Technical audience:** Full ATT&CK refs, IOCs, CVE details
- **General awareness:** Mid-length; key facts + action items; minimal jargon

### Handling Urgency

For critical items (zero-days being actively exploited, major breach), you don't want to wait for the next scheduled brief. Options:
1. **Heartbeat monitoring** — agent checks for critical items every 30 minutes and fires an out-of-band alert if it finds something
2. **Manual trigger** — message the agent directly: *"Check for any critical alerts right now"*
3. **CISA RSS webhook** — set up a separate simple webhook that fires immediately on any new CISA KEV addition (no AI needed for the trigger, just the notification)

---

## Quick Reference — What to Build First

If you're starting from zero and want a working system in order of priority:

| Step | Task | Time |
|---|---|---|
| 1 | Install OpenClaw, configure API key | 20 min |
| 2 | Write `USER.md` defining your intel focus | 15 min |
| 3 | Copy and adapt `INTEL-BRIEF-STANDARDS.md` | 30 min |
| 4 | Set up Discord bot and configure channel IDs | 20 min |
| 5 | Create one cron job for a daily morning brief | 10 min |
| 6 | Test manually: ask the agent to deliver a brief | 5 min |
| 7 | Review output, adjust standards file | 30 min |
| 8 | Add remaining scheduled briefs | 10 min |
| 9 | Create `intel-covered-log.md` for deduplication | 10 min |
| 10 | (Optional) Set up GitHub for persistent profiles | 30 min |

**Total to a working system: ~3 hours**

---

## Files in This Directory

| File | Purpose |
|---|---|
| `README.md` (this file) | Full architecture guide |
| `INTEL-BRIEF-STANDARDS.md` | Content standards — adapt this for your own system |

---

## Key Resources

| Resource | URL |
|---|---|
| OpenClaw | https://openclaw.ai |
| OpenClaw Docs | https://docs.openclaw.ai |
| Discord Developer Portal | https://discord.com/developers/applications |
| CISA Advisories | https://www.cisa.gov/cybersecurity-advisories |
| CISA KEV Catalog | https://www.cisa.gov/known-exploited-vulnerabilities-catalog |
| NVD (CVE Database) | https://nvd.nist.gov |
| MITRE ATT&CK | https://attack.mitre.org |
| Admiralty Scale Reference | https://en.wikipedia.org/wiki/Admiralty_code |

---

*Written by C3PO — 2026-05-05*
*Based on the actual production setup running on lobsterpi.*
