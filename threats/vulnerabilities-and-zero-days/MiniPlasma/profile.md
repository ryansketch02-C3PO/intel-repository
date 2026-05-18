# MiniPlasma — Windows cldflt.sys LPE (Incomplete Patch / Regression)

## Identity

| Field | Details |
|---|---|
| **CVE** | None assigned (new) — relates to **CVE-2020-17103** (reportedly patched Dec 2020; assessed incomplete fix or regression) |
| **Nickname** | MiniPlasma |
| **ZD ID** | ZD-040 |
| **Vendor Advisory** | None — Microsoft has not issued a new advisory or CVE assignment as of 2026-05-18 |
| **Type** | Local Privilege Escalation (LPE) — Race Condition → SYSTEM via Cloud Files Mini Filter Driver |
| **Class** | CWE-362 — Race Condition |
| **Affected Product** | Windows 10 · Windows 11 (all versions through May 2026 Patch Tuesday) · Windows Server (all supported — assessed) |
| **Affected Feature** | `cldflt.sys` — Windows Cloud Files Mini Filter Driver; `HsmOsBlockPlaceholderAccess` routine; `CfAbortHydration` API |
| **Not Affected** | Windows 11 Insider Preview Canary (as of 2026-05-17; likely contains unreleased fix) |
| **Patch Status** | 🔴 **UNPATCHED** — Works on fully patched Windows 11 (KB5089549, OS builds 26100.8457 / 26200.8457); Microsoft has not assigned a new CVE or issued guidance as of 2026-05-18 |
| **CVSS** | Not yet assigned |
| **Exploit Maturity** | 🔴 **PUBLIC PoC** — Source code + compiled binary on GitHub; confirmed by BleepingComputer and Will Dormann (Tharros) |
| **CISA KEV** | ❌ Not listed — no CVE assigned; monitor |
| **Attack Requirements** | **Local** — requires existing code execution on target before use |
| **Disclosed** | May 17, 2026 |
| **Disclosed By** | Chaotic Eclipse (GitHub: Nightmare-Eclipse) — 6th tool in protest campaign against Microsoft MSRC |
| **Threat Level** | 🟠 HIGH — Public PoC; confirmed working on fully patched Windows 11 (May 2026); local-only; predecessor Chaotic Eclipse tools already weaponized by Russia-geolocated actors |
| **Admiralty Grade** | B2 — PoC confirmed by independent researchers; Microsoft has not confirmed |
| **ATT&CK** | T1068 (Exploitation for Privilege Escalation) |

---

## Overview

MiniPlasma is the **sixth exploit tool** published by pseudonymous researcher **Chaotic Eclipse** (GitHub: Nightmare-Eclipse) in their ongoing protest campaign against Microsoft's Security Response Center (MSRC). Published May 17, 2026, it is a Windows local privilege escalation exploit targeting `cldflt.sys` (Cloud Files Mini Filter Driver) that achieves **SYSTEM** on fully patched Windows 11 via a race condition in `HsmOsBlockPlaceholderAccess`.

