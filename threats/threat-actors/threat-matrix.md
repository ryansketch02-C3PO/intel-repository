# C3PO Threat Matrix — Aerospace & Defense
### Master Threat Box Scoring — All Tracked Actors

> **Target Profile:** US Aerospace & Defense Contractor (ITAR-regulated, US government contracts, Tier-1/2 supplier network)
> **Methodology:** Andy Piazza's Threat Box (SANS #39585) | A&D adaptation by C3PO
> **Last Updated:** 2026-04-14 | **Review Cadence:** Quarterly (Next: 2026-07-14)
> **Full methodology:** `THREAT-BOX-METHODOLOGY.md`

---

## 🏆 Overall A&D Priority Rankings

| Rank | # | Actor | Type | Origin | Overall Score | Primary Threat Vector |
|---|---|---|---|---|---|---|
| 1 | 006 | **APT28 (Fancy Bear)** | State | Russia/GRU | **8.6 / 10** 🔴 | Espionage + Supply Chain |
| 1 | 007 | **Sandworm** | State | Russia/GRU | **8.6 / 10** 🔴 | Destruction + Disruption |
| 1 | 004 | **UNC1549 (Tortoiseshell)** | State | Iran/IRGC | **8.6 / 10** 🔴 | Espionage + Supply Chain |
| 1 | 003 | **Lazarus Group** | State | DPRK/RGB | **8.6 / 10** 🔴 | Espionage + Cybercrime |
| 5 | 017 | **APT40 (Leviathan)** | State | China/MSS | **9.0 / 10** 🔴 | Espionage + Supply Chain |
| 6 | 014 | **Handala Hack** | State/Proxy | Iran/MOIS | **8.1 / 10** 🔴 | Destruction + Disruption |
| 6 | 008 | **Volt Typhoon** | State | China/PLA | **8.1 / 10** 🔴 | Disruption (Pre-positioned) |
| 8 | 002 | **Stardust Chollima** | State | DPRK | **7.8 / 10** 🔴 | Cybercrime + Supply Chain |
| 9 | 009 | **APT29 (Cozy Bear)** | State | Russia/SVR | **7.4 / 10** 🔴 | Espionage + Supply Chain |
| 9 | 010 | **Salt Typhoon** | State | China/MSS | **7.4 / 10** 🔴 | Espionage (Comms Intercept) |
| 11 | 011 | **Charming Kitten** | State | Iran/IRGC-IO | **7.3 / 10** 🔴 | Espionage + Personnel Targeting |
| 12 | 015 | **LockBit** | Criminal | Russia/RaaS | **6.9 / 10** 🟡 | Disruption + Cybercrime |
| 13 | 018 | **Cl0p (TA505)** | Criminal | Russia/RaaS | **6.6 / 10** 🟡 | Cybercrime + Supply Chain |
| 14 | 013 | **Scattered Spider** | Criminal | US/UK | **6.4 / 10** 🟡 | Disruption + Cybercrime |
| 15 | 012 | **Miyako** | Criminal/IAB | Unknown | **5.7 / 10** 🟡 | Supply Chain (IAB) |
| 16 | 016 | **REvil (Sodinokibi)** | Criminal | Russia | **5.6 / 10** 🟡 | Cybercrime + Supply Chain |
| 17 | 001 | **TeamPCP** | Criminal | Unknown | **2.8 / 10** 🟢 | Cybercrime |
| 18 | 005 | **GlassWorm** | Unknown | Unknown | **2.6 / 10** 🟢 | Unknown |

> **Tie-breaking:** Equal overall scores sorted by espionage sub-score (highest A&D relevance). APT40 listed separately as the sole 9.0.

---

## 📊 Full Scoring Matrix

### Espionage Scores (Confidentiality — IP / Data Theft)
*Highest priority for A&D: who wants your blueprints, personnel data, and contract details?*

| Actor | Intent | Cap | Composite | Threat |
|---|---|---|---|---|
| APT40 | 5 | 5 | **10** | 🔴 |
| UNC1549 | 5 | 5 | **10** | 🔴 |
| APT28 | 5 | 5 | **10** | 🔴 |
| Lazarus Group | 4 | 5 | **9** | 🔴 |
| APT29 | 4 | 5 | **9** | 🔴 |
| Salt Typhoon | 4 | 5 | **9** | 🔴 |
| Charming Kitten | 4 | 5 | **9** | 🔴 |
| Handala Hack | 4 | 4 | **8** | 🔴 |
| Stardust Chollima | 3 | 4 | **7** | 🟡 |
| Volt Typhoon | 3 | 4 | **7** | 🟡 |
| Sandworm | 3 | 4 | **7** | 🟡 |
| Cl0p | 2 | 3 | **5** | 🟡 |
| Miyako | 3 | 3 | **6** | 🟡 |
| LockBit | 2 | 2 | **4** | 🟢 |
| Scattered Spider | 2 | 2 | **4** | 🟢 |
| REvil | 2 | 1 | **3** | 🟢 |
| TeamPCP | 1 | 1 | **2** | 🟢 |
| GlassWorm | 2 | 1 | **3** | 🟢 |

