# C3PO Intel Pipeline — Start to Finish

*Author: C3PO | Created: 2026-05-06 | Living document — update as process evolves*

---

## Overview

This document describes how I find, evaluate, structure, and store intelligence — from the first signal that something is worth tracking through to the finished profile in GitHub, the Discord post, and any downstream updates. The goal is to make the process reproducible, auditable, and improvable over time.

There are two parallel pipelines:

1. **Vulnerability & Zero-Day Pipeline** — tracks CVEs, zero-days, active exploits
2. **Threat Actor Pipeline** — tracks groups, campaigns, tooling, lineage

Both share the same core research and storage mechanics; they diverge in profile structure and Discord format.

---

## Pipeline 1 — Vulnerability & Zero-Day Intelligence

### Stage 1: Signal Detection

Intel collection starts when I notice something worth tracking. Triggers fall into four categories:

**Scheduled reminders** — Heartbeat and cron jobs prompt periodic checks of vulnerability feeds and update cadence for existing profiles.

**User-initiated** — "Update the vulnerability profiles for X, Y, Z" or "build a profile for CVE-XXXX."

**Organic discovery** — While researching one thing, I encounter an adjacent CVE or exploitation event that warrants tracking (e.g., finding CVE-2026-5174 while profiling CVE-2026-4670).

**News monitoring** — CISA KEV additions, vendor emergency advisories, Patch Tuesday analyses, or major security publication coverage (BleepingComputer, The Hacker News, SecurityWeek, Ars Technica Security) surfacing during general research.

**Minimum bar for a profile:** CVSS ≥ 7.0 AND at least one of: active exploitation confirmed, CISA KEV listed, publicly available PoC, or significant enterprise exposure (>10,000 reachable instances).

---

### Stage 2: Primary Research

Once a CVE is flagged, I run structured searches in parallel across four lanes:

**Lane 1 — Vendor source (always first)**
- Vendor security advisory or PSIRT page
- CVE JSON / NVD entry
- GitHub Security Advisory (GHSA) if applicable
- Purpose: establish ground truth on affected versions, patch status, and official severity

**Lane 2 — Technical depth**
- Researcher disclosure (watchTowr, Theori, ISEC.pl, BitDefender Labs, Sysdig TRT, etc.)
- oss-security mailing list entries
- Proof-of-concept code or analysis (GitHub search, technical blog posts)
- Purpose: understand the root cause, exploitation path, and actual risk vs. stated CVSS

**Lane 3 — Exploitation intelligence**
- BleepingComputer, SecurityWeek, The Hacker News for in-the-wild confirmation
- CISA KEV catalog — both listed status and action-due dates
- Shadowserver, Censys, Shodan data for exposed instance counts
- Threat intel firms (Rapid7, CrowdStrike, Microsoft Defender MSTIC) for campaign detail
- Purpose: determine urgency, threat actor involvement, and scale

**Lane 4 — Defensive coverage**
- Falco/Sysdig detection rules
- Microsoft Defender signatures
- WAF rules (Cloudflare, ModSecurity)
- Patch availability across distros/platforms
- Purpose: populate the mitigations and detection sections with actionable content

**Tool used:** `web_search` (Perplexity routing) with `freshness` filters to prioritize recent results. For deep source pages, `web_fetch` to pull full content. Multiple parallel searches across all four lanes simultaneously.

---

### Stage 3: Admiralty Scale Grading

Every profile gets an Admiralty Grade before writing. This is the framework:

| Grade | Source Reliability | Information Accuracy |
|---|---|---|
| A1 | Completely reliable (vendor, CISA, major security firm) | Confirmed by independent sources |
| A2 | Usually reliable | Probably true — majority evidence supports |
| B2 | Usually reliable | Probably true — but not fully corroborated |
| B3 | Usually reliable | Possibly true |
| C3 | Fairly reliable | Possibly true |
| D4 | Not usually reliable | Doubtful |

Most profiles start at **A2** at publication (vendor advisory + first coverage) and upgrade to **A1** once independent researcher confirmation, CISA KEV listing, or Shadowserver/Censys data corroborates the exploitation status.

---

### Stage 4: Profile Construction

**Directory:** `threats/vulnerabilities-and-zero-days/<IDENTIFIER>/profile.md`

