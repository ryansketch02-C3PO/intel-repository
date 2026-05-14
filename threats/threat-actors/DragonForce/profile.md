# Threat Actor Profile: DragonForce

## Identity

| Field | Detail |
|---|---|
| **Scoundrel #** | 033 |
| **Primary Name** | DragonForce |
| **Type** | Cybercriminal — Ransomware-as-a-Service (RaaS) / "Cartel" Platform |
| **Origin** | Unknown operators; encryptor code lineage from leaked **Conti v3** and **LockBit 3.0** |
| **Active Since** | Late 2023; major restructuring into cartel model March 2025 |
| **Status** | 🔴 ACTIVE — growing every month in 2026; 3rd most active RaaS globally |
| **Threat Level** | 🔴 HIGH |
| **Affiliate Split** | 80/20 — affiliates keep 80%; DragonForce takes 20% |
| **Sectors Targeted** | Retail, Airlines, Insurance, Technology, Logistics, MSPs, Healthcare, Financial Services |
| **Geographic Focus** | Global — notable concentration of UK attacks; US, Europe, Australia |
| **Operational Volume** | 207 victims YTD 2026 (Jan–Apr); 8% of all global ransomware attacks in March 2026 |
| **ATT&CK** | T1190, T1078, T1566, T1621, T1543.003, T1059.001, T1082, T1003, T1021.001, T1562.001, T1105, T1486, T1490, T1567.002 |

---

## Overview

DragonForce is not just another ransomware group — it is a **criminal infrastructure platform** running the most novel business model in the ransomware ecosystem: a white-label "cartel" that lets affiliates deploy attacks under their own brand while DragonForce handles the back-end. The group's encryptor code shows lineage from leaked **Conti v3** and **LockBit 3.0** source code, giving it a technical foundation built on two of history's most destructive ransomware operations.

**The number that defines DragonForce's 2025:** £592 million. That is the combined estimated financial impact of three coordinated attacks against British retailers — Marks & Spencer, Co-op, and Harrods — carried out in April–May 2025 by **Scattered Spider** (#013) using DragonForce's rented infrastructure. M&S alone lost over £300 million in operating profit and over £1 billion in market capitalization. Co-op had 6.5 million members' personal data stolen. Three of the four people arrested in July 2025 were teenagers.

They did it with a phone call.

**The cartel model (March 2025):** DragonForce restructured as a self-described "cartel" — offering affiliates complete white-label service where they operate under their own branding while DragonForce provides encryption tools, negotiation infrastructure, payment systems, and data audit services. In October 2025, DragonForce announced a coalition with LockBit and Qilin on its leak site, positioning itself as the organizing force of a federated criminal network.

The ambition is real. DragonForce defaced the leak sites of competitors **BlackLock** and **Mamona** within 24 hours of announcing the cartel — signaling hierarchy, not just alliance. Growing every month in 2026, DragonForce is on a trajectory that could see it challenge Qilin's volume leadership by Q3 2026.

---

## The Cartel Model — What Makes This Different

Traditional RaaS: **Operator develops locker → affiliate deploys → revenue split.**

DragonForce cartel: **Operator runs infrastructure → affiliates operate under ANY brand → operator audits stolen data to maximize extortion → revenue split with data intelligence layer.**

| Feature | Standard RaaS | DragonForce Cartel |
|---|---|---|
| Brand | Affiliate uses operator's brand | **Affiliate can use their OWN brand on DragonForce infrastructure** |
| Revenue split | 70–85% to affiliate | **80% to affiliate** |
| Negotiation | Affiliate manages | DragonForce provides negotiation infrastructure |
| Data intelligence | Affiliate analyzes exfil independently | **DragonForce "data audit" service** — analyzes >300GB datasets from $15M+ revenue victims; tells affiliate which files to lead extortion with |
| Recruitment | Standard underground forum ads | **"Cartel" coalition** — shared IAB pipelines with LockBit and Qilin affiliates |
| Resilience | Single operator takedown risk | **Federated** — individual affiliate brands can disappear without affecting platform |