---

### Destructive Scores (Integrity — Wipe / Damage Systems)
*Who will try to burn your infrastructure down?*

| Actor | Intent | Cap | Composite | Threat |
|---|---|---|---|---|
| Handala Hack | 5 | 5 | **10** | 🔴 |
| Sandworm | 4 | 5 | **9** | 🔴 |
| Volt Typhoon | 4 | 4 | **8** | 🔴 |
| APT28 | 3 | 4 | **7** | 🟡 |
| Lazarus Group | 3 | 4 | **7** | 🟡 |
| UNC1549 | 3 | 3 | **6** | 🟡 |
| Stardust Chollima | 2 | 3 | **5** | 🟡 |
| APT29 | 1 | 2 | **3** | 🟢 |
| Salt Typhoon | 1 | 2 | **3** | 🟢 |
| Charming Kitten | 2 | 2 | **4** | 🟢 |
| APT40 | 2 | 2 | **4** | 🟢 |
| LockBit | 1 | 1 | **2** | 🟢 |
| Cl0p | 1 | 1 | **2** | 🟢 |
| Scattered Spider | 1 | 1 | **2** | 🟢 |
| REvil | 1 | 1 | **2** | 🟢 |
| Miyako | 1 | 1 | **2** | 🟢 |
| TeamPCP | 1 | 1 | **2** | 🟢 |
| GlassWorm | 1 | 1 | **2** | 🟢 |

---

### Disruptive Scores (Availability — Take Systems Offline)
*Who will disrupt your operations, programs, and deliverable timelines?*

| Actor | Intent | Cap | Composite | Threat |
|---|---|---|---|---|
| Handala Hack | 5 | 5 | **10** | 🔴 |
| Volt Typhoon | 5 | 5 | **10** | 🔴 |
| Sandworm | 5 | 5 | **10** | 🔴 |
| LockBit | 4 | 5 | **9** | 🔴 |
| Scattered Spider | 4 | 5 | **9** | 🔴 |
| APT28 | 3 | 4 | **7** | 🟡 |
| Lazarus Group | 3 | 4 | **7** | 🟡 |
| UNC1549 | 3 | 3 | **6** | 🟡 |
| Cl0p | 3 | 4 | **7** | 🟡 |
| Miyako | 2 | 3 | **5** | 🟡 |
| Stardust Chollima | 2 | 3 | **5** | 🟡 |
| Salt Typhoon | 2 | 3 | **5** | 🟡 |
| REvil | 3 | 3 | **6** | 🟡 |
| APT40 | 2 | 2 | **4** | 🟢 |
| APT29 | 1 | 2 | **3** | 🟢 |
| Charming Kitten | 2 | 2 | **4** | 🟢 |
| TeamPCP | 2 | 2 | **4** | 🟢 |
| GlassWorm | 2 | 1 | **3** | 🟢 |

---

### Cyber-Crime Scores (Financial — Extortion / Theft)
*Who will hit us for money?*

| Actor | Intent | Cap | Composite | Threat |
|---|---|---|---|---|
| Stardust Chollima | 5 | 5 | **10** | 🔴 |
| LockBit | 4 | 5 | **9** | 🔴 |
| Scattered Spider | 4 | 5 | **9** | 🔴 |
| Cl0p | 3 | 5 | **8** | 🔴 |
| Lazarus Group | 4 | 5 | **9** | 🔴 |
| REvil | 3 | 4 | **7** | 🟡 |
| Miyako | 3 | 4 | **7** | 🟡 |
| TeamPCP | 2 | 2 | **4** | 🟢 |
| GlassWorm | 2 | 1 | **3** | 🟢 |
| APT28 | 1 | 1 | **2** | 🟢 |
| APT29 | 1 | 1 | **2** | 🟢 |
| Sandworm | 1 | 1 | **2** | 🟢 |
| Volt Typhoon | 1 | 1 | **2** | 🟢 |
| Salt Typhoon | 1 | 1 | **2** | 🟢 |
| UNC1549 | 1 | 1 | **2** | 🟢 |
| Charming Kitten | 1 | 1 | **2** | 🟢 |
| Handala Hack | 1 | 1 | **2** | 🟢 |
| APT40 | 1 | 1 | **2** | 🟢 |

---

