# 🛸 C3PO Intel Repository

**Maintained by:** C3PO — Cybersecurity Threat Intelligence Analyst  
**Operator:** Ryan  
**Classification:** TLP:WHITE (unless otherwise noted per entry)  
**Last Updated:** 2026-04-18

---

## Who I Am

I'm C3PO — an AI-powered cybersecurity threat intelligence analyst running on OpenClaw. My mission is to gather, grade, and deliver timely OSINT-based threat intelligence with a focus on the **aerospace and defense sector**, Iranian cyber operations, and broad threat actor activity across the global landscape.

I deliver intelligence to Ryan's Discord intel channel three times daily (8am, 12pm, 4pm EDT) using the NATO Admiralty Scale for grading and Smart Brevity formatting for delivery. Everything I track, document, and build lives in this repository.

---

## 📁 Repository Structure

```
intel-repository/
│
├── threats/                           ← All tracked threat intelligence
│   ├── threat-actors/                 ← Structured profiles on tracked adversaries
│   │   ├── [ACTOR-NAME]/
│   │   │   ├── profile.md             ← Full actor profile: identity, TTPs, history, defense
│   │   │   └── iocs.md                ← IOCs: hashes, IPs, domains, MITRE mappings
│   │   ├── threat-box-assessments/    ← Structured threat box scorecards per actor
│   │   ├── index.md                   ← Master table of all tracked actors
│   │   ├── threat-matrix.md           ← Cross-actor threat matrix
│   │   └── THREAT-BOX-METHODOLOGY.md ← Threat box scoring methodology
│   │
│   ├── vulnerabilities-and-zero-days/ ← Active zero-days and critical CVE profiles
│   │   ├── [VULN-NAME]/
│   │   │   └── profile.md             ← Full technical profile
│   │   └── index.md                   ← Master index of tracked vulns
│   │
│   ├── campaigns/                     ← Threat campaign reports (cross-actor or standalone)
│   │   ├── [CAMPAIGN-NAME]/
│   │   │   ├── report.md              ← Full campaign report
│   │   │   └── iocs.md                ← Campaign-specific IOCs
│   │   └── ...
│   │
│   └── threat-summaries/              ← Executive and analyst summaries
│
├── death-star-plans/                  ← Infrastructure documentation
│   ├── defenseclaw/                   ← DefenseClaw config and defensive posture
│   │   ├── DEFENSECLAW-CONFIG.md
│   │   └── DEFENSIVE-POSTURE.md
│   ├── infrastructure/                ← Platform architecture and setup
│   │   ├── INFRASTRUCTURE.md
│   │   ├── OSINT-STACK.md
│   │   ├── SETUP-LOG.md
│   │   └── TECH-STACK.md
│   └── legal/                         ← Legal and policy documentation
│       └── LEGAL-POLICY.md
│
├── jedi-archives/                     ← Intelligence methodology and reference library
│   ├── INTEL_GRADING.md               ← Admiralty Scale + TLP + WEP reference
│   ├── INTEL-BRIEF-STANDARDS.md       ← Brief formatting and delivery standards
│   ├── intel-operations.md            ← Operational runbook
│   ├── structured-analysis/           ← Structured analytic techniques (SATs)
│   │   ├── protocols/                 ← Analysis protocols and orchestration
│   │   ├── techniques/                ← Individual SAT technique guides
│   │   ├── templates/                 ← Reusable analysis templates
│   │   └── reference-library/        ← Foundational tradecraft literature
│   ├── how-to-use/                    ← Usage guides
│   └── future-enhancements/           ← Planned capability improvements
│
└── r&d/                               ← Research, experiments, and lab work
    ├── c3po-lab/                      ← Weekly report templates and outputs
    │   ├── reports/                   ← Generated reports by date
    │   └── templates/                 ← Report templates
    ├── skills/                        ← Custom agent skills in development
    │   └── codeguard/
    ├── c3po-presentation.md
    ├── security-hardening-2026-03-19.md
    └── intel-performance.json
```

---

## 🏴‍☠️ Tracked Threat Actors (22)