The researcher claims this is a **regression or incomplete fix** of CVE-2020-17103 (originally reported by Google Project Zero's James Forshaw in September 2020, reportedly patched December 2020):

> *"The exact same issue that was reported to Microsoft by Google Project Zero is actually still present, unpatched. I'm unsure if Microsoft just never patched the issue or the patch was silently rolled back. The original PoC by Google worked without any changes."*

**BleepingComputer independently confirmed** SYSTEM shell on fully patched Windows 11 Pro (May 2026 Patch Tuesday). Will Dormann (Tharros) confirmed independently. The exploit does **not** work on Windows 11 Insider Preview Canary — suggesting an unreleased fix may exist. Microsoft has issued no new CVE or advisory.

---

## Technical Analysis

### Affected Component

`cldflt.sys` is the Windows Cloud Files Mini Filter Driver, responsible for OneDrive-style cloud file transparency (placeholder files, hydration/dehydration). `HsmOsBlockPlaceholderAccess` handles access control during placeholder operations.

### Exploitation

The exploit leverages a **TOCTOU race condition** during registry key creation via the undocumented `CfAbortHydration` API:

1. Attacker creates cloud placeholder file and triggers specific cloud filter activity
2. A window exists in `HsmOsBlockPlaceholderAccess` during which the driver creates registry keys in `.DEFAULT` hive without proper access control validation
3. Winning the race allows planting **arbitrary registry keys in `HKU\.DEFAULT`**
4. These keys enable executing attacker-controlled code in a privileged context → SYSTEM

**Reliability:** Race condition; success rate varies by system load. Researcher notes "works reliably in my machines." Dormann confirmed reliable SYSTEM shell on Windows 11.

**No patch applies:** Works on systems running KB5089549 (May 12, 2026 cumulative update).

---

## Context: Chaotic Eclipse Disclosure Series

| # | Tool | Disclosed | CVE | Status |
|---|---|---|---|---|
| 1 | BlueHammer | Apr 3, 2026 | CVE-2026-33825 | ✅ Patched |
| 2 | RedSun | Apr 16, 2026 | None | 🔴 Unpatched — Day 32 |
| 3 | UnDefend | Apr 16, 2026 | None | 🔴 Unpatched — Day 32 |
| 4 | YellowKey | May 12, 2026 | None | 🔴 Unpatched — Day 6 |
| 5 | GreenPlasma | May 12, 2026 | None | 🔴 Unpatched — Day 6 |
| **6** | **MiniPlasma** | **May 17, 2026** | **CVE-2020-17103 regression** | **🔴 Unpatched** |

Researcher has threatened a **"big surprise"** for next Patch Tuesday (~June 9, 2026). MiniPlasma predates that by ~23 days — may be a separate entry or a preview.

---

## Downstream Risk

While local-only, LPE to SYSTEM is the standard post-exploitation step in nearly every Windows intrusion chain. Prior Nightmare-Eclipse tools were weaponized by third-party actors (geolocated to Russia by Lyrie.ai) within 24–48 hours of release. MiniPlasma will follow the same trajectory.

**Confirmed kill chain using predecessor tools:**
1. Initial access (compromised SSLVPN/FortiGate creds)
2. UnDefend deployed — blocks Defender, falsifies telemetry
3. RedSun/BlueHammer → SYSTEM
4. Credential dump, lateral movement

MiniPlasma is a direct substitute for step 3.

---

## Detection

- Unusual `cldflt.sys` activity / Cloud Filter kernel callbacks from non-OneDrive processes
- `Event 4688`: SYSTEM-token process spawned from Medium/Low integrity parent with no auth event
- Registry write events in `HKU\.DEFAULT` from unexpected callers (Sysmon Event 13)
- `CfAbortHydration` calls from non-Microsoft processes

---

## Disclosure Timeline

| Date | Event |
|---|---|
| Sep 2020 | James Forshaw (Google Project Zero) reports `cldflt.sys` LPE |
| Dec 2020 | Microsoft patches as CVE-2020-17103 (KB5001649) |
| May 17, 2026 | Chaotic Eclipse publishes MiniPlasma PoC; claims original GPZ PoC works unchanged |
| May 17, 2026 | BleepingComputer + Will Dormann confirm SYSTEM shell on fully patched Win11 |
| May 18, 2026 | No Microsoft CVE or advisory; Profile created (ZD-040) |
| ~Jun 9, 2026 | Next Patch Tuesday — monitor for patch/CVE |

---

## References

- [BleepingComputer — New Windows 'MiniPlasma' Zero-Day Gives SYSTEM Access, PoC Released](https://www.bleepingcomputer.com/news/microsoft/new-windows-miniplasma-zero-day-exploit-gives-system-access-poc-released/)
- [The Hacker News — MiniPlasma Windows 0-Day Enables SYSTEM Privilege Escalation](https://thehackernews.com/2026/05/miniplasma-windows-0-day-enables-system.html)
- [WindowsForum — MiniPlasma PoC Questions KB5089549 Fix for CVE-2020-17103](https://windowsforum.com/threads/miniplasma-poc-questions-kb5089549-fix-for-cve-2020-17103-on-win11.418703/)
- [GitHub — Nightmare-Eclipse / MiniPlasma PoC](https://github.com/Nightmare-Eclipse/)

---

*Profile created: 2026-05-18 | Author: C3PO | Admiralty Grade: B2 | TLP: WHITE*
