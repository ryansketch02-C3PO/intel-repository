# Operation DreamJob — UAV/Aerospace Espionage Campaign (2025 Wave)

> C3PO Threat Actor Repository | Campaign File  
> Status: 🔴 ACTIVE (ongoing; new waves expected) | Last Updated: 2026-04-17

---

## Campaign Summary

| Field | Detail |
|---|---|
| **Campaign Name** | Operation DreamJob — UAV/Aerospace Wave (2025) |
| **Threat Actor** | Lazarus Group (HIDDEN COBRA) — see Scoundrel #003 |
| **Sponsor** | North Korea (DPRK) — Reconnaissance General Bureau |
| **Campaign Start** | 2020 (Operation DreamJob origin); this wave: March 2025 |
| **Status** | 🔴 ACTIVE — ESET assessed further attacks "highly likely" against UAV sector |
| **Primary Objective** | Steal UAV/drone design data, manufacturing know-how, proprietary weapons specifications |
| **Sectors Targeted** | Aerospace, Defense, Engineering (UAV-specific) |
| **Geographic Focus** | Central and Southeastern Europe; Italy, Spain (confirmed submissions); previously India, Poland, UK |
| **Admiralty Grade** | B1 — ESET Research (credible commercial), multiple independent corroborations |
| **TLP** | TLP:CLEAR |

---

## Strategic Context

North Korea's drone program has accelerated dramatically. DPRK drones like the **Saetbyol-4** and **Saetbyol-9** are assessed by Western analysts to be reverse-engineered copies of US military UAVs (RQ-4 Global Hawk, MQ-9 Reaper). North Korea is actively building a factory for mass UAV production.

In late 2024 and 2025, North Korean troops were deployed to Russia to assist with operations in Ukraine — where they encountered Western-made drones on the battlefield. Operation DreamJob's 2025 wave appears designed to close the gap between what DPRK observed on the battlefield and what they can manufacture: **steal the blueprints and the factory know-how**.

ESET confirmed that at least one targeted company produces UAV models currently deployed in Ukraine, and another is involved in the supply chain for advanced single-rotor drones that North Korea is actively developing.

---

## Attack Chain

```
1. Fake recruiter profile (LinkedIn or email)
        │
        ▼
2. Spearphishing with "dream job" lure
   (prestigious position at well-known defense/aerospace firm)
        │
        ▼
3. Delivery of decoy document + trojanized "PDF reader"
   (legitimate-looking job description; malicious reader = malware loader)
        │
        ▼
4. DLL side-loading via trojanized open-source app
   (MuPDF, TightVNC Viewer, Notepad++ plugin, DirectX wrapper)
        │
        ▼
5. Early-stage loaders decrypt AES-128/ChaCha20 payload in memory
   (BinMergeLoader, QuanPinLoader, libpcre loader — never unencrypted on disk)
        │
        ▼
6. ScoringMathTea RAT deployed
   (full C2; ~40 commands; file/process manipulation, data exfil, command execution)
        │
        ▼
7. Proprietary UAV data, manufacturing specs, intellectual property exfiltrated
```

---

## TTPs (MITRE ATT&CK)

| Tactic | Technique | ID | Notes |
|---|---|---|---|
| Initial Access | Spearphishing via Service | T1566.003 | LinkedIn and email fake job offers from spoofed defense company recruiters |
| Execution | User Execution: Malicious File | T1204.002 | Victim executes trojanized PDF reader / installer |
| Execution | Reflective Code Loading | T1620 | Payloads loaded in memory; never appear unencrypted on disk |
| Persistence | DLL Side-Loading | T1574.002 | Core Lazarus/DreamJob technique; uses trojanized legitimate apps as host |
| Defense Evasion | DLL Side-Loading | T1574.002 | Legitimate executables load malicious DLLs |
| Defense Evasion | Obfuscated Files | T1027 | AES-128/ChaCha20 encrypted payloads; decrypted only at runtime in memory |
| Defense Evasion | Masquerading | T1036 | Dropper filename: `DroneEXEHijackingLoader.dll` (internally named) |
| Collection | Data from Local System | T1005 | Targeting UAV design files, manufacturing specs, intellectual property |
| Command & Control | Web Protocols | T1071.001 | ScoringMathTea uses TCP connections to C2 for command/control |
| Exfiltration | Exfiltration Over C2 Channel | T1041 | Data exfiltrated via ScoringMathTea RAT C2 |
| Process Injection | Process Injection | T1055 | Used in payload chain for execution |

---

## Malware & Toolset

### ScoringMathTea RAT
- **Type:** Remote Access Trojan (C3PO flagship payload designation)
- **First seen:** October 2022 (uploaded from Portugal/Germany; Airbus-themed lure)
- **Capabilities:** ~40 commands including:
  - File and process manipulation
  - System information collection
  - Configuration modification
  - TCP connection establishment
  - Local command execution
  - New payload download and execution from C2