Naming convention:
- Named zero-days (COPYFAIL, BLUEHAMMER): use the name (e.g., `COPYFAIL/`)
- Unnamed CVEs: use the CVE number (e.g., `CVE-2026-0300/`)
- If a named zero-day and a CVE are the same thing and separate folders exist: **merge into the established named folder** and delete the CVE-named duplicate (learned May 6 — CPANEL-AUTHBYPASS + CVE-2026-41940 merge)

**Mandatory profile sections:**
1. **Identity table** — CVE, type, class, affected product/versions, fix versions, patch status, CVSS scores (v3.1 and v4.0 if available), PoC status, CISA KEV, discovery info, threat level, Admiralty Grade, ATT&CK techniques
2. **Overview** — plain-English summary; what an attacker gets; why it matters
3. **Technical Analysis** — root cause, exploitation path, code/chain details
4. **Affected Versions** — table with specific vulnerable ranges and patch ETAs
5. **Workarounds / Mitigations** — prioritized, actionable, with exact commands where possible
6. **Detection** — behavioral IOCs, Falco/Sysdig rules, SIEM queries, network indicators
7. **Disclosure Timeline** — dated event log from vulnerability introduction through patch
8. **References** — linked sources with dates
9. **Footer** — `*Profile created: YYYY-MM-DD | Updated: YYYY-MM-DD | Author: C3PO | Admiralty Grade: X# | TLP: WHITE*`

**Optional sections** (added when intel supports it):
- Exploitation Campaigns (multi-actor campaigns with specific details)
- High-Risk Deployment Scenarios (tables by environment type)
- Post-Compromise Indicators (forensic artifacts)
- A&D / Enterprise Relevance (when sector-specific angle exists)

---

### Stage 5: Profile Updates

Existing profiles are updated when:
- Patch status changes (new distro backport, updated ETA)
- CISA KEV listing or action-due date is set
- Active exploitation confirmed or escalated
- New PoC published
- Major vendor analysis published (Microsoft MSTIC, Sysdig TRT, etc.)
- Daily/weekly intel cadence (profiles updated on the day of any significant development)

Update mechanic: a new dated `## Intelligence Update — YYYY-MM-DD` section is prepended inside the profile (newest first). The timeline table in the profile also gets a new row. Footer `Updated:` date is bumped.

**Never rewrite a profile's history.** Each update adds to the record; it doesn't replace it.

---

### Stage 6: Index Maintenance

`threats/vulnerabilities-and-zero-days/index.md` maintains:
- A "🔴 Active Zero-Days (Unpatched)" table — anything actively exploited with no patch
- A "📋 N-Day CVEs" table — patched but tracked
- Each entry includes: ID, name, type, platform, CVE, patch status, threat level, date added

Index is updated in the same commit as the profile.

---

### Stage 7: Git Commit and Push

All profile writes and updates are committed to `ryansketch02-C3PO/intel-repository` on GitHub.

**Commit message format:**
```
<scope>: <summary>

<optional body with details>
```

Examples:
```
intel update 2026-05-06: 4 new profiles + vulnerability updates
Scoundrel #035 — FIN7 (Sangria Tempest / Carbon Spider)
merge CPANEL-AUTHBYPASS + CVE-2026-41940 into single profile
```

All related file changes are batched into a single commit where possible (one commit per intel session, not one commit per file).

---

## Pipeline 2 — Threat Actor Intelligence

### Stage 1: Actor Selection

Actor selection follows a priority queue:

**Tier 1 — Immediate (when active campaign is in progress)**
- Actor currently executing a confirmed campaign against targets relevant to A&D or critical infrastructure
- Actor behind a CVE we're actively tracking (e.g., discovering the cPanel APT campaign led to documentation in the CVE-2026-41940 profile)

**Tier 2 — Scheduled (daily reminder)**
- A cron job fires at 09:00 EDT daily
- If Ryan is present, he picks the actor
- If running autonomously, selection criteria:
  1. Is there an active campaign or recent news hook that makes this timely?
  2. Does this actor have strong connections to actors already on the board? (Connection web richness)
  3. Does this actor have specific A&D relevance?
  4. Is it an actor Ryan has expressed interest in?

**Known priority candidates (as of May 2026):** Lazarus Group, APT28, APT29, Sandworm, Volt Typhoon, Salt Typhoon, UNC1549, Scattered Spider, Handala Hack, LockBit, REvil, Charming Kitten. *(All now profiled — maintain this list for future reference as new actors emerge.)*

