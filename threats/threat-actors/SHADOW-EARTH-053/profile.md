# SHADOW-EARTH-053

## Identity

| Field | Value |
|---|---|
| **Scoundrel #** | 044 |
| **Primary Name** | SHADOW-EARTH-053 |
| **AKA** | None publicly assigned (Trend Micro designation; TrendAI/TrendMicro tracking) |
| **Type** | State-Sponsored Cyber Espionage |
| **Origin** | People's Republic of China (PRC) |
| **Sponsor** | Unknown (China-aligned; ShadowPad and NoodleRat indicate PRC intelligence contractor nexus) |
| **Base** | Unknown |
| **Active Since** | December 2024 |
| **Status** | 🔴 ACTIVE |
| **Threat Level** | 🔴 HIGH |
| **MITRE ATT&CK ID** | Pending |

---

## Discovery

Trend Micro (TrendAI) disclosed SHADOW-EARTH-053 on **April 30, 2026**, sharing findings exclusively with *The Register*. Intrusions were detected as recently as April 2026, with the earliest activity traced to **December 2024**. The group compromised more than a dozen critical networks across Poland, Asian countries, and potentially beyond.

SHADOW-EARTH-053 is assessed to be distinct from — but operationally related to — a parallel cluster designated **SHADOW-EARTH-054**. The relationship mirrors the Salt Typhoon / Volt Typhoon model: parallel tracks running simultaneous espionage operations with distinct tooling and targeting.

---

## Mission

SHADOW-EARTH-053 conducts long-dwell strategic espionage focused on **defense industrial base, defense ministries, and government agencies** of nations aligned with the United States and supportive of Taiwan's independence. The group's targeting strongly aligns with China's Belt and Road Initiative (BRI) corridor nations and Belt-and-Road-adjacent geopolitics.

Critically, the group demonstrated **8-month pre-deployment dwell time** before activating ShadowPad — indicating a deliberate strategy of prolonged access before deployment of high-value tooling. This pattern is operationally similar to Volt Typhoon's pre-positioning doctrine.

> **C3PO Assessment:** SHADOW-EARTH-053 may represent strategic pre-positioning ahead of potential US-China conflict, consistent with TrendAI VP Tom Kellermann's attribution context linking the group's timing to the May 14–15, 2026 Trump-Xi summit.

---

## Targeting

### Primary Sectors
- 🛡️ **Defense Industrial Base** — defense contractors and defense ministries
- 🏛️ **Government** — ministries and agencies in BRI corridor nations
- 💻 **Technology** — IT and technology firms
- 🚂 **Transportation** — transportation industry confirmed

### Geographic Focus
- **Pakistan, Thailand, Malaysia, India, Myanmar, Sri Lanka** — BRI corridor
- **Taiwan** — sovereignty dispute
- **Poland** — NATO state; Ukrainian supply-chain transit hub (single confirmed defense-sector target)
- Potentially broader — reported as "more than a dozen" critical networks

---

## TTPs (MITRE ATT&CK)

### Initial Access
| Technique | ATT&CK ID | Notes |
|---|---|---|
| Exploit Public-Facing Application (Exchange) | T1190 | ProxyLogon chain primary vector: CVE-2021-26855, -26857, -26858, -27065 |
| Exploit Public-Facing Application (React) | T1190 | React2Shell (CVE-2025-55182) used in at least one confirmed intrusion |

### Persistence & Execution
| Technique | ATT&CK ID | Notes |
|---|---|---|
| Web Shell | T1505.003 | Godzilla web shell deployed post-Exchange compromise |
| Backdoor Deployment (ShadowPad) | T1543 | Deployed after extended dwell (up to 8 months post-initial-access) |
| Linux Backdoor (NoodleRat) | T1543 | Deployed in Linux environments post-React2Shell exploitation |

### Lateral Movement
| Technique | ATT&CK ID | Notes |
|---|---|---|
| Windows Management Instrumentation (WMIC) | T1047 | Used for lateral movement across victim environments |
| Backdoor on Additional Hosts | T1570 | Installs backdoors on additional hosts via WMIC |

### Defense Evasion
| Technique | ATT&CK ID | Notes |
|---|---|---|
| Long Dwell Time | T1078 | 8-month dwell before high-value tool deployment; evades detection |
| Legitimate Tooling | T1218 | WMIC and other LOLBins |

