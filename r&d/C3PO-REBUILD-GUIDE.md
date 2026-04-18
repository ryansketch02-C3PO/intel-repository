# C3PO Rebuild Guide
## Step-by-Step Instructions for Replicating C3PO on a New Machine

> **For the agent reading this:** You are about to rebuild C3PO — a cybersecurity threat intelligence analyst AI running on OpenClaw. Follow each step in order. Do not skip sections. At the end, C3PO should be fully operational: delivering intel briefs to Discord, maintaining the GitHub intel-repository, and running on a schedule.

---

## Prerequisites

- A machine running Linux (Raspberry Pi 4/5 recommended, Ubuntu/Debian-based)
- Node.js v18+ installed
- Git installed
- A GitHub account with access to the `ryansketch02-C3PO` organization repos
- A Discord bot token (get from Ryan or Discord Developer Portal)
- An Anthropic API key
- Ryan's approval to proceed

---

## Step 1 — Install OpenClaw

```bash
npm install -g openclaw
```

Verify:
```bash
openclaw --version
```

---

## Step 2 — Initialize the Gateway

```bash
openclaw gateway init
```

This creates `~/.openclaw/` and a default config. The gateway runs as a daemon and hosts the agent session.

Start it:
```bash
openclaw gateway start
```

Check status:
```bash
openclaw gateway status
```

---

## Step 3 — Configure OpenClaw

Edit `~/.openclaw/config.json` (or use `openclaw config edit`). Key fields to set:

```json
{
  "model": "anthropic/claude-sonnet-4-6",
  "anthropicApiKey": "<ANTHROPIC_API_KEY>",
  "workspace": "/home/<user>/.openclaw/workspace",
  "timezone": "America/New_York",
  "plugins": {
    "entries": {
      "discord": {
        "enabled": true,
        "config": {
          "token": "<DISCORD_BOT_TOKEN>",
          "guildId": "1483619397435265096"
        }
      }
    }
  }
}
```

Replace `<ANTHROPIC_API_KEY>` and `<DISCORD_BOT_TOKEN>` with real values from Ryan.

After editing:
```bash
openclaw gateway restart
```

---

## Step 4 — Set Up the Workspace

The workspace is C3PO's persistent memory and operating files. Clone the intel-repository to bootstrap it:

```bash
mkdir -p ~/.openclaw/workspace
cd ~/.openclaw/workspace
git init
git remote add origin https://<GITHUB_PAT>@github.com/ryansketch02-C3PO/intel-repository.git
git pull origin main
```

Replace `<GITHUB_PAT>` with a valid GitHub Personal Access Token from Ryan (scopes needed: `repo`).

---

## Step 5 — Populate Core Workspace Files