- **Encryption:** Traffic encrypted; continuous minor updates/bug fixes
- **Related payloads:** LightlessCan, ImprudentCook, BlindingCan, miniBlindingCan, SimplexTea (Linux)

### Loaders (2025 Wave — New Additions)
| Loader | Mechanism | Notes |
|---|---|---|
| `DroneEXEHijackingLoader.dll` | DLL proxy | Exploits legitimate executable; key indicator of UAV targeting |
| BinMergeLoader | DLL side-load | Mirrors Mandiant's MISTPEN; abuses Microsoft Graph API tokens |
| QuanPinLoader | DLL side-load | New in 2025 wave |
| libpcre loader | DLL proxy | New in 2025 wave |
| DirectInput `dinput.dll` | DLL side-load | DirectX wrapper technique |

### Trojanized Open-Source Applications Used
- **MuPDF** — PDF viewer (2025 wave)
- **TightVNC Viewer** — Remote desktop viewer (2025 wave)
- **Notepad++ plugins** — (2025 wave)
- **PDF readers** — Generic (used as initial lure delivery)

---

## Confirmed Victim Profile

| Date | Target | Country | Sector |
|---|---|---|---|
| Jan 2023 | Indian technology company | India | Technology |
| Mar 2023 | Polish defense firm | Poland | Defense |
| Oct 2023 | British industrial automation company | UK | Industrial/Defense |
| Sep 2025 | Italian aerospace company | Italy | Aerospace |
| Mar–Sep 2025 | Metal engineering company | Central/SE Europe | Defense manufacturing |
| Mar–Sep 2025 | Aircraft components manufacturer | Central/SE Europe | Aerospace |
| Mar–Sep 2025 | Defense contractor (UAV-involved) | Central/SE Europe | Defense/UAV |

---

## Geopolitical Intelligence Value

**Why UAVs specifically:**
- North Korea's Saetbyol UAV series closely mirrors US designs — reverse engineering is their established method
- NK troops in Russia encountered Western drones on Ukrainian battlefields firsthand
- NK is building mass UAV production capability — manufacturing know-how is as valuable as design specs
- Operation DreamJob has been running since 2020; UAV focus intensified in 2023–2025 as the battlefield intelligence value became clear

**What they're stealing:**
- UAV design specifications and blueprints
- Manufacturing processes and industrial know-how
- Component supply chain details
- Software/firmware for UAV guidance systems

---

## Analyst Assessment

**Confidence:** HIGH (B1 — ESET Research, multiple independent sources; no government advisory specific to this wave)

This campaign is directly relevant to the aerospace focus of this repository. Three companies in the European defense/UAV supply chain were compromised in the 2025 wave. ESET explicitly assessed that further attacks against the UAV sector are **likely** given North Korea's stated industrial goals.

The attack chain is notably consistent across years — Lazarus maintains the same core methodology (fake job → trojanized reader → DLL sideload → ScoringMathTea) while updating the specific loaders and trojanized applications for each wave. This predictability makes it detectable but — as ESET noted — employee awareness in defense/aerospace sectors remains insufficient.

**Assessment (WEP):** Operation DreamJob activity against aerospace/UAV targets is **almost certainly** continuing. Organizations in the UAV supply chain, drone component manufacturing, and defense tech sectors in Europe and beyond are **likely** to be targeted in future waves.

---

## Connections to Repository

- 🔗 **Scoundrel #003 — Lazarus Group** — parent actor for this campaign
- 🔗 **Scoundrel #002 — Stardust Chollima** — related DPRK actor; different financial focus but same state apparatus

---

## Defensive Recommendations

1. **Train defense/aerospace employees** specifically on recruitment-based spearphishing — "dream job" lures remain effective because awareness is low
2. **Block LinkedIn-delivered file downloads** from unverified recruiter accounts
3. **Application allowlisting** — prevent execution of unknown DLL loaders
4. **Hunt for `DroneEXEHijackingLoader.dll`** and BinMergeLoader signatures in endpoint telemetry
5. **Monitor Microsoft Graph API token abuse** — BinMergeLoader abuses Graph tokens
6. **Ingest ESET IOC list** (SHA-1 hashes, C2 domains, IPs from ESET DreamJob GitHub repo)
7. **Assume targeting** if your organization is involved in UAV design, components, or supply chain

---

*Campaign file created: 2026-04-17 | Author: C3PO*  
*Sources: ESET Research "Gotta fly: Lazarus targets the UAV sector" (Oct 23, 2025), HelpNet Security, CSO Online, Security Affairs, Infosecurity Magazine*
