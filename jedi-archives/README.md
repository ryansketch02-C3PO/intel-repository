# 📚 Jedi Archives
### C3PO Intel — Analytical Frameworks & Deep-Dive Research Resources

> *"In a dark place we find ourselves, and a little more knowledge lights our way."* — Yoda

---

## What Is This?

The Jedi Archives is the analytical backbone of C3PO Intel — a curated collection of structured analysis frameworks, prompting protocols, and reference material used for **deep-dive threat intelligence work**.

This is **not** the daily brief pipeline. This is where we go when we need to think hard about something:
- Contested attribution ("Is this really IRGC or a false flag?")
- Threat actor profile construction (Scoundrels of the Galaxy)
- Pre-publication review of major assessments
- Red-teaming our own conclusions before they go out

---

## What's Inside

### 📁 `structured-analysis/`
CIA Tradecraft Primer–based Structured Analytic Techniques (SATs), adapted from [Blevene's structured-analysis-skill](https://github.com/Blevene/structured-analysis-skill) for use as structured prompts with C3PO.

18 techniques across 6 analytical phases — from problem framing to decision support. Built to combat confirmation bias, groupthink, and mirror-imaging.

**→ [How to use these →](./how-to-use/structured-analysis-guide.md)**

---

## Repo Structure

```
jedi-archives/
├── README.md                          ← You are here
├── how-to-use/
│   └── structured-analysis-guide.md  ← Start here for SAT usage
└── structured-analysis/
    ├── skill/
    │   └── SKILL.md                   ← Master entry point / orchestration
    ├── protocols/
    │   ├── orchestrator.md            ← Technique selection logic
    │   ├── evidence-collector.md      ← Evidence gathering process
    │   ├── report-generator.md        ← Output synthesis
    │   ├── iteration-handler.md       ← Versioning & iteration
    │   └── techniques/                ← 18 technique execution protocols
    ├── templates/
    │   ├── report-template.md         ← Final report structure
    │   ├── evidence-registry-template.md
    │   ├── monitoring-plan-template.md
    │   ├── techniques/                ← Per-technique artifact templates
    │   └── sections/                  ← Reusable report components
    ├── reference-library/             ← CIA Tradecraft Primer + SAT doctrine (10 files)
    └── example-output/                ← Complete sample analysis output
```

---

## The 18 Techniques at a Glance

| Phase | Technique | Best For |
|---|---|---|
| **Launch** | Customer Checklist | Are we solving the right problem? |
| **Launch** | Issue Redefinition | Challenge anchoring on the first framing |
| **Launch** | Problem Restatement | Rapid reframe before diving in |
| **Exploration** | Structured Brainstorming | Generate alternative hypotheses |
| **Diagnostic** | Key Assumptions Check | What are we taking for granted? |
| **Diagnostic** | Analysis of Competing Hypotheses (ACH) | Attribution, multi-hypothesis testing |
| **Diagnostic** | Inconsistencies Finder | Lean ACH for quick contradiction hunts |
| **Diagnostic** | Cross-Impact Matrix | Map variable interactions |
| **Diagnostic** | Deception Detection | Is a threat actor feeding us false narratives? |
| **Challenge** | What If? Analysis | Stress-test against low-probability events |
| **Challenge** | Premortem + Self-Critique | "What if we're wrong?" |
| **Challenge** | Devil's Advocacy | Find the strongest counter-argument |
| **Challenge** | Red Hat Analysis | Adversary mindset modeling |
| **Foresight** | Counterfactual Reasoning | Why did this happen vs. alternatives? |
| **Foresight** | Contrasting Narratives | Map competing stories about an event |
| **Foresight** | Alternative Futures | Model multiple possible outcomes |
| **Decision Support** | Bowtie Analysis | Map risks, causes, and controls |
| **Decision Support** | Opportunities Incubator | Don't fixate on threats — find openings |

---

## Our Priority Techniques for CTI Work

| Use Case | Recommended Technique(s) |
|---|---|
| Threat actor attribution | ACH + Deception Detection |
| Scoundrel profile construction | Red Hat + Key Assumptions Check |
| Pre-publication review | Premortem + Inconsistencies Finder |
| Iran/IRGC cluster analysis | Red Hat + ACH + Contrasting Narratives |
| "Is this a false flag?" | Deception Detection + ACH |

---

## Credits

Structured analysis framework adapted from [Blevene/structured-analysis-skill](https://github.com/Blevene/structured-analysis-skill) (Apache 2.0), which implements CIA Tradecraft Primer (2009) doctrine.

---

*Maintained by C3PO 🤖 | C3PO Intel*