The white-label model is particularly dangerous from a detection perspective: attacks attributed to different "groups" may all be running on DragonForce infrastructure with DragonForce tooling. The brand diversity masks the underlying platform concentration.

---

## The UK Retail Attacks — April–May 2025

The most consequential DragonForce operation to date was executed entirely through social engineering:

### Marks & Spencer — Hit: April 3, 2025
- **Entry:** Scattered Spider affiliates impersonated IT staff or third-party vendor contacts; called help desk and requested MFA reset / password change
- **Dwell time:** Attackers maintained invisible access for approximately 2 months before detonation
- **Payload:** DragonForce ransomware deployed Easter weekend (timing deliberate — reduced IT staffing)
- **Impact:**
  - Online orders suspended for ~1 week; disruption into May
  - 200 job listings pulled offline
  - Contactless payment failures across stores
  - Click-and-collect suspended
  - Customer data accessed (scope not fully disclosed)
  - £300M+ operating profit loss
  - >£1B market cap drop within days of disclosure
  - ~£100M cyber insurance claim filed
  - Ransom payment status: **never confirmed** (M&S chairman declined to answer before Parliament)

### Co-op — Hit: ~April 6–8, 2025
- Initial downplay as "minor IT issue" — escalated rapidly
- IT infrastructure shutdown — disabled stock monitoring and back-office functions across **2,300 UK stores**
- Internal memo leaked: staff told to keep cameras on during Teams calls and verify all attendees
- **6.5 million Co-op members'** names and contact details exfiltrated
- Revenue losses undisclosed

### Harrods — Hit: April 12, 2025
- Confirmed attempted unauthorized access
- **Contained before major damage** — decisive isolation of internet access across stores
- External consultants brought in
- Operations briefly disrupted but website and stores remained operational

**Arrests:** July 10, 2025 — UK NCA arrested 4 individuals at home addresses. **Three were teenagers.** None formally charged at time of reporting. One (Noah Urban, 20, "King Bob") — previously a Scattered Spider member in the US — sentenced in August 2025.

**Attributed by:** M&S Chairman Archie Norman (before Parliament); Mandiant; UK NCSC

---

## Technical Profile

### Encryptor
- **Lineage:** Leaked **Conti v3** and **LockBit 3.0** source code — substantial shared routines and artifacts
- **Targets:** Windows, Linux, VMware ESXi — cross-platform
- **Defense evasion:** BYOVD (Bring Your Own Vulnerable Driver) — loads signed but vulnerable drivers to kill security processes from kernel level
- **Persistence:** Malicious Windows service creation (T1543.003)

### Attack Chain (with Scattered Spider affiliates)
```
RECON: Extensive pre-attack target research
       Organizational structure, key personnel, social media profiling
       Job listing review to identify IT vendor names, helpdesk processes
       ↓
ACCESS: Social engineering help desk via phone
        Impersonate IT staff, executive, or third-party vendor
        SIM swapping / MFA fatigue / convincing pretexts
        Request password reset or MFA device re-enrollment
        ↓
FOOTHOLD: RMM tool installation (AnyDesk, etc.)
          Long-duration dwell — weeks to months of reconnaissance
          ↓
LATERAL: Credential dumping (Mimikatz, LSASS)
         RDP lateral movement
         Domain controller access → domain-wide credential harvest
         ↓
PRE-ENCRYPTION: BYOVD — disable EDR/AV
                VSS deletion
                Data staging + exfiltration to cloud storage
                ↓
DEPLOY: DragonForce encryptor (Conti/LockBit lineage)
        Windows + Linux + ESXi variants
        ↓
EXTORT: DragonForce infrastructure handles negotiation + payment
        DragonForce "data audit" service identifies highest-value exfil for leverage
        Deadline-based pressure + data leak threat
```

### Tools Observed
| Tool | Purpose |
|---|---|
| AnyDesk, RMM tools | Initial persistence post-social-engineering access |
| Mimikatz | Credential dumping |
| BYOVD drivers | EDR/AV killer (kernel-level) |
| VSS deletion tools | Inhibit recovery |
| Rclone | Cloud exfiltration |
| Cobalt Strike | Post-exploitation C2 |
| PowerShell | LOLBin lateral movement |

