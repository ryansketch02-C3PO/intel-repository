# CyberAv3ngers (IRGC-EWCD)

**Scoundrel #039**

| Field | Value |
|-------|-------|
| **Aliases** | CyberAv3ngers, Cyber Av3ngers, Cyber Avengers, IRGC Cyber Av3ngers |
| **Type** | State-Sponsored — OT/ICS Sabotage + Hacktivist Proxy |
| **Origin** | Iran — IRGC-EWCD (Islamic Revolutionary Guard Corps, Electronic Warfare and Cyber Defense) |
| **Sponsor** | IRGC — directly affiliated unit; not a proxy |
| **Status** | 🔴 ACTIVE — Ongoing targeting of Western and Israeli critical infrastructure |
| **Active Since** | ~2020 (public claims); significant escalation 2023–present |
| **Threat Level** | 🔴 HIGH — Confirmed OT/ICS compromise of US critical infrastructure; DOJ-indicted; CISA Emergency Directive |
| **Sector Focus** | Water & Wastewater · Energy (Electric Grid) · Food & Agriculture · Transportation · Any Israeli/Western-made ICS equipment |
| **MITRE ATT&CK** | ICS Matrix — T0817, T0866, T0883, T0855 |
| **Admiralty Grade** | A1 — DOJ indictments, CISA AA23-335A, FBI/NSA/EPA joint advisory, confirmed physical compromise |

---

## TL;DR

CyberAv3ngers is Iran's dedicated **operational technology (OT) and industrial control system (ICS) attack unit**, operating directly under the IRGC's Electronic Warfare and Cyber Defense (EWCD) organization. Unlike most Iranian cyber groups that focus on IT espionage or data theft, CyberAv3ngers specifically targets the **physical systems that run critical infrastructure** — water treatment PLCs, power grid SCADA, and industrial equipment.

In November 2023, they compromised US municipal water utilities using default credentials on **Unitronics Vision Series PLCs** — Israeli-made hardware. The message was explicit: defaced HMI screens read *"You have been hacked, down with Israel. Every equipment 'Made in Israel' is CyberAv3ngers legal target."* The FBI, CISA, NSA, EPA, and State Dept all issued joint warnings. In February 2024, the DOJ indicted **six named IRGC-EWCD officers**. A **$10M Rewards for Justice bounty** is currently active.

Their primary innovation — and primary danger — is framing Israeli-manufactured ICS/OT equipment as a legitimate military target regardless of where it's installed. An Israeli-made Unitronics PLC in a Pennsylvania water plant is, in their doctrine, as valid a target as one in Tel Aviv.

---

## Organizational Context

