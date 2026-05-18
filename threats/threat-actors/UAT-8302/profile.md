# UAT-8302

## Identity

| Field | Value |
|---|---|
| **Scoundrel #** | 045 |
| **Primary Name** | UAT-8302 |
| **AKA** | Overlaps with: Jewelbug / REF7707 / CL-STA-0049 / LongNosedGoblin / UAT-6382 (shared tooling; not confirmed identical) |
| **Type** | State-Sponsored Cyber Espionage |
| **Origin** | People's Republic of China (PRC) |
| **Sponsor** | Unknown (China-nexus; assessed by Cisco Talos with high confidence) |
| **Base** | Unknown |
| **Active Since** | Late 2024 (South America); 2025 (Southeastern Europe) |
| **Status** | 🔴 ACTIVE |
| **Threat Level** | 🔴 HIGH |
| **MITRE ATT&CK ID** | Pending |

---

## Discovery

Cisco Talos disclosed UAT-8302 on **May 5, 2026**, following a multi-year investigation. The group has targeted South American government entities since at least **late 2024** and government agencies in southeastern Europe in **2025**. The disclosure by Talos researchers Jungsoo An, Asheer Malhotra, and Brandon White represents the first public attribution.

UAT-8302 is structurally significant not just as a threat actor but as **evidence of China's shared tooling infrastructure**: the group deploys malware families previously linked to at least five distinct China-nexus APT clusters simultaneously, demonstrating either a centralized malware pool, a contractor network, or deliberate tool-sharing among MSS-affiliated operators.

---

## Mission

UAT-8302 is tasked with **obtaining and maintaining long-term access** to government entities worldwide for intelligence collection purposes. Post-compromise activity focuses on:
- Information collection
- Credential extraction
- Network mapping and lateral movement
- Persistent backdoor maintenance

The selection of South American and European government targets suggests an MSS-directed collection requirement with a broad geographic mandate — distinct from China-nexus actors with narrower regional focuses (e.g., APT40's Indo-Pacific maritime focus or Salt Typhoon's telecom-sector focus).

---

## Targeting

### Primary Sectors
- 🏛️ **Government** — South American and southeastern European government ministries and agencies (primary confirmed)
- 🔭 **Suspected broader targeting** — Talos assesses the group has a global government collection mandate

### Geographic Focus
- **South America** — government entities (late 2024–present)
- **Southeastern Europe** — government agencies (2025)
- Suspected global reach based on tool diversity and operational tempo

---

## TTPs (MITRE ATT&CK)

### Initial Access
| Technique | ATT&CK ID | Notes |
|---|---|---|
| Exploit Public-Facing Application | T1190 | Suspected zero-day and N-day exploits against web applications; specific CVEs not yet disclosed |

### Discovery & Lateral Movement
| Technique | ATT&CK ID | Notes |
|---|---|---|
| Network Reconnaissance | T1046 | Automated scanning using open-source gogo tool |
| Lateral Movement via Implants | T1570 | Post-compromise proliferation using Impacket and custom tools |

### Command & Control
| Technique | ATT&CK ID | Notes |
|---|---|---|
| Web Protocol C2 (NetDraft/CloudSorcerer) | T1071.001 | NetDraft uses FinalDraft/SquidDoor communication protocol; CloudSorcerer uses cloud services |
| Proxy/Tunnel | T1090 | Stowaway and SoftEther VPN for alternative backdoor access and traffic obfuscation |

### Defense Evasion
| Technique | ATT&CK ID | Notes |
|---|---|---|
| Masquerading (Rust stager) | T1036 | SNOWRUST Rust-based stager downloads VShell, blends with legitimate traffic |
| Proxy Tool Abuse | T1090.002 | SoftEther VPN used to disguise C2 traffic |

---

## Malware & Tools Arsenal

### Custom Malware
| Tool | Notes |
|---|---|
| **NetDraft (NosyDoor)** | .NET/C# backdoor; variant of FinalDraft/SquidDoor family; previously attributed to Jewelbug/REF7707/CL-STA-0049/LongNosedGoblin |
| **SNOWRUST** | Rust-based stager (new); downloads VShell payload from remote server |

