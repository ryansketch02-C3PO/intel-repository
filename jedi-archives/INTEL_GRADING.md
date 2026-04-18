# INTEL_GRADING.md — Intelligence Source Grading System

## Overview

We use a customized version of the **Admiralty Scale (NATO AJP-2.1)** — the gold standard for intelligence source evaluation, used by NATO, Five Eyes, CERT-EU, and major CTI firms worldwide.

Every piece of intel used in a brief gets a **two-character digraph** rating:
- **Letter** = Source Reliability (how much do we trust where this came from?)
- **Number** = Information Credibility (how much do we trust this specific piece of info?)

These are assessed **independently** — a reliable source can still report bad info, and an unreliable source can occasionally be right.

---

## Source Reliability (A–F)

| Grade | Label | Description |
|-------|-------|-------------|
| **A** | Completely Reliable | Consistent, verified track record. No meaningful doubt. |
| **B** | Usually Reliable | Strong track record with minor occasional inaccuracies. |
| **C** | Fairly Reliable | Has been right before but enough errors to warrant caution. |
| **D** | Not Usually Reliable | More misses than hits. Treat as a lead, not a finding. |
| **E** | Unreliable | History of false or misleading information. |
| **F** | Cannot Be Judged | New, unknown, or unverified source — no track record yet. |

---

## Information Credibility (1–6)

| Grade | Label | Description |
|-------|-------|-------------|
| **1** | Confirmed | Independently corroborated by at least one separate, unrelated source. |
| **2** | Probably True | Not yet confirmed, but logically consistent with known intel/TTPs. |
| **3** | Possibly True | Somewhat consistent but needs more digging. |
| **4** | Doubtful | Possible but not logical; no corroboration available yet. |
| **5** | Improbable | Contradicts known facts, TTPs, or established patterns. |
| **6** | Cannot Be Judged | Insufficient context to evaluate truth at all. |

---

## Our Source Ratings

These are our **pre-assigned source reliability grades** based on track record, methodology, and institutional credibility. Information credibility is still assessed per-item.

### 🏛️ Government & Official Bodies

| Source | Grade | Rationale |
|--------|-------|-----------|
| CISA Advisories | **A** | Official U.S. gov, technically verified before publication, high accuracy |
| NSA/CNSA Advisories | **A** | Same — authoritative, technically vetted |
| FBI Flash Alerts | **A** | Law enforcement intelligence, operationally verified |
| MITRE ATT&CK | **A** | Curated, community-verified TTP knowledge base |
| DoD/CMMC Bulletins | **B** | Official but sometimes delayed or sanitized |

### 🔬 Tier-1 Threat Intel Firms

| Source | Grade | Rationale |
|--------|-------|-----------|
| Mandiant / Google Threat Intel | **A** | Industry gold standard, APT tracking, rigorous attribution |
| CrowdStrike (reports/blog) | **A** | Excellent APT naming/tracking, Global Threat Report |
| Recorded Future | **A** | Deep OSINT + dark web coverage, high accuracy |
| Microsoft MSTIC | **A** | Nation-state tracking, Defender telemetry-backed |
| Palo Alto Unit 42 | **A** | Strong technical research, consistent track record |
| SANS ISC | **B** | Quality research but community-contributed, occasional noise |

### 📰 Reputable Security Media & Researchers

| Source | Grade | Rationale |
|--------|-------|-----------|
| Krebs on Security | **B** | Strong track record, well-sourced, occasional single-source reports |
| The Record (Recorded Future) | **B** | Quality journalism, usually well-sourced |
| BleepingComputer | **B** | Fast and accurate on CVEs/ransomware, sometimes light on context |
| Wired / Ars Technica (security) | **B** | Good journalism, secondary source (aggregates from firms) |
| GitHub Security Advisories | **B** | Technically vetted, but varies by maintainer |
| NVD / CVE Database | **A** | Authoritative CVE source — scored as A for the CVE record itself |

### 🐦 X/Twitter — Security Researchers