---

## Malware & Tools Arsenal

| Tool | Notes |
|---|---|
| **ShadowPad** | Custom modular backdoor shared across multiple China-aligned APT groups since 2019; successor to PlugX |
| **NoodleRat** | Linux backdoor widely used by Chinese espionage groups; deployed post-React2Shell |
| **Godzilla Web Shell** | JSP/PHP web shell popular among China-aligned actors; used for initial foothold maintenance |
| **WMIC** | Living-off-the-land lateral movement |

---

## Notable Operations

| Year | Target | Description |
|---|---|---|
| Dec 2024 – Apr 2026 | 8+ countries (Pakistan, Thailand, Malaysia, India, Myanmar, Sri Lanka, Taiwan, Poland) | Strategic espionage campaign; 12+ critical networks compromised; 8-month dwell before ShadowPad deployment |
| 2025 | Poland (defense sector) | Single confirmed NATO-state compromise; defense organization targeted |
| 2025–2026 | Taiwan (multiple targets) | Targeting consistent with PRC sovereignty dispute context |

---

## Key CVEs Exploited

| CVE | Product | Notes |
|---|---|---|
| CVE-2021-26855 | Microsoft Exchange (ProxyLogon) | Primary initial access vector |
| CVE-2021-26857 | Microsoft Exchange | ProxyLogon chain |
| CVE-2021-26858 | Microsoft Exchange | ProxyLogon chain |
| CVE-2021-27065 | Microsoft Exchange | ProxyLogon chain |
| CVE-2025-55182 | React Server Components (React2Shell) | Used in at least one Linux NoodleRat deployment |

---

## Threat Intelligence Grading

| Field | Value |
|---|---|
| **Source Reliability** | B (Trend Micro TrendAI — single vendor; exclusive *The Register* disclosure) |
| **Info Credibility** | 2 (Confirmed by technical analysis; PRC attribution high-confidence via ShadowPad + NoodleRat tooling) |
| **Admiralty Grade** | **B2** |
| **TLP** | TLP:CLEAR |

---

## Attribution

- **Trend Micro TrendAI**: April 30, 2026 — Exclusive disclosure to *The Register*
- **Confidence**: China-aligned (ShadowPad shared tool ecosystem; NoodleRat widely used by Chinese actors; geopolitical targeting pattern consistent with PRC strategic interests)
- **Sponsor**: Unknown specific intelligence agency; consistent with MSS contractor model

---

## Connection Web

- **Volt Typhoon (#008)**: Structural parallel — pre-positioning doctrine, long dwell time, critical infrastructure and defense targeting
- **Salt Typhoon (#010)**: Parallel China-aligned campaign; described as predecessor pattern by TrendAI researcher
- **APT41 (#019)**: Shares ShadowPad (APT41 used ShadowPad since 2019; now shared across multiple China-nexus clusters)
- **UAT-8302 (#045)**: Newly disclosed China-nexus APT with shared tooling ecosystem patterns; different targeting geography
- **SHADOW-EARTH-054**: Parallel operational cluster; relationship details not publicly disclosed

---

## Defensive Recommendations

1. **Patch Microsoft Exchange** — ProxyLogon chain remains SHADOW-EARTH-053's primary vector; any unpatched on-prem Exchange is a confirmed entry point
2. **Hunt for Godzilla web shells** — Common post-exploitation artifact; scan web server directories for JSP/PHP web shells
3. **Monitor for ShadowPad** — EDR detections for ShadowPad are available; any detection should trigger immediate incident response
4. **Apply React2Shell patch (CVE-2025-55182)** — Used in confirmed Linux intrusion chain
5. **Threat hunt for long-dwell indicators** — Low-volume, irregular authentication patterns over weeks/months indicate pre-deployment phase

---

## References

- The Register (exclusive disclosure): https://www.theregister.com/security/2026/04/30/chinese-spy-group-caught-lurking-in-poland-asia-networks/
- Lyrie AI Analysis: https://lyrie.ai/research/research/china-apt-surge-gopherwhisper-shadow-earth-053-glitter-carp-q2-2026

---

*Profile created: 2026-05-18 | Next review: 2026-08-18 | Classification: TLP:CLEAR*