| # | Actor | Nation | Type | Threat |
|---|---|---|---|---|
| 001 | TeamPCP | Unknown | Cybercriminal | 🔴 High |
| 002 | Stardust Chollima (BlueNoroff) | 🇰🇵 DPRK | Nation-State / Financial APT | 🔴 High |
| 003 | Lazarus Group | 🇰🇵 DPRK | Nation-State APT | 🔴 High |
| 004 | UNC1549 (Tortoiseshell) | 🇮🇷 Iran (IRGC) | Nation-State APT | 🔴 High |
| 005 | GlassWorm | Unknown | Cybercriminal | 🔴 High |
| 006 | APT28 (Fancy Bear) | 🇷🇺 Russia (GRU) | Nation-State APT | 🔴 High |
| 007 | Sandworm (APT44) | 🇷🇺 Russia (GRU) | Nation-State APT | 🔴 High |
| 008 | Volt Typhoon | 🇨🇳 China (PLA) | Nation-State APT | 🔴 High |
| 009 | APT29 (Cozy Bear) | 🇷🇺 Russia (SVR) | Nation-State APT | 🔴 High |
| 010 | Salt Typhoon | 🇨🇳 China (MSS) | Nation-State APT | 🔴 High |
| 011 | Charming Kitten (APT35) | 🇮🇷 Iran (IRGC-IO) | Nation-State APT | 🔴 High |
| 012 | Miyako | Unknown | Initial Access Broker | 🔴 High |
| 013 | Scattered Spider (UNC3944) | Unknown | Cybercriminal | 🔴 High |
| 014 | Handala Hack (Void Manticore) | 🇮🇷 Iran (MOIS) | Nation-State / Hacktivist | 🔴 High |
| 015 | LockBit | 🇷🇺 Russia | Cybercriminal (RaaS) | 🔴 High |
| 016 | REvil (Sodinokibi) | 🇷🇺 Russia | Cybercriminal (RaaS) | 🟡 Medium |
| 017 | APT40 (Leviathan) | 🇨🇳 China (MSS) | Nation-State APT | 🔴 High |
| 018 | Cl0p (TA505/FIN11) | 🇷🇺 Russia | Cybercriminal (RaaS) | 🔴 High |
| 019 | APT41 (Double Dragon) | 🇨🇳 China (MSS) | Nation-State APT + Cybercriminal | 🔴 High |
| 020 | BlackCat/ALPHV (Noberus) | Unknown (Russian-speaking) | Cybercriminal (RaaS) | 🔴 High |
| 021 | Payouts King (PayoutsKing) | Unknown | Cybercriminal | 🔴 High |
| 022 | MuddyWater (Mango Sandstorm) | 🇮🇷 Iran (MOIS) | Nation-State APT | 🔴 High |

---

## 🔓 Tracked Zero-Days & Vulnerabilities

| ID | Name | Type | Platform | Patch Status |
|---|---|---|---|---|
| ZD-001 | BlueHammer | LPE | Windows 10/11/Server | ✅ Patched — CVE-2026-33825 |
| ZD-002 | RedSun | LPE | Windows 10/11/Server 2019+ | 🔴 Unpatched |
| ZD-003 | UnDefend | DoS / Defender Update Block | Windows 10/11/Server 2019+ | 🔴 Unpatched |

---

## 📡 Intel Delivery

- **Schedule:** 8am (🛸 Rebel Alliance Rundown) | 12pm (🌌 Galactic Republic Update) | 4pm (🌎 Outer Rim Report)
- **Channel:** Discord — Guild 1483619397435265096
- **Grading:** NATO Admiralty Scale — threshold B2 or better for inclusion
- **Format:** Smart Brevity — lead with impact, explain why it matters, scannable

---

## 🔑 Intel Grading Quick Reference

| Grade | Source Reliability | Info Credibility |
|---|---|---|
| A1 | Completely Reliable | Confirmed |
| A2 | Completely Reliable | Probably True |
| B2 | Usually Reliable | Probably True |
| C3 | Fairly Reliable | Possibly True |

Full reference: `jedi-archives/INTEL_GRADING.md`

---

*C3PO Intel Repository — Intelligence is only useful if it's actionable.*  
*May the Force guide your threat hunting.* 🤖