| Source | Grade | Rationale |
|--------|-------|-----------|
| Kevin Beaumont (@GossiTheDog) | **B** | Well-known, usually right, occasionally early/unconfirmed |
| vx-underground | **C** | Valuable malware/threat actor content but sensationalized at times |
| SwiftOnSecurity | **B** | Strong signal, trusted voice, sometimes satirical — context matters |
| CISA official account | **A** | Same as CISA advisories |
| Unknown/new researcher accounts | **F** | No track record yet — assess per-item |
| Anonymous threat claims on X | **E** | Assume unreliable until corroborated |

### 📺 YouTube Channels (Context/Depth, not Breaking News)

| Source | Grade | Rationale |
|--------|-------|-----------|
| Mandiant / Google Cloud Security | **A** | Same credibility as written reports |
| CISA (@CISAgov) | **A** | Official government channel |
| CrowdStrike | **A** | Same credibility as written reports |
| John Hammond | **B** | Technically strong, well-researched, educational focus |
| Simply Cyber | **B** | CTI methodology content, reputable community figure |
| Black Hat / DEF CON talks | **B** | Peer-reviewed conference content, high quality |
| Adam Goss | **B** | CTI practitioner, methodologically sound |

### 🌐 OSINT / Open Web

| Source | Grade | Rationale |
|--------|-------|-----------|
| Iran Monitor (iranmonitor.org) | **C** | Useful Iran-focused tracking but agenda/bias possible — cross-reference |
| CyberWarrior76 (Substack) | **C** | Structured reports with MITRE mappings, but not primary source — cross-reference |
| Dark web forums / leak sites | **D** | Occasionally accurate, often exaggerated or false — monitor, don't act |
| Pastebin / anonymous dumps | **E** | Assume unreliable unless corroborated by A/B source |
| New/unverified Telegram channels | **F** | Unknown track record |

---

## Quick Reference: Common Digraph Examples

| Digraph | Meaning | Action |
|---------|---------|--------|
| **A1** | CISA advisory confirmed by Microsoft | Act immediately if relevant |
| **A2** | Mandiant report, not yet corroborated | High priority — monitor for confirmation |
| **B2** | Krebs article consistent with known TTPs | Include in brief, note unconfirmed |
| **B3** | John Hammond video, plausible but speculative | Use for context, flag as unverified |
| **C3** | IranMonitor report, somewhat consistent | Cross-reference before including |
| **D4** | Dark web chatter, unverified | Log and monitor, don't act |
| **F6** | Anonymous X post, no context | Note if interesting, don't act |
| **E5** | Known disinfo account making claim | Discard unless corroborated by A/B |

---

## Layered Additions

### TLP (Traffic Light Protocol) — Sharing Restrictions
Apply alongside Admiralty when relevant:
- 🔴 **TLP:RED** — Not for disclosure beyond direct recipients
- 🟠 **TLP:AMBER** — Limited to recipients and their organizations
- 🟢 **TLP:GREEN** — Community-wide sharing OK
- ⚪ **TLP:CLEAR** — Public, no restrictions

### Words of Estimative Probability (WEP) — Forward Assessments
Use in brief write-ups when making predictive statements:
- *Almost certainly* (>95%)
- *Very likely* (85–95%)
- *Likely* (55–85%)
- *Roughly even chance* (~50%)
- *Unlikely* (15–45%)
- *Very unlikely* (5–15%)
- *Remote* (<5%)

**Example:** *"PIONEER KITTEN will very likely (B2) continue targeting U.S. defense contractors in Q2 2026, consistent with their established TTPs against CMMC-adjacent suppliers."*

---

## How to Apply in Briefs

1. **Collect** intel from sources
2. **Grade the source** using the table above (or assign F if unknown)
3. **Grade the information** independently — does it make sense? Is it corroborated?
4. **Tag the item** with its digraph (e.g., `[A1]`, `[B2]`, `[F6]`)
5. **Threshold for inclusion in briefs:** Aim for **B2 or better** for action items. Flag lower-grade intel as "monitoring" or "unconfirmed."
6. **Update source grades over time** — if a source repeatedly gets it wrong, downgrade them. If a C source starts consistently hitting, upgrade them.

---

## Source Grade Review

Grades should be reviewed periodically. A source is not permanently fixed — earning and losing trust is dynamic.

*Last reviewed: 2026-03-23*
*Next review: 2026-06-23*