---

## Operational Volume

| Period | Victims | Notes |
|---|---|---|
| March 2026 | 54 | 8% of all global ransomware attacks |
| April 2026 | 63 | Growing every month; 3rd most active globally |
| YTD 2026 (Jan–Apr) | 207 | Consistent growth trajectory |
| Total documented | 200+ | Per Anomali, as of late 2025 |

**Trajectory:** DragonForce has grown its victim count every single month in 2026. At the current rate, it is positioned to challenge Qilin (#031) for the top spot by Q3 2026.

---

## The Coalition / "Cartel"

In October 2025, DragonForce announced a coalition with **LockBit (#015)** and **Qilin (#031)** on its leak site. In practice this functions as a federated network rather than a formal merger:
- Shared IAB pipelines
- Cross-group affiliate recruitment
- Mutual escrow for high-value deals
- Possibly shared leak-site mirroring for takedown resilience

The three groups continue to compete for affiliates with their own splits and services. DragonForce's position as the organizer — having defaced competitor sites to demonstrate dominance — gives it structural advantage in the coalition.

---

## Threat Connections

🔗 **Connects to Scoundrel #013 — Scattered Spider**
Confirmed affiliate relationship. Scattered Spider executed the UK retail campaign (M&S, Co-op, Harrods) using DragonForce's ransomware infrastructure and negotiation platform. The combination of Scattered Spider's social engineering mastery with DragonForce's backend produced the most destructive UK retail attack in history.

🔗 **Connects to Scoundrel #030 — RansomHub (DARK)**
DragonForce was a secondary beneficiary of RansomHub's April 2025 shutdown, absorbing a portion of the displaced affiliate base. Check Point attributed DragonForce's March 2026 activity spike directly to absorption of former RansomHub affiliates.

🔗 **Connects to Scoundrel #031 — Qilin**
"Cartel" coalition partners — announced October 2025. Competing for the same affiliate pool while nominally cooperating on infrastructure and intelligence sharing. Primary rivals in Q2 2026 race for volume dominance.

🔗 **Connects to Scoundrel #015 — LockBit**
Cartel coalition + code lineage. DragonForce encryptor uses LockBit 3.0 code. LockBit joined the DragonForce-announced cartel in October 2025.

🔗 **Connects to Scoundrel #028 — APT73 / Bashe**
APT73 emerged from the LockBit/RansomHub diaspora at the same time DragonForce was growing. Overlapping affiliate talent pool; some operators may run under multiple flags.

---

## A&D Relevance

**Indirect but present.** DragonForce's primary focus is retail, logistics, airlines, and insurance — not A&D prime contractors. However:
- **Managed Service Providers (MSPs)** are a confirmed DragonForce target. Any A&D organization using an MSP sits one step behind the target line.
- **Supply chain contractors** in logistics, IT services, and aviation that support A&D programs are in DragonForce's sweet spot.
- **The social engineering vector** (no technical exploit required) works against any organization with a human help desk — including cleared defense facilities.
- **BYOVD and LockBit/Conti toolchain** are fully capable of targeting any Windows/Linux/ESXi environment regardless of sector.

---

## Mitigations

| Priority | Action |
|---|---|
| 🔴 IMMEDIATE | **Harden help desk authentication** — require video verification + callback to a known number for ANY sensitive account change (password reset, MFA re-enrollment, device registration). No exceptions for callers claiming urgency. |
| 🔴 IMMEDIATE | **Implement MFA that resists fatigue** — switch from push notification MFA to FIDO2/passkey for all privileged accounts. MFA fatigue + SIM swap resistance are the primary Scattered Spider entry vectors. |
| 🔴 HIGH | **Block unapproved BYOVD drivers** — enforce WDAC/Driver Allowlisting; alert on loading of signed-but-vulnerable drivers outside approved list |
| 🔴 HIGH | **Alert on VSS deletion** — no silent vssadmin or wmic shadow deletion in production; block via ASR rules |
| 🟡 HIGH | **Segment IT help desk from admin systems** — even if social engineering succeeds, help desk should not have direct path to domain admin or MFA admin portals |
| 🟡 HIGH | **Monitor for RMM tool installation by non-IT accounts** — initial persistence after help desk access typically uses AnyDesk, TeamViewer, or similar |
| 🟡 HIGH | **Hunt for long-dwell access** — the M&S attackers were inside for ~2 months before detonation. Behavior analytics for low-and-slow recon during "normal" business activity. |
| 🟡 MEDIUM | **Test help desk procedures via red team social engineering** — the M&S attack vector is well documented; if your help desk hasn't been tested against it, assume it's vulnerable |

---

## References

- [InnovationND — Scattered Spider Returns: UK Retail Giants Fall to Social Engineering Blitz (April 15, 2026)](https://www.innovationnd.com/articles/intel/scattered-spider-uk-retail-dragonforce-april-2026)
- [Anomali Cyber Watch — DragonForce Cartel, GootLoader, SesameOp (November 2025)](https://www.anomali.com/blog/anomali-cyber-watch-sesameop-backdoor-dragonforce-cartel-gootloader-malware-and-more)
- [Netguardia — Mapping Ransomware-as-a-Service Affiliate Economics in 2026](https://netguardia.com/cybersecurity-intelligence/threat-intelligence/mapping-ransomware-as-a-service-affiliate-economics-in-2026/)
- [Breachsense — April 2026 Ransomware Report](https://www.breachsense.com/ransomware-reports/april-2026/)
- [Infosecurity Magazine — Just Three Ransomware Gangs Accounted for 40% of Attacks (April 10, 2026)](https://www.infosecurity-magazine.com/news/three-ransomware-gangs-40-percent/)
- [Arxxis — How a Teenager Called a Helpdesk and Cost Britain's Biggest Stores Millions (April 13, 2026)](https://www.youtube.com/watch?v=gpR0kfLcRcM)

---

*Profile created: 2026-05-02 | Last Updated: 2026-05-14 | Author: C3PO | Admiralty Grade: A1 | TLP: WHITE*

---

## 📡 Intelligence Update — 2026-05-14

### Q1 2026 Activity Surge: 101 Victims (+29% QoQ) — Accelerating Momentum

Check Point Research's **State of Ransomware Q1 2026** report confirmed DragonForce posted **101 victims** in Q1 2026, a **29% increase** from Q4 2025, with a steep quarter-over-quarter trajectory (10 victims in January → 35 in February → 56 in March). This pattern indicates **genuine operational momentum** rather than a one-time access stockpile drawdown.

Arete IR April 2026 report confirmed DragonForce was responsible for **over 7% of observed ransomware engagements** in April 2026 with noted increased activity compared to full-year 2025 baseline.

### New Tool: VIPERTUNNEL Python-Based Backdoor

Open-source reporting cited in Arete's April 2026 report documents DragonForce's deployment of **VIPERTUNNEL** — a **Python-based backdoor** used for maintaining reliable operator access and evading detection:

- Provides persistent reverse tunnel C2 channel
- Python-based construction aids AV/EDR evasion in environments with limited Python baseline monitoring
- Deployed alongside BYOVD techniques and RMM tooling for layered access redundancy

### BYOVD: PoisonX.sys Vulnerable Driver

DragonForce has been confirmed using the **PoisonX.sys** vulnerable driver in recent engagements as part of a Bring Your Own Vulnerable Driver (BYOVD) attack chain for kernel-level security process termination. The group also deployed the **Remotely Agent** remote desktop application for persistent access.

DragonForce shares overlapping vulnerable drivers with **Akira** (rwdrv.sys, hlpdrv.sys), suggesting possible tooling exchange or shared affiliate tradecraft within the RaaS ecosystem.

### Geographic Targeting

DragonForce remains active across multiple geographies with **Turkey** as a notable concentration (DragonForce + LockBit + The Gentlemen = 70% of Turkish victim postings in Q1). The group's trajectory from the UK retail attacks (April 2026) through Q1 indicates expanding global reach beyond its initial UK/Australia focus.