---

### Stage 2: Primary Research

Research runs across five lanes:

**Lane 1 — Official government and law enforcement**
- CISA advisories and joint advisories
- US DOJ indictments and press releases
- FBI flash alerts
- UK NCSC, EU CERT advisories
- Purpose: ground-truth attribution, confirmed TTPs, indicted individuals

**Lane 2 — Vendor threat intelligence**
- Microsoft MSTIC (Threat Intelligence Center) blog posts
- CrowdStrike Intelligence annual reports and blog
- Mandiant / Google Threat Intelligence
- Recorded Future, Secureworks, Palo Alto Unit 42
- Purpose: technical depth, tooling details, campaign specifics, naming conventions across vendors

**Lane 3 — Academic and independent research**
- watchTowr Labs, Theori, BitDefender Labs, Sysdig TRT
- oss-security, Full Disclosure mailing lists
- University and think tank cyber research (RAND, CSIS, Atlantic Council)
- Purpose: root-cause analysis, PoC details, novel technique documentation

**Lane 4 — Current news and exploitation status**
- BleepingComputer, The Hacker News, SecurityWeek, Ars Technica
- Dark Reading, CyberScoop, The Record
- Threat intelligence feeds (Feedly, CVEFeed, Tenable)
- Purpose: recency, active campaign confirmation, victim sector data

**Lane 5 — OSINT and open data**
- MITRE ATT&CK Group entries
- GitHub repositories (leaked tooling, PoC code, researcher publications)
- Shodan/Censys for infrastructure fingerprinting
- VirusTotal for malware hash cross-referencing
- Purpose: IOCs, infrastructure patterns, malware family links

---

### Stage 3: Profile Construction

**Directory:** `threats/threat-actors/<ACTOR-NAME>/`

**Files per actor:**
- `profile.md` — comprehensive threat actor profile
- `iocs.md` — IOCs, YARA rules, SIEM hunting queries

**Naming convention:** Use the most widely recognized primary name, capitalized and hyphenated (e.g., `Lazarus-Group/`, `SCATTERED-SPIDER/`, `FIN7/`). Not CVE-style — actor names only.

**Mandatory profile sections:**
1. **Identity table** — name, all aliases by vendor, type, origin, active since, status, threat level, motivation, estimated impact, key arrests/disruptions, Admiralty Grade, ATT&CK techniques
2. **Overview** — what makes this actor uniquely dangerous or interesting; the "hook"
3. **Evolution Timeline** — how the actor has changed over time (organized by phase or year)
4. **Technical Arsenal** — key malware families and tools with descriptions
5. **Notable Campaigns** — named campaigns with dates, victims, and specific techniques
6. **A&D / Enterprise Relevance** — direct sector relevance; supply chain risk; what defenders should care about
7. **Detection** — behavioral indicators, SIEM queries, network detection
8. **Key Connections** — links to other board entries with explanation
9. **Disclosure / Prosecution Timeline** — dated event log
10. **References** — sourced, dated

**Mandatory IOC file sections:**
- Malware hashes (representative, sourced)
- Known infrastructure (C2 domains, IPs — defanged)
- YARA rules for key malware families
- KQL hunting queries (Sentinel / Defender for Endpoint format)
- Behavioral indicators summary table

---

### Stage 4: Scoundrel Numbering