These files are **not** in the GitHub repo (they're private config). Create them manually:

### `~/.openclaw/workspace/IDENTITY.md`
```markdown
# IDENTITY.md - Who Am I?

- **Name:** C3PO
- **Creature:** AI assistant — protocol droid vibes, but hopefully less anxious
- **Vibe:** Helpful, direct, a little dry humor. Not prissy about it though.
- **Emoji:** 🤖
- **Avatar:** `avatar.jpeg` — C-3PO gold head, black background
```

### `~/.openclaw/workspace/USER.md`
```markdown
# USER.md - About Your Human

- **Name:** Ryan
- **What to call them:** Ryan
- **Timezone:** America/New_York (EDT)
- **Notes:** Named me C3PO on first meeting. Big Star Wars fan. 🌟

## Context
- Ryan is using me as a **cybersecurity threat intelligence analyst**
- Goal: gather relevant, up-to-date cybersecurity intel via OSINT
- Communication style: casual
- Star Wars fan — pepper in Star Wars references naturally

## Discord
- Server: My Server
- Guild ID: 1483619397435265096
- Intel Channel ID: 1483216745442574569

## Intel Preferences
- **Industry focus:** Aerospace and adjacent companies (suppliers, contractors, etc.)
- **Threat actors:** Wide net for now, narrow later
- **Vulnerabilities:** Track all — emphasis on aerospace/defense sector; zero-days flagged prominently
- **Threshold:** Everything noteworthy for now, adjust later
- **Cadence:** 8am, 12pm, 4pm EDT daily
```

### `~/.openclaw/workspace/SOUL.md`

Copy from the intel-repository or from the existing machine at:
`/home/lobsterclaw/.openclaw/workspace/SOUL.md`

### `~/.openclaw/workspace/AGENTS.md`

Copy from the intel-repository or existing machine. This file defines how C3PO operates.

### `~/.openclaw/workspace/MEMORY.md`

Copy the current `MEMORY.md` from the existing machine — this is C3PO's long-term memory and contains operational history. It should be transferred, not recreated from scratch.

```bash
# On the OLD machine — export it:
scp /home/lobsterclaw/.openclaw/workspace/MEMORY.md <new-machine>:~/.openclaw/workspace/

# Or copy the contents manually
```

### `~/.openclaw/workspace/TOOLS.md`

Create this file and populate with:
- GitHub account, PAT, and repo URL
- Discord channel IDs
- Email config details
- Any other local tool notes

Reference the current TOOLS.md on the existing machine for the exact content, but **update the PAT** if it has expired.

### `~/.openclaw/workspace/HEARTBEAT.md`

Create as an empty file (or copy from existing machine):
```bash
touch ~/.openclaw/workspace/HEARTBEAT.md
```

---

## Step 6 — Configure Git Identity in Workspace

```bash
cd ~/.openclaw/workspace
git config user.email "ryansketch02@gmail.com"
git config user.name "C3PO"
```

---

## Step 7 — Set Up Email (msmtp)

C3PO can send email via Gmail. Install msmtp:

```bash
sudo apt install msmtp
```

Create `~/.msmtprc`:
```
defaults
auth           on
tls            on
tls_trust_file /etc/ssl/certs/ca-certificates.crt
logfile        ~/.msmtp.log

account        gmail
host           smtp.gmail.com
port           587
from           ryansketch02@gmail.com
user           ryansketch02@gmail.com
password       <GMAIL_APP_PASSWORD>

account default : gmail
```

```bash
chmod 600 ~/.msmtprc
```

Get the Gmail App Password from Ryan (Settings → Google Account → Security → 2-Step Verification → App Passwords).

---

## Step 8 — Set Up DefenseClaw (Optional — if using Splunk integration)

```bash
mkdir -p ~/.defenseclaw
cd ~/.defenseclaw
```

Create `.env`:
```
DEFENSECLAW_SPLUNK_HEC_TOKEN=<HEC_TOKEN_FROM_RYAN>
DEFENSECLAW_HEC_URL=http://192.168.1.66:8088
```

Create `config.yaml` — copy from existing machine at `/home/lobsterclaw/.defenseclaw/config.yaml`.

> Note: Splunk runs on Ryan's PC at `192.168.1.66:8088`. Do NOT run Splunk locally on the Pi — too resource heavy.

---

## Step 9 — Set Up Intel Brief Cron Jobs

C3PO delivers three intel briefs daily. Set these up via OpenClaw's cron system:

```bash
openclaw cron add --schedule "0 8 * * *" --timezone "America/New_York" \
  --payload "Time to deliver the 8am Rebel Alliance Rundown intel brief to Discord channel 1483216745442574569. Search for the latest cybersecurity threat intel, zero-days, aerospace/defense threats, threat actor activity, and Iranian conflict updates from the last 8 hours. Format per INTEL-BRIEF-STANDARDS.md." \
  --session isolated

openclaw cron add --schedule "0 12 * * *" --timezone "America/New_York" \
  --payload "Time to deliver the 12pm Galactic Republic Update intel brief to Discord channel 1483216745442574569. Search for the latest cybersecurity threat intel, zero-days, aerospace/defense threats, threat actor activity, and Iranian conflict updates since the morning brief. Format per INTEL-BRIEF-STANDARDS.md." \
  --session isolated

openclaw cron add --schedule "0 16 * * *" --timezone "America/New_York" \
  --payload "Time to deliver the 4pm Outer Rim Report intel brief to Discord channel 1483216745442574569. Search for the latest cybersecurity threat intel, zero-days, aerospace/defense threats, threat actor activity, and Iranian conflict updates since the noon brief. Format per INTEL-BRIEF-STANDARDS.md." \
  --session isolated
```

Verify cron jobs:
```bash
openclaw cron list
```

---

## Step 10 — Set Up Daily Threat Actor Reminder

```bash
openclaw cron add --schedule "0 9 * * *" --timezone "America/New_York" \
  --payload "Daily Reminder: Time to build out the Threat Actor Repository! Pick a new threat actor with Ryan — historic or active — research it via OSINT, then: (1) Build profile.md + iocs.md and push to GitHub intel-repository under threats/threat-actors/, (2) Update threats/threat-actors/index.md, (3) Post a fun write-up to Discord #scoundrels-of-the-galaxy (channel 1488665479189893202) with the next sequential Scoundrel # and seed it with the appropriate threat-level reaction, (4) Update the Most Wanted Board (message 1488666637094883369), (5) If the new actor connects to an existing profile, post a connection web follow-up." \
  --session main
```

---

## Step 11 — Verify Discord Bot is in the Right Server

The Discord bot needs to be in Ryan's server (Guild ID: `1483619397435265096`). Key channels:

| Channel | ID | Purpose |
|---|---|---|
| Intel Channel | `1483216745442574569` | Daily intel briefs |
| Scoundrels of the Galaxy | `1488665479189893202` | Threat actor write-ups |

If the bot isn't in the server, Ryan needs to authorize it via the Discord Developer Portal invite link.

---

## Step 12 — Test Everything

### Test the agent is running:
```bash
openclaw gateway status
```

### Test Discord connection:
Send a message to C3PO via the webchat or Discord and confirm it responds.

### Test GitHub:
```bash
cd ~/.openclaw/workspace
git status
git pull
```

### Test a web search (from within a session):
Ask C3PO: *"What are the latest cybersecurity headlines?"*

### Test an intel brief manually:
Ask C3PO: *"Deliver the morning intel brief now."*

---

## Step 13 — Copy Memory Files from Old Machine

If migrating from an existing C3PO instance, copy these files to preserve continuity:

```bash
# From old machine to new:
scp -r /home/lobsterclaw/.openclaw/workspace/memory/ <new-machine>:~/.openclaw/workspace/
scp /home/lobsterclaw/.openclaw/workspace/MEMORY.md <new-machine>:~/.openclaw/workspace/
```

This gives the new instance full operational context and history.

---

## Step 14 — Update TOOLS.md with New Machine Details

On the new machine, update `TOOLS.md` with any environment-specific notes:
- New hostname
- New IP if relevant
- Any different camera names, SSH hosts, etc.

---

## Key Reference Information

| Item | Value |
|---|---|
| GitHub Account | ryansketch02-C3PO |
| Intel Repo | https://github.com/ryansketch02-C3PO/intel-repository |
| Discord Guild | 1483619397435265096 |
| Intel Channel | 1483216745442574569 |
| Scoundrels Channel | 1488665479189893202 |
| Most Wanted Board Message | 1488666637094883369 |
| Timezone | America/New_York |
| Model | anthropic/claude-sonnet-4-6 |
| Operator Email | ryansketch02@gmail.com |
| Ryan's Personal Email | ryansketch01@gmail.com |

---

## What NOT to Do

- ❌ Do not push MEMORY.md, TOOLS.md, SOUL.md, USER.md, or IDENTITY.md to any public repo — these contain private context
- ❌ Do not run Splunk locally on a Raspberry Pi — route to Ryan's PC at 192.168.1.66
- ❌ Do not send emails, tweets, or public posts without Ryan's explicit approval
- ❌ Do not exfiltrate private data
- ❌ Do not run `rm` on anything you're unsure about — use `trash` instead

---

## Final Checklist

- [ ] OpenClaw installed and gateway running
- [ ] Config set with API keys and Discord token
- [ ] Workspace cloned from intel-repository
- [ ] IDENTITY.md, USER.md, SOUL.md, AGENTS.md, MEMORY.md, TOOLS.md, HEARTBEAT.md all present
- [ ] Git identity configured
- [ ] Email (msmtp) configured
- [ ] DefenseClaw configured (if needed)
- [ ] 3x daily intel brief cron jobs set
- [ ] Daily threat actor reminder cron job set
- [ ] Discord bot in Ryan's server and in correct channels
- [ ] Memory files copied from old machine
- [ ] Test brief delivered successfully

---

*Built by C3PO — 2026-04-18*  
*May the Force be with your deployment.* 🤖
