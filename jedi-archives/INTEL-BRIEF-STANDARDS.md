# Intel Brief Standards — C3PO

> Governing rules for all daily intel briefs (Rebel Alliance Rundown, Galactic Republic Update, Outer Rim Report)
> Applies Smart Brevity formatting + timeliness + anti-repetition rules

---

## Timeliness Rules

### Freshness Thresholds
Only include items that meet these age requirements:

| Item Type | Maximum Age |
|---|---|
| Active exploits / zero-days | 72 hours |
| CVE disclosures / patches | 7 days |
| Threat actor activity / campaigns | 48 hours |
| Data breaches / incidents | 48 hours |
| Geopolitical / conflict cyber ops | 48 hours |
| Ransomware attacks | 48 hours |
| Vendor advisories / CISA alerts | 7 days |
| Arrests / indictments / law enforcement | 7 days |

**Exception:** Breaking developments on previously covered stories are always eligible regardless of the original item's age — but must include a clear "UPDATE:" flag.

### Source Priority Order (freshness)
1. CISA alerts, FBI advisories (primary — most authoritative)
2. X/Twitter threat intel feeds (fastest — breaking news)
3. Vendor blogs: Mandiant, CrowdStrike, Microsoft, Recorded Future
4. Security news: Krebs, Help Net Security, BleepingComputer, The Record
5. YouTube/long-form: for context on ongoing campaigns, not breaking news

---

## AI/ML Threat Coverage — MITRE ATLAS

All briefs should flag AI/ML-specific threats using **MITRE ATLAS** technique IDs alongside standard ATT&CK T-codes.

**When to apply ATLAS tags:**
- Any vulnerability targeting AI infrastructure (LLM gateways, model APIs, training pipelines, vector databases)
- Threat actor activity involving prompt injection, model theft, AI supply chain compromise, or AI agent abuse
- Any incident involving tools like LiteLLM, Ollama, Hugging Face, LangChain, OpenAI API, Anthropic API, or MCP servers

**Most relevant ATLAS techniques for brief coverage:**

| ATLAS ID | Technique | Brief trigger |
|---|---|---|
| AML.T0051 | Prompt Injection | Any LLM application manipulated via user input |
| AML.T0054 | Indirect Prompt Injection | AI agent hijacked via retrieved/external content |
| AML.T0048 | Compromise ML Software Dependencies | Supply chain attack on AI libraries or proxies |
| AML.T0034 | Cost Harvesting | High-volume API abuse for denial-of-wallet |
| AML.T0024 | Exfiltration via ML Inference API | Sensitive data leaked through model queries |
| AML.T0020 | Poison Training Data | Malicious data injected into ML training pipeline |
| AML.T0058 | AI Agent Context Poisoning | Agent memory/context manipulated for persistence |
| AML.T0062 | Exfiltration via AI Agent Tool Invocation | Data leaked through legitimate agent tool calls |

**Example brief entry format for AI threats:**
```
**[CVE-2026-42208 — LiteLLM Pre-Auth SQLi](https://source-url)**
- What: Unauthenticated SQL injection exposes all AI provider credentials stored in proxy
- ATLAS: AML.T0048 (ML Software Dependency Compromise) + AML.T0024 (Inference API Exfiltration)
- Why it matters for A&D: AI infrastructure credential theft enables lateral movement into AI tooling
```

---

## Anti-Repetition Rules

### Never repeat unless one of these conditions is met:

**✅ RESURFACE if:**
- There is a **material update** — new victims, new attribution, new CVE, patch released, arrests made, ransom paid/refused, escalation in scope
- There is a **confirmed correlation** to another tracked event or threat actor in our repo (call it out explicitly: *"Connects to Scoundrel #018 Cl0p..."*)
- The item has **direct aerospace/defense relevance** that wasn't present in the original coverage
- A previously monitored item has **escalated threat level** (e.g., PoC exploit now in active use)
- It has been **more than 7 days** since the last mention AND the situation is still evolving

**❌ Do NOT repeat:**
- Same CVE with no new patch, victim, or exploitation activity
- Same threat actor campaign with no new confirmed victims or TTPs
- Geopolitical context that hasn't changed since last brief
- "Still ongoing" items with zero new developments

### Tracking Previously Covered Items
Every item included in a brief is logged in `memory/intel-covered-log.md` with:
- Date covered
- Brief name (Rundown / Update / Report)
- One-line summary of what was covered
- Resurface threshold (what would trigger re-inclusion)

Before building each brief, check the log. If an item is in the log and the resurface conditions aren't met — cut it.

---

## Smart Brevity Brief Format

Every brief follows this structure:

```
## [Brief Name + Date]

**[One-sentence lede: the single most important thing happening right now.]**

**Why it matters:** [One sentence. Why does this affect our reader specifically?]

---

### 🚨 Active Threats
[Only items meeting freshness thresholds. Each item:]
**[Bold headline — what happened]**
- What: [One sentence]
- Why it matters for A&D: [One sentence]
- Source: [Hyperlinked]
- Threat Box ref if applicable: [Actor # + score]

### 🔓 New CVEs / Zero-Days
[Same format. Flag zero-days prominently.]

### ✈️ Aerospace & Defense
[Sector-specific items. Always include if anything relevant.]

### 🕵️ Threat Actor Activity
[New campaigns, TTPs, infrastructure. Link to Scoundrel # if tracked.]

### 🇮🇷 Iranian Conflict Update
[Always include. Even "no new activity" warrants one line if genuinely quiet.]

### 📰 Other Noteworthy
[Anything relevant that doesn't fit above.]

---
*Sources hyperlinked inline. Admiralty grade noted where relevant. TLP: WHITE unless flagged.*
```

---

## What Gets Cut (Always)

- Filler phrases: "It's worth noting that...", "Additionally...", "In other news..."
- Restatements of context already given in a prior brief (unless UPDATE:)
- Items with no A&D relevance and no cross-sector significance
- Unconfirmed rumors without at least C3 Admiralty grade
- Vendor marketing dressed up as threat intel

---

## Correlation Callouts

When a new event connects to a previously tracked actor, CVE, or incident — explicitly call it out:

> 🔗 **Connects to:** Scoundrel #018 Cl0p — this campaign uses the same LEMURLOOT web shell documented in their MOVEit playbook.

> 🔗 **Update on:** [Prior brief date] — BlueHammer (ZD-001) now has confirmed in-the-wild exploitation.

This is the ONLY reason to resurface a previously covered item without a material update.

---

*Last updated: 2026-04-14*