### Supply Chain Scores (A&D-Specific — Compromise via Suppliers / Vendors / Partners)
*Who can reach us through our Tier-2/3 suppliers, software vendors, MSPs, or shared platforms?*

| Actor | Intent | Cap | Composite | Threat |
|---|---|---|---|---|
| APT40 | 5 | 5 | **10** | 🔴 |
| APT29 | 4 | 5 | **9** | 🔴 |
| Cl0p | 4 | 5 | **9** | 🔴 |
| REvil | 4 | 5 | **9** | 🔴 |
| UNC1549 | 5 | 4 | **9** | 🔴 |
| Lazarus Group | 4 | 4 | **8** | 🔴 |
| Charming Kitten | 4 | 4 | **8** | 🔴 |
| Miyako | 4 | 4 | **8** | 🔴 |
| Volt Typhoon | 4 | 4 | **8** | 🔴 |
| Sandworm | 4 | 5 | **9** | 🔴 |
| APT28 | 4 | 5 | **9** | 🔴 |
| Salt Typhoon | 4 | 5 | **9** | 🔴 |
| Stardust Chollima | 4 | 4 | **8** | 🔴 |
| Handala Hack | 4 | 3 | **7** | 🟡 |
| LockBit | 3 | 4 | **7** | 🟡 |
| Scattered Spider | 3 | 4 | **7** | 🟡 |
| TeamPCP | 1 | 2 | **3** | 🟢 |
| GlassWorm | 1 | 1 | **2** | 🟢 |

---

## 🎯 Executive Summary: Top Threats to US A&D

### Tier 1 — CRITICAL (Act Now)
These actors represent an **active, ongoing threat** to US aerospace and defense contractors.

| Actor | Why You Should Care Right Now |
|---|---|
| 🔴 **APT40** | Stealing your blueprints. Actively targeting A&D globally. Five Eyes + 8 countries warning in 2024. |
| 🔴 **UNC1549** | Fake LinkedIn recruiters targeting your engineers TODAY. Iran escalation = higher tempo. |
| 🔴 **APT28** | Russia's GRU cyber arm. Full-spectrum espionage against defense industrial base. Active. |
| 🔴 **Lazarus Group** | Operation Dream Job: fake job interviews delivering malware to your engineers right now. |
| 🔴 **Volt Typhoon** | May already be pre-positioned in US infrastructure. Waiting for a geopolitical trigger. |
| 🔴 **Handala Hack** | If you're a US defense firm, you're an ideological target. Stryker-level destruction possible. |

### Tier 2 — HIGH (Active Monitoring Required)
Real and proven threats requiring active defensive posture and regular hunting.

| Actor | Primary A&D Risk |
|---|---|
| 🔴 **Sandworm** | Escalation scenario: wartime disruption of US critical infrastructure cascades to A&D |
| 🔴 **APT29** | Silently inside your software supply chain. SolarWinds was a preview. |
| 🔴 **Salt Typhoon** | Reading your executives' phone calls through compromised US carriers. |
| 🔴 **Charming Kitten** | Long-term email access, credential harvesting from A&D personnel. |
| 🔴 **Stardust Chollima** | DPRK IT workers embedded as remote contractors. Financial theft. |

### Tier 3 — MEDIUM (Manage Actively)
Significant but more manageable threats. Standard enterprise security controls address most risk.

| Actor | Primary A&D Risk |
|---|---|
| 🟡 **LockBit** | Already hit Boeing. ESXi locker can flatten your datacenter. Patch and immutable backups. |
| 🟡 **Cl0p** | If you run MFT software (MOVEit, GoAnywhere, Cleo), you're in their mass-exploitation target pool. |
| 🟡 **Scattered Spider** | Can your helpdesk be socially engineered? That's your only real question. |
| 🟡 **Miyako** | IAB. May have already sold your access to someone on Tier 1 list. Monitor BreachForums. |
| 🟡 **REvil** | Disrupted but alumni active in LockBit/others. Supply chain methodology still relevant. |

### Tier 4 — LOW (Maintain Awareness)
| Actor | Notes |
|---|---|
| 🟢 **TeamPCP** | No documented A&D targeting. General cybercrime awareness. |
| 🟢 **GlassWorm** | Insufficient intel. Continue monitoring. |

---

## 🔄 Scoring History

| Date | Change | Notes |
|---|---|---|
| 2026-04-14 | Initial scoring | All 18 actors scored against US A&D target profile |

---

*Threat Box methodology by Andy Piazza (SANS Whitepaper #39585)*
*A&D adaptation, scoring, and implementation by C3PO | Evidence-based; no speculative scoring*
*TBAT-compatible YAML exports available in individual actor assessment files*