### Shared / Previously Attributed Tools
| Tool | Origin Attribution | Notes |
|---|---|---|
| **CloudSorcerer v3.0** | Previously used against Russian government entities (2024) | C2 over cloud services |
| **VShell** | UNC5174, UNC6586, UAT-6382 | Backdoor delivered by SNOWLIGHT stager |
| **SNOWLIGHT** | UNC5174, UNC6586, UAT-6382 | VShell stager |
| **DeedRAT (Snappybee)** | Earth Estries (Salt Typhoon cluster) | ShadowPad successor; deployed alongside ZingDoor |
| **ZingDoor** | Earth Estries (Salt Typhoon cluster) | Deployed in conjunction with DeedRAT |
| **Draculoader** | Generic shellcode loader | Delivers Crowdoor and HemiGate |

### Open-Source / Utility Tools
| Tool | Notes |
|---|---|
| **gogo** | Open-source network scanning/automated reconnaissance |
| **Impacket** | Credential extraction and lateral movement |
| **Stowaway** | Proxy tool for alternative backdoor access |
| **SoftEther VPN** | Traffic obfuscation and C2 tunneling |

---

## Notable Operations

| Year | Target | Description |
|---|---|---|
| Late 2024 – 2026 | South American governments | Sustained intrusion campaign; information collection, credential theft, network mapping |
| 2025 | Southeastern European governments | Expansion into European government targets; same toolset |

---

## Structural Significance: The Shared Tooling Problem

UAT-8302 is the clearest recent evidence of **China's centralized offensive cyber infrastructure**. The simultaneous deployment of tools previously attributed to five separate APT clusters (Jewelbug, Earth Estries, UNC5174, LongNosedGoblin, UAT-6382) within a single operation strongly suggests:

1. A **centralized tool repository** accessible to multiple MSS contractor groups
2. A **shared malware development program** coordinated at the intelligence apparatus level
3. **Deliberate tool reuse** that complicates vendor attribution and makes cluster-based tracking less reliable

> *"UAT-8302 is less a 'new threat actor' story and more a structural intelligence story about how China organizes offensive cyber operations."* — Lyrie AI, May 11, 2026

---

## Threat Intelligence Grading

| Field | Value |
|---|---|
| **Source Reliability** | A (Cisco Talos — primary source with full technical disclosure) |
| **Info Credibility** | 1 (Confirmed by technical analysis with high-confidence China-nexus assessment by Talos) |
| **Admiralty Grade** | **A1** |
| **TLP** | TLP:CLEAR |

---

## Attribution

- **Cisco Talos**: May 5, 2026 — "UAT-8302 and its box full of malware"
- **Confidence**: High confidence — China-nexus (Talos explicit assessment); sponsor org not identified
- **Tool links**: NetDraft → Jewelbug/REF7707; CloudSorcerer → prior Russia-targeting operation; DeedRAT/ZingDoor → Earth Estries/Salt Typhoon cluster

---

## Connection Web

- **Salt Typhoon (#010)**: DeedRAT and ZingDoor overlap (both deployed by Earth Estries/Salt Typhoon cluster AND UAT-8302)
- **APT41 (#019)**: Both MSS-linked; APT41 represents the better-documented end of the same contractor ecosystem
- **GopherWhisper (#043)**: Another newly disclosed China-aligned APT using legitimate cloud services; different toolset, similar operational pattern of shared infrastructure
- **SHADOW-EARTH-053 (#044)**: Co-emerged in same disclosure cycle; both represent China's below-noise-floor APT proliferation

---

## Defensive Recommendations

1. **Patch web-facing applications aggressively** — Initial access is suspected to exploit web app vulnerabilities
2. **Hunt for FinalDraft/SquidDoor variants** — NetDraft is a C# variant; behavioral detection on .NET backdoors connecting to cloud storage APIs is effective
3. **Monitor for CloudSorcerer C2 patterns** — Cloud service API connections from unexpected processes
4. **Alert on SoftEther VPN processes** — Legitimate VPN tool abused for C2 obfuscation
5. **Block gogo scanning tool** — Known reconnaissance tool; any instance outside approved red-team use is a red flag
6. **Audit Impacket usage** — Common in post-exploitation; flag any Impacket execution not tied to authorized AD administration

---

## References

- Cisco Talos Blog: https://blog.talosintelligence.com/uat-8302/
- The Hacker News: https://thehackernews.com/2026/05/china-linked-uat-8302-targets.html
- Lyrie AI Research: https://lyrie.ai/research/research/uat-8302-china-nexus-apt-shared-malware-toolkit-government-espionage

---

*Profile created: 2026-05-18 | Next review: 2026-08-18 | Classification: TLP:CLEAR*
