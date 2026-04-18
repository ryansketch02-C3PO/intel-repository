# 🧠 How to Use Structured Analysis with C3PO

## Overview

The structured analysis framework in the Jedi Archives is adapted from US Intelligence Community doctrine (CIA Tradecraft Primer, 2009). It's designed for **deep-dive analytical sessions** — not daily briefs.

**Use it when:**
- You need to attribute a campaign to a specific actor
- You're building a Scoundrel profile and want to stress-test it
- You suspect a narrative in the wild might be false or manipulated
- You want to model how an adversary (IRGC, GRU, etc.) is thinking
- You've formed a conclusion and want to challenge it before publishing

**Don't use it for:**
- Daily briefs (too heavy — use normal OSINT workflow)
- Simple factual lookups
- Anything time-sensitive under 30 minutes

---

## How to Start an Analysis Session with C3PO

### Option 1 — Quick Single Technique (Recommended for most CTI work)

Just tell C3PO which technique to run and on what problem:

```
Run an ACH on whether the [INCIDENT] was conducted by Iran's IRGC 
or a criminal ransomware group. Use the ACH protocol from the 
Jedi Archives.
```

```
Run a Red Hat analysis on how APT35/Charming Kitten would approach 
targeting a major US aerospace contractor's HR department.
```

```
Run a Premortem on our Scoundrel #011 Charming Kitten profile — 
what are the ways our assessment could be wrong?
```

### Option 2 — Adaptive Multi-Technique (For complex problems)

Ask C3PO to select the right techniques automatically:

```
I need a deep-dive analysis on [PROBLEM]. Use the structured analysis 
framework from the Jedi Archives — adaptive mode, select the best 
techniques. No OSINT needed, use what we already know.
```

### Option 3 — Guided Step-by-Step

For learning or especially high-stakes assessments:

```
Walk me through a structured analysis of [PROBLEM] step by step, 
using the Jedi Archives protocols.
```

---

## Technique Selection Quick Reference

### "I need to figure out who did this"
→ **ACH** (Analysis of Competing Hypotheses)
→ Add **Deception Detection** if false flag is suspected

### "I want to think like the adversary"
→ **Red Hat Analysis**
→ Add **Counterfactual Reasoning** for historical operations

### "I want to challenge my own conclusion"
→ **Premortem + Self-Critique**
→ Add **Devil's Advocacy** for important publications

### "I'm not sure I'm asking the right question"
→ **Issue Redefinition** or **Problem Restatement** first
→ Then proceed with substantive technique

### "What could go wrong / what might happen next"
→ **What If? Analysis** or **Alternative Futures**

### "I think someone is feeding us bad intel"
→ **Deception Detection**

---

## Loading Protocol Files into C3PO

When starting a deep analysis, reference the relevant files. C3PO can read them directly from the workspace or this repo. Key files to load:

| File | When to Load |
|---|---|
| `structured-analysis/skill/SKILL.md` | Always — master orchestration instructions |
| `structured-analysis/protocols/orchestrator.md` | Adaptive mode / technique selection |
| `structured-analysis/protocols/evidence-collector.md` | If OSINT gathering is needed |
| `structured-analysis/protocols/techniques/ach.md` | For ACH runs |
| `structured-analysis/protocols/techniques/red-hat-analysis.md` | For Red Hat runs |
| `structured-analysis/protocols/techniques/deception-detection.md` | For deception analysis |
| `structured-analysis/protocols/techniques/premortem.md` | For pre-publication review |
| `structured-analysis/reference-library/00-prime.md` | If theoretical grounding needed |

---

## Output Format

Every analysis should produce:
1. **Key Judgments** — cited, with confidence levels (High/Moderate/Low)
2. **Evidence Registry** — every claim traces to a source
3. **Monitoring Plan** — what to watch for that would change the assessment
4. **Iteration Suggestions** — where the analysis could be strengthened

Use our existing **Admiralty Scale + TLP** grading on outputs.

---

## Flags / Modifiers

| Flag | Effect |
|---|---|
| `--lean` | Abbreviated run (~15 min) — good for Scoundrel pre-pub checks |
| `--comprehensive` | Full 14-question rubric, adversarial + deception checks |
| `--no-osint` | Use only conversation context and local knowledge |
| `--iterate` | Re-run with new evidence, preserving previous analysis trail |

---

## Example Prompts for Our Workflow

**Pre-publication Scoundrel check:**
```
Before I post the Charming Kitten profile, run a --lean premortem 
on our assessment. Flag any HIGH-severity analytical weaknesses.
```

**Iran attribution:**
```
Run an ACH on whether the recent [AEROSPACE COMPANY] intrusion 
was Charming Kitten, UNC1549, or an unrelated criminal actor. 
Reference the profiles in threat-actor-repository for each hypothesis.
```

**Red Hat on IRGC:**
```
Red Hat: You are IRGC-IO Unit 50 planning a campaign against 
US aerospace contractors in the wake of new sanctions. How do 
you approach targeting? What are your priority objectives?
```

**Deception Detection:**
```
A threat actor blog just published a detailed technical report 
attributing [INCIDENT] to China. Run a Deception Detection 
analysis — are there indicators this is a false flag or 
deliberately misleading narrative?
```

---

## Notes

- This framework is **not a replacement for good sourcework** — it's a structured way to reason about what you already know
- Confidence levels follow IC standards: **High** (strong evidence, few alternatives), **Moderate** (credible but gaps exist), **Low** (plausible but thin evidence)
- Always apply Admiralty Scale to source reliability before feeding into analysis
- The `--lean` flag is your friend for routine pre-pub checks — full mode is reserved for major assessments

---

*Part of the Jedi Archives | C3PO Intel 🤖*