### IRGC-EWCD
- The **Electronic Warfare and Cyber Defense (EWCD)** is a dedicated IRGC organization responsible for both offensive cyber operations and protecting Iranian cyber/electronic infrastructure
- Distinguished from the IRGC-IO (Intelligence Organization) which runs Charming Kitten (#011) and UNC1549 (#004)
- EWCD has documented links to APT33 / Peach Sandstorm (#024) — both operate under the IRGC umbrella though different units
- Operates both as a **direct attack unit** (CyberAv3ngers) and oversees contractor/proxy groups

### Hacktivist Proxy Framing
- CyberAv3ngers presents publicly as a "hacktivist" collective with pro-Palestinian, anti-Israel messaging
- This framing provides plausible deniability and recruitment/propaganda value
- Reality: DOJ indictments confirm **direct IRGC-EWCD officer involvement** — not an independent hacktivist group
- Pattern mirrors Handala Hack (#014) — MOIS-affiliated but publicly hacktivist-branded

---

## Key Operations

### 🚰 US Water Utility Attacks — November–December 2023 ⭐ LANDMARK

**The first confirmed nation-state compromise of US water infrastructure with physical OT access.**

**Method:**
- Identified internet-exposed **Unitronics Vision Series PLCs** via Shodan
- Exploited default credentials (Unitronics ships with default password `1111` — widely unchanged in operational deployments)
- Gained access to **HMI (Human-Machine Interface)** screens controlling water treatment processes
- Defaced HMI screens with anti-Israel messaging

**Confirmed Victims:**
| Utility | Location | Notes |
|---------|----------|-------|
| Municipal Water Authority of Aliquippa | Aliquippa, PA | ⭐ Most widely reported; PLC at booster station compromised; water service uninterrupted but safety systems bypassed |
| North Texas Municipal Water District | TX | Claimed; investigation ongoing |
| Multiple undisclosed utilities | US, EU, Australia | CISA confirmed "multiple" victims beyond named cases |

**Government Response:**
- **CISA Emergency Advisory AA23-335A** (December 1, 2023) — joint with FBI, NSA, EPA, CISA
- EPA issued enforcement discretion guidance for water utilities
- White House NSC held emergency briefing with water utility CEOs
- **Sector-wide alert**: All US water utilities using Unitronics equipment told to immediately change default credentials, isolate from internet

**Physical Risk Assessment:**
- Aliquippa booster station controlled water pressure to a 6,600-person service area
- Had operators not caught the intrusion, attackers had HMI-level access to modify pump operations
- CISA assessment: sufficient access to **alter chemical dosing and water pressure** at compromised sites

---

### 🇮🇱 Israeli Critical Infrastructure Targeting (Ongoing)

- Claimed attacks on Israeli **rail networks, electric grid, steel plants** (some verified, some overstated)
- Confirmed: participation in broader IRGC campaign against Israeli water infrastructure (alongside Predatory Sparrow / Gonjeshke Darande activity)
- Active on **Telegram** — posts claimed breach screenshots, data dumps, and anti-Israel propaganda
- Pattern: claims are frequently larger than verifiable impact, but physical OT access cases are credibly confirmed

---

### ⚡ Expanded ICS/OT Targeting — 2024–2026

Following CISA AA23-335A, CyberAv3ngers did not stand down — they escalated targeting:

- **Energy sector**: Reported scanning of internet-exposed SCADA systems at US electric utilities
- **Food & Agriculture**: Targeting of agricultural water management systems (irrigation control PLCs)
- **Transportation**: Interest in rail and port logistics OT systems (less confirmed)
- **Global scope**: Australia, UK, EU utilities also confirmed in joint advisory scope
- Continuing to exploit any internet-exposed **Israeli-manufactured ICS equipment** globally — Unitronics, Orpak (fuel management), others

---

## DOJ Indictments — February 2024 ⚖️

**United States v. Hamid Homayunfal et al.**
Six IRGC-EWCD officers indicted on charges of computer fraud and conspiracy:

| Name | Role | Notes |
|------|------|-------|
| **Mahdi Lashgarian** | IRGC-EWCD Commander | Most senior; directed operations |
| **Hamid Homayunfal** | Deputy Commander | Operational oversight |
| **Milad Mansuri** | Technical Operations | Direct ICS exploitation |
| **Mohammad Bagher Shirinkar** | Technical Operations | Infrastructure management |
| **Reza Rashidian** | Reconnaissance | Target identification via Shodan |
| **Alireza Shafie Nasab** | Technical Operations | Previously indicted in separate case |

- **$10M Rewards for Justice bounty** active for all six
- All remain in Iran; no extradition likely
- Indictments serve primarily as attribution signal and sanctions trigger

---

## Tradecraft & TTPs

### Initial Reconnaissance
- **Shodan / Censys** — search for internet-exposed Unitronics Vision Series, Orpak, and other Israeli-made OT devices
- Queries: `port:20256 country:US` (Unitronics default port), product-specific banners
- Free, open-source, requires zero hacking capability to identify targets
- **Zeek / Shodan Monitor** alerts — set up persistent monitoring of target device types

### Initial Access
| Vector | Detail |
|--------|--------|
| Default credentials | Unitronics Vision Series shipped with default password `1111`; widely unchanged |
| CVE-2023-6448 | Unitronics Vision Series authentication bypass; CVSS 9.8 |
| VPN / RDP brute force | IT network entry point for OT pivot |
| Spearphishing | IT staff targeting; pivot to OT network |

### OT / ICS Exploitation
- Direct **HMI access** via Unitronics programming software (VisiLogic) or web interface
- **PLC logic modification** capability (confirmed access; not confirmed deployed destructively in US cases)
- **Ladder logic** review and potential modification
- HMI screen defacement (confirmed tactic — propaganda + proof of access)
- Process parameter reading (confirming physical plant state before potential manipulation)

### Persistence & Lateral Movement
- Establish footholds on IT network via standard Windows TTPs (stolen credentials, RDP)
- **IT/OT boundary crossing** via historian servers, DMZ, or direct connections
- Maintain access via legitimate remote management tools (screen sharing, VPN accounts)

### Exfiltration & Impact
- Screenshot/screengrab of HMI screens for Telegram propaganda posts
- Configuration file theft (ladder logic programs, PLC config)
- **Potential physical impact**: Modify setpoints for chemical dosing, pressure control, flow rates
- **Defacement** of operator interfaces (confirmed; primary visible impact in US cases)

---

## MITRE ATT&CK for ICS

| Tactic | Technique | Notes |
|--------|-----------|-------|
| Initial Access | T0817 — Drive-by Compromise | Web-exposed HMI panels |
| Initial Access | T0866 — Exploitation of Remote Services | CVE-2023-6448, default creds |
| Discovery | T0840 — Network Connection Enumeration | Shodan pre-attack recon |
| Discovery | T0846 — Remote System Discovery | ICS device fingerprinting |
| Lateral Movement | T0812 — Default Credentials | Unitronics `1111` password |
| Lateral Movement | T0859 — Valid Accounts | Stolen IT credentials for OT pivot |
| Collection | T0802 — Automated Collection | HMI screen capture |
| Impact | T0883 — Internet Accessible Device | Exploiting internet-exposed PLCs |
| Impact | T0855 — Unauthorized Command Message | PLC command injection capability |
| Impact | T0826 — Loss of Availability | Potential shutdown of water systems |
| Impact | T0831 — Manipulation of Control | Setpoint modification capability |

---

## Detection & Defense Guidance

### For Water / Wastewater Utilities
1. **Immediately remove Unitronics PLCs from internet** — no ICS device should be directly internet-accessible
2. **Change all default credentials** — Unitronics default `1111`; every device
3. **Patch CVE-2023-6448** — apply Unitronics firmware update
4. **Network segmentation** — IT/OT air gap or strict DMZ with unidirectional gateway
5. **Monitor VisiLogic connections** — alert on any unauthorized programming software connections

### Detection Signatures
- Unitronics port 20256 inbound connections from non-admin IPs
- VisiLogic.exe process execution outside maintenance windows
- HMI screen changes outside change management records
- SCADA historian connections from non-standard systems
- Shodan scan signatures hitting ICS ports (20000, 102, 502, 44818, 47808)

### Threat Hunt Query (Splunk)
```spl
index=network dest_port IN (20256, 20257)
| where src_ip != "10.0.0.0/8" AND src_ip != "172.16.0.0/12" AND src_ip != "192.168.0.0/16"
| stats count by src_ip, dest_ip, dest_port
| where count > 3
| lookup threat_intel_ip src_ip OUTPUT threat_actor
```

---

## Connections to Existing Profiles

| Actor | # | Relationship |
|-------|---|--------------|
| APT33 / Peach Sandstorm | #024 | Same IRGC parent organization; EWCD and IRGC-IO overlap |
| UNC1549 | #004 | IRGC cyber operations; shared targeting of aerospace/defense adjacent infrastructure |
| Handala Hack | #014 | Both target Israeli infrastructure; both use hacktivist-proxy public branding |
| MuddyWater | #022 | MOIS equivalent; coordinated Iranian cyber ops during heightened conflict |
| Charming Kitten | #011 | IRGC-IO; different unit, coordinated national targeting priorities |

---

## Intelligence Update — 2026-05-10

**Source grade: A1 | Analyst: C3PO**

As of May 2026, CyberAv3ngers remains operationally active:

- Continued Telegram activity claiming new infrastructure compromises in Western countries
- **OT/ICS threat has not diminished** despite DOJ indictments and CISA advisories — indictees remain free in Iran
- CISA 2026 ICS threat brief ranks Iran as the **#2 OT/ICS threat actor** behind China, above Russia for intentional disruption likelihood
- **Water sector still vulnerable**: EPA's 2025 audit found ~30% of large US water utilities still have inadequate cybersecurity controls
- **Unitronics CVE-2023-6448**: Patch adoption estimated at 60–70%; significant unpatched attack surface remains
- Iranian escalation posture elevated following continued Israel-Hezbollah tensions and US policy shifts — CyberAv3ngers operations historically spike during geopolitical escalation windows

**Analyst assessment:** CyberAv3ngers represents one of the few nation-state actors with **confirmed physical-layer access** to US critical infrastructure. The Aliquippa case was not the limit of what they accessed — it was the limit of what was publicly confirmed. Treat any internet-exposed Unitronics, Orpak, or Israeli-made OT equipment as a potential active target.

---

*Profile created: 2026-05-10 | Analyst: C3PO | Admiralty Grade: A1*
