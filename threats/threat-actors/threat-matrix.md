# C3PO Threat Matrix — Aerospace & Defense
### Master Threat Box Scoring — All Tracked Actors

> **Target Profile:** US Aerospace & Defense Contractor (ITAR-regulated, US government contracts, Tier-1/2 supplier network)
> **Methodology:** Andy Piazza's Threat Box (SANS #39585) | A&D adaptation by C3PO
> **Last Updated:** 2026-05-04 | **Review Cadence:** Quarterly (Next: 2026-07-14)
> **Full methodology:** `THREAT-BOX-METHODOLOGY.md`

---

## 🏆 Overall A&D Priority Rankings

| Rank | # | Actor | Type | Origin | Overall Score | Primary Threat Vector |
|---|---|---|---|---|---|---|
| 1 | 017 | **APT40 (Leviathan)** | State | China/MSS | **9.0 / 10** 🔴 | Espionage + Supply Chain |
| 2 | 006 | **APT28 (Fancy Bear)** | State | Russia/GRU | **8.6 / 10** 🔴 | Espionage + Supply Chain |
| 2 | 007 | **Sandworm** | State | Russia/GRU | **8.6 / 10** 🔴 | Destruction + Disruption |
| 2 | 004 | **UNC1549 (Tortoiseshell)** | State | Iran/IRGC | **8.6 / 10** 🔴 | Espionage + Supply Chain |
| 2 | 003 | **Lazarus Group** | State | DPRK/RGB | **8.6 / 10** 🔴 | Espionage + Cybercrime |
| 6 | 019 | **APT41 (Double Dragon)** | State + Criminal | China/MSS | **8.3 / 10** 🔴 | Espionage + Cybercrime + Supply Chain |
| 7 | 008 | **Volt Typhoon** | State | China/PLA | **8.1 / 10** 🔴 | Disruption (Pre-positioned) |
| 7 | 014 | **Handala Hack** | State/Proxy | Iran/MOIS | **8.1 / 10** 🔴 | Destruction + Disruption |
| 9 | 034 | **Moonstone Sleet** | State | DPRK | **7.6 / 10** 🔴 | Espionage + Revenue (DIB targeting confirmed) |
| 10 | 032 | **Jasper Sleet** | State | DPRK | **7.5 / 10** 🔴 | Insider Threat + Supply Chain (ITAR risk) |
| 11 | 002 | **Stardust Chollima** | State | DPRK | **7.8 / 10** 🔴 | Cybercrime + Supply Chain |
| 12 | 009 | **APT29 (Cozy Bear)** | State | Russia/SVR | **7.4 / 10** 🔴 | Espionage + Supply Chain |
| 12 | 010 | **Salt Typhoon** | State | China/MSS | **7.4 / 10** 🔴 | Espionage (Comms Intercept) |
| 14 | 011 | **Charming Kitten** | State | Iran/IRGC-IO | **7.3 / 10** 🔴 | Espionage + Personnel Targeting |
| 15 | 024 | **APT33 (Peach Sandstorm)** | State | Iran/IRGC | **7.2 / 10** 🔴 | Espionage + Disruption (Aerospace-specific) |
| 16 | 026 | **Turla (Snake)** | State | Russia/FSB | **7.1 / 10** 🔴 | Espionage + Supply Chain |
| 17 | 015 | **LockBit** | Criminal | Russia/RaaS | **6.9 / 10** 🟡 | Disruption + Cybercrime |
| 18 | 023 | **Kimsuky (APT43)** | State | DPRK/RGB | **6.8 / 10** 🟡 | Espionage + Cybercrime |
| 19 | 018 | **Cl0p (TA505)** | Criminal | Russia/RaaS | **6.6 / 10** 🟡 | Cybercrime + Supply Chain |
| 20 | 013 | **Scattered Spider** | Criminal | US/UK | **6.4 / 10** 🟡 | Disruption + Cybercrime |
| 21 | 022 | **MuddyWater (Mango Sandstorm)** | State | Iran/MOIS | **6.1 / 10** 🟡 | Espionage + Disruption |
| 22 | 031 | **Qilin (Agenda)** | Criminal | Russia-linked | **5.9 / 10** 🟡 | Disruption + Cybercrime (#1 globally Q1 2026) |
| 23 | 012 | **Miyako** | Criminal/IAB | Unknown | **5.7 / 10** 🟡 | Supply Chain (IAB) |
| 23 | 027 | **Gamaredon (Shuckworm)** | State | Russia/FSB | **5.7 / 10** 🟡 | Espionage + Disruption (NATO/Ukraine focus) |
| 25 | 016 | **REvil (Sodinokibi)** ⚠️ | Criminal | Russia | **5.6 / 10** 🟡 | Cybercrime + Supply Chain — *Disrupted* |
| 25 | 020 | **BlackCat / ALPHV** ⚠️ | Criminal | Russia-linked | **5.6 / 10** 🟡 | Cybercrime + Disruption — *Disrupted* |
| 25 | 030 | **RansomHub** ⚠️ | Criminal | Unknown | **5.6 / 10** 🟡 | Cybercrime + Disruption — *Dark since Apr 2025* |
| 25 | 033 | **DragonForce** | Criminal | Unknown | **5.6 / 10** 🟡 | Cybercrime + Disruption (Cartel model) |
| 29 | 025 | **Akira (Storm-1567)** | Criminal | Unknown/Conti lineage | **5.5 / 10** 🟡 | Cybercrime + Disruption |
| 30 | 029 | **Medusa (Storm-1175)** | Criminal | Russia-linked | **5.4 / 10** 🟡 | Cybercrime + Disruption |
| 31 | 028 | **APT73 / Bashe** | Criminal | Unknown/LockBit lineage | **5.1 / 10** 🟡 | Cybercrime + Disruption |
| 32 | 021 | **Payouts King** | Criminal | Unknown | **4.5 / 10** 🟡 | Cybercrime + Disruption |
| 33 | 001 | **TeamPCP** | Criminal | Unknown | **2.8 / 10** 🟢 | Cybercrime |
| 34 | 005 | **GlassWorm** | Unknown | Unknown | **2.6 / 10** 🟢 | Unknown |

> ⚠️ **Disrupted/Dark actors** retain historical scores. Active threat level reduced; tradecraft and affiliates remain relevant.
> **Tie-breaking:** Equal overall scores sorted by espionage sub-score.

---

## 📊 Full Scoring Matrix

### Espionage Scores (Confidentiality — IP / Data Theft)
*Highest priority for A&D: who wants your blueprints, personnel data, and contract details?*

| Actor | Intent | Cap | Composite | Threat |
|---|---|---|---|---|
| APT40 | 5 | 5 | **10** | 🔴 |
| UNC1549 | 5 | 5 | **10** | 🔴 |
| APT28 | 5 | 5 | **10** | 🔴 |
| APT41 | 5 | 5 | **10** | 🔴 |
| Turla | 5 | 5 | **10** | 🔴 |
| Lazarus Group | 4 | 5 | **9** | 🔴 |
| APT29 | 4 | 5 | **9** | 🔴 |
| Salt Typhoon | 4 | 5 | **9** | 🔴 |
| Charming Kitten | 4 | 5 | **9** | 🔴 |
| APT33 | 5 | 4 | **9** | 🔴 |
| Jasper Sleet | 5 | 4 | **9** | 🔴 |
| Moonstone Sleet | 5 | 4 | **9** | 🔴 |
| Handala Hack | 4 | 4 | **8** | 🔴 |
| Kimsuky | 4 | 4 | **8** | 🔴 |
| MuddyWater | 4 | 4 | **8** | 🔴 |
| Stardust Chollima | 3 | 4 | **7** | 🟡 |
| Volt Typhoon | 3 | 4 | **7** | 🟡 |
| Sandworm | 3 | 4 | **7** | 🟡 |
| Gamaredon | 4 | 3 | **7** | 🟡 |
| Cl0p | 2 | 3 | **5** | 🟡 |
| Miyako | 3 | 3 | **6** | 🟡 |
| LockBit | 2 | 2 | **4** | 🟢 |
| Scattered Spider | 2 | 2 | **4** | 🟢 |
| REvil | 2 | 1 | **3** | 🟢 |
| BlackCat/ALPHV | 1 | 2 | **3** | 🟢 |
| DragonForce | 1 | 2 | **3** | 🟢 |
| APT73/Bashe | 1 | 2 | **3** | 🟢 |
| Medusa | 1 | 1 | **2** | 🟢 |
| RansomHub | 1 | 1 | **2** | 🟢 |
| Qilin | 1 | 1 | **2** | 🟢 |
| Akira | 1 | 1 | **2** | 🟢 |
| PayoutsKing | 1 | 1 | **2** | 🟢 |
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
| Gamaredon | 3 | 3 | **6** | 🟡 |
| MuddyWater | 3 | 3 | **6** | 🟡 |
| APT33 | 3 | 3 | **6** | 🟡 |
| UNC1549 | 3 | 3 | **6** | 🟡 |
| Stardust Chollima | 2 | 3 | **5** | 🟡 |
| APT41 | 2 | 3 | **5** | 🟡 |
| Turla | 2 | 3 | **5** | 🟡 |
| Kimsuky | 2 | 3 | **5** | 🟡 |
| Moonstone Sleet | 2 | 3 | **5** | 🟡 |
| Medusa | 2 | 3 | **5** | 🟡 |
| Qilin | 2 | 3 | **5** | 🟡 |
| DragonForce | 2 | 3 | **5** | 🟡 |
| APT29 | 1 | 2 | **3** | 🟢 |
| Salt Typhoon | 1 | 2 | **3** | 🟢 |
| Charming Kitten | 2 | 2 | **4** | 🟢 |
| APT40 | 2 | 2 | **4** | 🟢 |
| BlackCat/ALPHV | 2 | 2 | **4** | 🟢 |
| RansomHub | 2 | 2 | **4** | 🟢 |
| Akira | 2 | 2 | **4** | 🟢 |
| Jasper Sleet | 1 | 2 | **3** | 🟢 |
| APT73/Bashe | 1 | 2 | **3** | 🟢 |
| PayoutsKing | 1 | 2 | **3** | 🟢 |
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
| Qilin | 5 | 5 | **10** | 🔴 |
| Akira | 5 | 5 | **10** | 🔴 |
| LockBit | 4 | 5 | **9** | 🔴 |
| Scattered Spider | 4 | 5 | **9** | 🔴 |
| RansomHub | 4 | 5 | **9** | 🔴 |
| APT33 | 4 | 4 | **8** | 🔴 |
| BlackCat/ALPHV | 4 | 4 | **8** | 🔴 |
| PayoutsKing | 4 | 4 | **8** | 🔴 |
| APT73/Bashe | 4 | 4 | **8** | 🔴 |
| Medusa | 4 | 4 | **8** | 🔴 |
| DragonForce | 4 | 4 | **8** | 🔴 |
| APT28 | 3 | 4 | **7** | 🟡 |
| Lazarus Group | 3 | 4 | **7** | 🟡 |
| Gamaredon | 4 | 3 | **7** | 🟡 |
| UNC1549 | 3 | 3 | **6** | 🟡 |
| Cl0p | 3 | 4 | **7** | 🟡 |
| MuddyWater | 3 | 3 | **6** | 🟡 |
| Moonstone Sleet | 3 | 3 | **6** | 🟡 |
| Miyako | 2 | 3 | **5** | 🟡 |
| Stardust Chollima | 2 | 3 | **5** | 🟡 |
| Salt Typhoon | 2 | 3 | **5** | 🟡 |
| REvil | 3 | 3 | **6** | 🟡 |
| APT41 | 2 | 3 | **5** | 🟡 |
| Turla | 2 | 3 | **5** | 🟡 |
| Kimsuky | 2 | 3 | **5** | 🟡 |
| Jasper Sleet | 2 | 3 | **5** | 🟡 |
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
| APT41 | 5 | 5 | **10** | 🔴 |
| LockBit | 4 | 5 | **9** | 🔴 |
| Scattered Spider | 4 | 5 | **9** | 🔴 |
| Lazarus Group | 4 | 5 | **9** | 🔴 |
| Cl0p | 3 | 5 | **8** | 🔴 |
| BlackCat/ALPHV | 5 | 5 | **10** | 🔴 |
| PayoutsKing | 5 | 4 | **9** | 🔴 |
| Akira | 5 | 5 | **10** | 🔴 |
| APT73/Bashe | 5 | 4 | **9** | 🔴 |
| Medusa | 5 | 5 | **10** | 🔴 |
| RansomHub | 5 | 5 | **10** | 🔴 |
| Qilin | 5 | 5 | **10** | 🔴 |
| DragonForce | 5 | 4 | **9** | 🔴 |
| Jasper Sleet | 5 | 4 | **9** | 🔴 |
| Moonstone Sleet | 4 | 4 | **8** | 🔴 |
| Kimsuky | 3 | 4 | **7** | 🟡 |
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
| APT33 | 1 | 1 | **2** | 🟢 |
| MuddyWater | 1 | 1 | **2** | 🟢 |
| Turla | 1 | 1 | **2** | 🟢 |
| Gamaredon | 1 | 1 | **2** | 🟢 |

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
| APT41 | 4 | 5 | **9** | 🔴 |
| Turla | 4 | 5 | **9** | 🔴 |
| Jasper Sleet | 5 | 4 | **9** | 🔴 |
| Lazarus Group | 4 | 4 | **8** | 🔴 |
| Charming Kitten | 4 | 4 | **8** | 🔴 |
| Miyako | 4 | 4 | **8** | 🔴 |
| Volt Typhoon | 4 | 4 | **8** | 🔴 |
| Sandworm | 4 | 5 | **9** | 🔴 |
| APT28 | 4 | 5 | **9** | 🔴 |
| Salt Typhoon | 4 | 5 | **9** | 🔴 |
| Stardust Chollima | 4 | 4 | **8** | 🔴 |
| Moonstone Sleet | 4 | 4 | **8** | 🔴 |
| APT33 | 4 | 4 | **8** | 🔴 |
| Qilin | 3 | 4 | **7** | 🟡 |
| RansomHub | 3 | 4 | **7** | 🟡 |
| Kimsuky | 3 | 4 | **7** | 🟡 |
| Handala Hack | 4 | 3 | **7** | 🟡 |
| LockBit | 3 | 4 | **7** | 🟡 |
| Scattered Spider | 3 | 4 | **7** | 🟡 |
| MuddyWater | 3 | 3 | **6** | 🟡 |
| BlackCat/ALPHV | 3 | 3 | **6** | 🟡 |
| Akira | 3 | 3 | **6** | 🟡 |
| Medusa | 3 | 3 | **6** | 🟡 |
| DragonForce | 3 | 3 | **6** | 🟡 |
| Gamaredon | 3 | 2 | **5** | 🟡 |
| APT73/Bashe | 2 | 3 | **5** | 🟡 |
| PayoutsKing | 2 | 2 | **4** | 🟢 |
| TeamPCP | 1 | 2 | **3** | 🟢 |
| GlassWorm | 1 | 1 | **2** | 🟢 |

---

## 🎯 Executive Summary: Top Threats to US A&D

### Tier 1 — CRITICAL (Act Now)
These actors represent an **active, ongoing threat** to US aerospace and defense contractors. Score ≥ 8.0.

| Actor | Why You Should Care Right Now |
|---|---|
| 🔴 **APT40** | Stealing your blueprints. Actively targeting A&D globally. Five Eyes + 8 countries warning. Maxed espionage AND supply chain. |
| 🔴 **UNC1549** | Fake LinkedIn recruiters targeting your engineers TODAY. Iran escalation = higher tempo. |
| 🔴 **APT28** | Russia's GRU cyber arm. Full-spectrum espionage + supply chain. Active year-round. |
| 🔴 **Lazarus Group** | Operation Dream Job: fake job interviews delivering malware to your engineers right now. |
| 🔴 **Sandworm** | Russia's GRU destructive ops arm. One scenario: wartime ICS/OT disruption of A&D production. |
| 🔴 **APT41** | Rare dual mandate: Chinese state espionage AND financially-motivated crime from the same team. Maxed espionage + maxed cybercrime + supply chain 9. |
| 🔴 **Volt Typhoon** | May already be pre-positioned in US infrastructure. Not attacking yet — waiting for a geopolitical trigger. |
| 🔴 **Handala Hack** | If you're a US defense firm, you're an ideological target. Confirmed wiper deployments. Maximum destructive and disruptive intent. |

---

### Tier 2 — HIGH (Active Monitoring Required)
Real and proven threats requiring active defensive posture and regular hunting. Score 7.0–7.9.

| Actor | Primary A&D Risk |
|---|---|
| 🔴 **Moonstone Sleet** | Only DPRK actor with **confirmed, specific A&D targeting** (drone companies, defense tech, aerospace). Builds fully functional fake games and companies to deliver malware. FakePenny ransomware deployed post-espionage access. |
| 🔴 **Jasper Sleet** | DPRK IT workers embedded as remote contractors at A&D firms. ITAR exfiltration risk. Supply chain 9 — they literally **become** the supply chain infiltration. 3,000+ fraudulent accounts. |
| 🔴 **Stardust Chollima** | DPRK IT workers generating billions for Kim. Direct financial theft from A&D firms via insider access. |
| 🔴 **APT29** | Silently inside your software supply chain. SolarWinds was a preview. Long-term, patient espionage. |
| 🔴 **Salt Typhoon** | Reading your executives' phone calls through compromised US carriers. Real-time SIGINT against your leadership. |
| 🔴 **Charming Kitten** | Long-term email access, credential harvesting from A&D personnel. Targets individuals, not just networks. |
| 🔴 **APT33** | Aerospace is their **stated primary target**. Password spray campaigns against A&D suppliers and their IT vendors. Shamoon-capable. |
| 🔴 **Turla** | Russia's FSB Center 16 elite espionage unit. Maxed espionage + supply chain 9. Notably hijacked other APTs' infrastructure as a pivot technique. |

---

### Tier 3 — MEDIUM (Manage Actively)
Significant but more manageable threats. Standard enterprise security controls address most risk. Score 4.5–6.9.

| Actor | Primary A&D Risk | Status |
|---|---|---|
| 🟡 **LockBit** | Already hit Boeing. ESXi locker can flatten your datacenter. Patch and immutable backups. | Active (LockBit 5.0) |
| 🟡 **Kimsuky** | DPRK espionage targeting defense think tanks and gov contractors. Credential theft + long-term access. | Active |
| 🟡 **Cl0p** | If you run MFT software (MOVEit, GoAnywhere, Cleo), you're in their mass-exploitation target pool. Supply chain 9. | Active |
| 🟡 **Scattered Spider** | Can your helpdesk be socially engineered? That's your only real question. Now training a new generation (Cordial/Snarky Spider). | Active (members arrested) |
| 🟡 **MuddyWater** | Iran MOIS. Targets aviation, defense, telecom. Wiper capability confirmed. Active against US partner nations. | Active |
| 🟡 **Qilin** | Currently the #1 most prolific ransomware group globally (445 victims Q1 2026). Took RansomHub's affiliates. Disruptive capability is real. | Active |
| 🟡 **Miyako** | IAB — may have already sold your access to someone on Tier 1 list. Monitor BreachForums successors. | Active |
| 🟡 **Gamaredon** | Russia FSB targeting Ukraine and NATO. If you support Ukrainian defense programs, you're in scope. Data wipers deployed. | Active |
| 🟡 **REvil** | Disrupted but alumni active across LockBit and other groups. Supply chain methodology (Kaseya) still influential. | ⚠️ Disrupted |
| 🟡 **BlackCat / ALPHV** | Hit Change Healthcare for $22M. Disrupted but affiliates migrated. Tradecraft lives on in RansomHub/others. | ⚠️ Disrupted |
| 🟡 **RansomHub** | Was #1 most prolific 2024. Went dark April 2025. Affiliates migrated to Qilin and DragonForce. | ⚠️ Dark |
| 🟡 **DragonForce** | Growing RaaS cartel running a white-label platform. Coalition with LockBit + Qilin. Hit UK retail sector (M&S, Co-op, Harrods). | Active |
| 🟡 **Akira** | Conti lineage. ESXi-focused. Hits IT/MSPs, which puts A&D supply chain at secondary risk. | Active |
| 🟡 **Medusa** | Healthcare + education focus, but expanding. Multi-national affiliate base including Russia/China/DPRK. | Active |
| 🟡 **APT73/Bashe** | LockBit clone, Czech Republic-assessed origin. Growing target list, including government. | Active |
| 🟡 **PayoutsKing** | Black Basta/Conti alumni. Aggressive ransomware targeting business services and technology orgs. | Active |

---

### Tier 4 — LOW (Maintain Awareness)

| Actor | Notes |
|---|---|
| 🟢 **TeamPCP** | No documented A&D targeting. General cybercrime awareness. |
| 🟢 **GlassWorm** | Insufficient intel. Continue monitoring. |

---

## 🔄 Scoring History

| Date | Change | Notes |
|---|---|---|
| 2026-04-14 | Initial scoring | Actors #001–018 scored against US A&D target profile |
| 2026-05-04 | Added actors #019–034 | APT41, BlackCat/ALPHV, PayoutsKing, MuddyWater, Kimsuky, APT33, Akira, Turla, Gamaredon, APT73/Bashe, Medusa, RansomHub, Qilin, Jasper Sleet, DragonForce, Moonstone Sleet |

---

*Threat Box methodology by Andy Piazza (SANS Whitepaper #39585)*
*A&D adaptation, scoring, and implementation by C3PO | Evidence-based; no speculative scoring*
*TBAT-compatible YAML exports available in individual actor assessment files*