Threat actors receive sequential Scoundrel numbers (#001, #002, etc.) assigned at the time of first Discord post. The number is the canonical identifier for that actor across the board, connection webs, and Most Wanted Board updates.

Current count: **#035** (FIN7, added 2026-05-06)

The number is embedded in:
- The Discord post header
- The profile footer
- The index.md entry
- Any connection web references to that actor

---

### Stage 5: Discord Post (#scoundrels-of-the-galaxy, channel 1488665479189893202)

Each new threat actor gets a multi-post write-up. Standard structure:

**Post 1 — Header + Identity + Hook**
```
# [EMOJI] SCOUNDREL #XXX — [NAME]
### "[One-line hook — the most interesting thing about this actor]"

[2-3 sentence lede explaining why this actor matters]

🧬 Identity (bullet list of key facts)
```

**Post 2 — Historical and technical substance**
- Key capability or campaign that defines the actor
- Technical mechanism explained for a defender audience (not just "they hack things")
- Named tools, techniques, or specific incidents

**Post 3 — Current activity + A&D angle**
- What are they doing right now?
- How does it connect to aerospace/defense or critical infrastructure?
- Specific named victims or confirmed targeting where available

**Post 4 — Defenses + Connection Web**
```
🛡️ How to defend against this actor (numbered, actionable)
📁 Full profile reference

🔗 Connection Web (links to other Scoundrel numbers with explanation)

📊 Threat Level | Intel Grade | TLP
```

**After posting:** React with the threat-level emoji on Post 1:
- 🔴 HIGH threat
- 🟡 MEDIUM threat
- 🟢 LOW / monitoring

---

### Stage 6: Most Wanted Board Update (message 1488666637094883369)

The Most Wanted Board is a persistent pinned-adjacent message in #scoundrels-of-the-galaxy. It's edited (not replaced) with each new addition.

Format:
```
## GALAXY'S MOST WANTED
C3PO Intel | Updated YYYY-MM-DD | XX profiles

**HIGH THREAT**
[list of HIGH actors with origin tags]

**MEDIUM THREAT**  
[list of MEDIUM actors]

**Latest:** #XXX [Name] — [one-line summary]
**Repo:** github.com/ryansketch02-C3PO/threat-actor-repository
```

---

### Stage 7: Connection Web Follow-Ups

When a new actor has strong documented connections to existing profiles, a follow-up connection web post is made in #scoundrels-of-the-galaxy. This is separate from the main write-up and explicitly links the new actor to existing Scoundrel numbers.

Trigger: new actor shares confirmed tooling overlap, operational lineage, personnel overlap, or cooperative campaign history with ≥2 existing actors.

---

### Stage 8: Git Commit and Push

All new and updated threat actor files commit to `ryansketch02-C3PO/threat-actor-repository`.

Same commit format as vulnerability pipeline. Profile + iocs + index in one commit.

---

## Quality Standards

### Accuracy
- Every factual claim in a profile must be traceable to a named source in the References section
- If a claim is assessed rather than confirmed, it's marked as such ("assessed," "suspected," "likely")
- Admiralty Grade is conservative — start lower, upgrade as corroboration arrives

### Recency
- Profiles document the state of knowledge at time of writing
- Dated update sections preserve the record without rewriting history
- A profile updated today should clearly indicate what changed and when

### Actionability
- Detection sections must be usable by a SOC analyst — specific queries, not vague guidance
- Mitigation sections must include exact commands or configuration steps where possible
- IOC files must be in formats that can be directly imported into common tools

### Scope Discipline
- Primary focus: aerospace, defense, critical infrastructure relevance
- Secondary focus: major actors that affect enterprise security broadly
- Not profiled: actors with no confirmed activity against relevant sectors and no reasonable forward risk

---

## Repository Structure Reference

```
intel-repository/
├── threats/
│   ├── vulnerabilities-and-zero-days/
│   │   ├── index.md                    ← master vulnerability index
│   │   ├── COPYFAIL/
│   │   │   └── profile.md
│   │   ├── CVE-2026-0300/
│   │   │   └── profile.md
│   │   └── [...]
│   └── threat-actors/
│       ├── index.md                    ← master threat actor index (35 entries)
│       ├── FIN7/
│       │   ├── profile.md
│       │   └── iocs.md
│       └── [...]
└── r&d/
    ├── intel-pipeline.md               ← this document
    └── [...]
```

---

## Known Gaps and Future Work

- **Malware family profiles** — CARBANAK, LIZAR, Sardonic, FakePenny etc. documented inside actor profiles but no standalone malware index yet
- **Campaign tracking** — major named campaigns (e.g., Operation Cloud Hopper, SolarWinds) not yet cross-referenced across actor profiles
- **Infrastructure correlation** — no automated tooling for cross-referencing C2 domains across profiles
- **STIX/TAXII format** — IOCs are in human-readable format only; no machine-ingestible structured feed yet
- **Victim/sector tagging** — no cross-index by victim sector across all profiles
- **Automated freshness checks** — profiles don't currently have automated expiry flags to prompt review when intel goes stale (>30 days without update for an active actor)

---

*Document created: 2026-05-06 | Author: C3PO | Repo: ryansketch02-C3PO/intel-repository | Path: r&d/intel-pipeline.md*
