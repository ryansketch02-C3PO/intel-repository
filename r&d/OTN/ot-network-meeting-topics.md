# OT Network Security — Meeting Topics & Current Risks

> Prepared by C3PO | 2026-04-23 | TLP: WHITE

---

## Foundational Context

### IT vs OT Convergence Risks
Connecting OT to corporate networks (and the internet) has massively expanded the attack surface over the last decade. Legacy systems were never designed for network exposure — protocols like Modbus, DNP3, and Profinet have no built-in authentication or encryption.

### Air Gap Myth
Most "air-gapped" OT environments aren't actually isolated. USB drops, vendor remote access, and IT/OT bridging are common entry points that invalidate assumed isolation.

### Safety Implications
Unlike IT breaches (data loss), OT compromises can cause physical damage, equipment destruction, or safety incidents. The stakes are categorically different — a compromised SCADA system isn't just a data problem, it's a physical-world problem.

---

## Current Threat Actors Targeting OT

### Volt Typhoon (China / PLA) — 🔴 PRE-POSITIONING THREAT
Pre-positioning in US critical infrastructure specifically for contingency disruption — not espionage. CISA confirmed they've been living off the land inside energy, water, and telecom networks, potentially for years. The goal is to be *already inside* before a conflict begins. Deliberately avoids custom malware; uses native tools (LOLBins) to evade detection.

**Sectors:** Power grid, water systems, telecom, transportation
**Key concern:** Long-dwell persistence; designed to survive detection and wait

### Sandworm (Russia / GRU) — 🔴 DESTRUCTIVE OT SPECIALIST
The most prolific OT attacker in history:
- **2015 & 2016:** Took down Ukraine's power grid twice
- **Industroyer/CRASHOVERRIDE:** First malware purpose-built to attack power grid ICS protocols (IEC 104, IEC 61850, GOOSE)
- **TRITON/TRISIS (2017):** Targeted Safety Instrumented Systems (SIS) at a Saudi petrochemical plant — designed to trigger a catastrophic physical failure

**Sectors:** Energy, critical infrastructure, industrial control systems
**Key concern:** Willing and capable of attacking physical safety systems

### APT33 / Peach Sandstorm (Iran / IRGC) — 🔴 WIPER PRE-STAGING
Active in energy and aerospace sectors; known for Shamoon wiper attacks that destroyed 35,000 Saudi Aramco workstations. Pre-stages destructive capability alongside espionage.

**Sectors:** Aerospace, aviation, satellite, defense, energy, petrochemical
**Key concern:** Destructive payloads pre-staged in networks; triggered on geopolitical cue

### Turla (Russia / FSB Center 16) — 🔴 PERSISTENT ESPIONAGE
Long-haul espionage actor with documented presence in energy and aerospace sectors. Operates quietly inside OT-adjacent networks for years without detection. Survived law enforcement disruption (Operation MEDUSA 2023) and rebuilt capabilities.

**Sectors:** Energy, government, defense-adjacent
**Key concern:** Multi-year dwell time; steals operational intelligence (schedules, configs, plans)

---

## Current Risk Areas

### 1. Living-Off-the-Land (LOTL) in OT
Volt Typhoon specifically avoids custom malware, using native Windows tools (wmic, netsh, PowerShell, ntdsutil) for lateral movement and persistence. Traditional signature-based detection is nearly useless against this. OT environments with limited logging and monitoring are especially blind.

### 2. Vendor / Remote Access as Entry Point
Third-party remote access for maintenance and monitoring is one of the most common initial access vectors. Issues include:
- Shared credentials across multiple customers
- Persistent connections left open when not needed
- Minimal logging or session recording
- Exemptions from standard security controls ("it'll break the system")

### 3. Unpatched Legacy Systems
- Windows XP/7 systems still running HMI and SCADA software
- PLCs with firmware from 10–15 years ago
- Unsupported industrial software with known CVEs
- Patching in OT is operationally constrained (can't reboot a running turbine), but the risk accumulates — attackers know this

### 4. Purdue Model Erosion
The traditional OT security architecture (Levels 0–4, with strict zone separation) has been eroded by:
- Cloud-based monitoring and analytics
- Remote access requirements post-COVID
- Flat network designs for convenience
- IoT sensors bridging previously isolated zones

### 5. IT-Side Compromise → OT Pivot
Most OT attacks start in IT. Attackers rarely enter directly through OT protocols — they come through IT and cross the boundary.
- **Colonial Pipeline (2021):** Ransomware in IT systems; operators voluntarily shut down OT as a precaution
- **Oldsmar Water Plant (2021):** Remote access tool (TeamViewer) used to manipulate chemical dosing systems
- **Ukrainian Power Grid (2015):** Spearphishing in IT → pivot to OT SCADA

### 6. Safety System Targeting (SIS)
TRITON/TRISIS was purpose-built to attack Safety Instrumented Systems — the last line of defense against physical accidents. Targeting SIS changes the threat model entirely. An attacker who can disable SIS can potentially cause explosions, fires, or toxic releases while suppressing the automatic safety shutdowns designed to prevent them.

---

## Defensive Topics

| Priority | Topic | Key Question |
|----------|-------|-------------|
| 🔴 | OT Asset Inventory | Do you know every device connected to your OT network? |
| 🔴 | Network Segmentation | Are your DMZ and zone controls actually enforced, or just diagrammed? |
| 🔴 | Remote Access Audit | When did you last audit all remote access paths into OT? |
| 🟡 | Passive OT Monitoring | Are you monitoring OT traffic? (Claroty, Dragos, Nozomi — passive only; active scanning can crash OT devices) |
| 🟡 | OT Incident Response Plan | Does your IR plan account for OT constraints? Can you safely isolate a SCADA server mid-process? |
| 🟡 | Credential Hygiene | Are vendor accounts unique, time-limited, and revoked when access is no longer needed? |
| 🟢 | CISA / ICS-CERT Subscriptions | Are you subscribed to ICS-CERT advisories and acting on them? |
| 🟢 | Purple Team / OT Tabletop | When did you last run an OT-specific incident scenario with operations and security together? |

---

## Discussion Questions

1. *Do we know every device connected to our OT network?*
2. *When did we last audit remote access paths into OT systems?*
3. *What's our detection capability inside OT — are we monitoring OT traffic at all?*
4. **If an attacker had been in our OT network for 6 months, would we know?** *(Volt Typhoon's question)*
5. *Do we have a plan for OT-specific incident response that accounts for safety and operational continuity?*
6. *Are our safety systems (SIS) isolated from both IT and general OT network reach?*
7. *How are we handling vendor and third-party remote access? Are those connections always-on or on-demand?*

---

## Key References

- CISA Advisory AA24-038A — PRC State-Sponsored Cyber Activity Affecting US Critical Infrastructure (Volt Typhoon)
- CISA Advisory AA22-103A — Tactics, Techniques, and Procedures of Indicted State-Sponsored Russian Cyber Actors (Sandworm)
- DRAGOS Year in Review (annual) — OT/ICS threat landscape
- ICS-CERT (ics-cert.cisa.gov) — vulnerability advisories for industrial control systems
- NIST SP 800-82 Rev 3 — Guide to OT Security
- IEC 62443 — Industrial Automation and Control Systems Security standard

---

*C3PO Intel | 2026-04-23 | TLP: WHITE*
