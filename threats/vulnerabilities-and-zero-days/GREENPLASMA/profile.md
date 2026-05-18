# GreenPlasma — Windows CTFMON Arbitrary Section Creation Elevation of Privilege

## Identity

| Field | Details |
|---|---|
| **Vulnerability Name** | GreenPlasma |
| **CVE** | None assigned — **UNPATCHED** |
| **Type** | Elevation of Privilege (EoP) — Partial PoC |
| **Class** | CTFMON Arbitrary Section Object Creation / SYSTEM-Writable Directory Abuse |
| **Affected Platforms** | Windows 11 · Windows Server 2022 · Windows Server 2025 |
| **Patch Status** | 🔴 **UNPATCHED** — No CVE, no Microsoft acknowledgment, no timeline |
| **PoC Status** | 🟡 **PARTIAL PUBLIC** — PoC released but incomplete; lacks final SYSTEM shell component |
| **Discovered By** | Chaotic Eclipse / Nightmare-Eclipse (pseudonymous researcher) |
| **Public Disclosure** | May 12, 2026 (published simultaneously with YellowKey, on Patch Tuesday) |
| **Exploited in Wild** | ❌ Not confirmed |
| **Threat Level** | 🟡 MEDIUM (standalone, partial PoC) → 🔴 HIGH (if completed; fits Nightmare-Eclipse chaining pattern) |
| **Admiralty Grade** | A3 — Researcher-confirmed mechanism; partial PoC only; no independent full exploitation confirmation |
| **ATT&CK** | T1068 (Exploitation for Privilege Escalation) · T1134 (Access Token Manipulation) |

---

## Overview

GreenPlasma is an unpatched Windows elevation of privilege vulnerability targeting **CTFMON (ctfmon.exe)**, the Collaborative Translation Framework monitor that runs as SYSTEM in every interactive Windows session. By abusing the Cloud Files API policy registry keys and SYSTEM-writable named object directories, an unprivileged user can create arbitrary memory section objects in directory locations that are normally only writable by SYSTEM — potentially allowing manipulation of privileged services, kernel-mode drivers, and other components that trust those object paths.

Chaotic Eclipse published GreenPlasma alongside YellowKey on May 12, 2026 (Patch Tuesday), continuing a pattern of paired and escalating disclosures. The PoC is **intentionally incomplete** — Chaotic Eclipse released the section-creation primitive but withheld the component needed to elevate to a full SYSTEM shell, describing it as a "challenge" to other researchers: *"if you're smart enough, you can turn this into a full privilege escalation."*

The partial release is consistent with the researcher's pattern: BlueHammer was fully released; RedSun was fully released; GreenPlasma is partially released. This may indicate Chaotic Eclipse is holding the completion for a future disclosure or as continued pressure on Microsoft.

---

## Technical Details

### Root Cause

CTFMON.exe runs as SYSTEM in every interactive session and manages named objects under:

```
\Sessions\<id>\BaseNamedObjects\
```

Notably `CTF.AsmListCache.FMPWinlogon` and similar objects reside in the session's `BaseNamedObjects` directory — a location SYSTEM can write to.

GreenPlasma exploits a flaw in how CTFMON initializes these section objects by using three chained techniques:

**Step 1 — Cloud Files Registry Key Abuse:**
```
HKCU\Software\Policies\Microsoft\CloudFiles
```
The exploit creates this key with `REG_OPTION_CREATE_LINK` (volatile) + a `SymbolicLinkValue` pointing to `Policies\System`. This creates a registry symbolic link that redirects Cloud Files policy lookups into a SYSTEM-controlled policy path.

**Step 2 — DACL Manipulation:**
Uses `SetEntriesInAcl` + `TreeSetNamedSecurityInfo` to grant **Everyone: GENERIC_ALL** on the redirected key, then resets the DACL — temporarily giving a low-privileged user write access to paths they normally cannot touch.

**Step 3 — CTFMON Re-initialization via CfAbortOperation:**
Calls `CfAbortOperation` (from `cldapi.dll`) repeatedly to force CTFMON to re-initialize its section object creation. When CTFMON re-creates `CTF.AsmListCache.FMPWinlogon`, it now lands in a location the low-privileged attacker has influenced via the registry link and DACL manipulation.

**Result:** An unprivileged user controls the memory section object that CTFMON (running as SYSTEM) will use — enabling potential manipulation of any privileged service or driver that trusts that object path.

### What the PoC Actually Does

The released PoC creates the arbitrary section object in a SYSTEM-writable directory. It does **not** complete the privilege escalation to a SYSTEM shell — the final step (leveraging the corrupted object to hijack execution of a privileged process or driver) was deliberately withheld by the researcher.

### Relationship to Nightmare-Eclipse's Prior Work

GreenPlasma reuses `cldapi.dll` (the Cloud Files API library) as a primitive — the same library exploited in both BlueHammer and RedSun. This is a deliberate pattern: the researcher has identified `cldapi.dll` as a durable attack surface and continues to find new exploitation primitives within it. The registry symbolic link technique is also novel and not previously seen in the Nightmare-Eclipse toolkit.

---

## Affected Versions

| Platform | Affected |
|---|---|
| Windows 11 (all editions) | ✅ YES |
| Windows Server 2022 | ✅ YES |
| Windows Server 2025 | ✅ YES |
| Windows 10 | Unconfirmed (CTFMON present but session object structure differs) |
| Windows Server 2019 and older | Unconfirmed |

---

## Context: The Nightmare-Eclipse Toolkit (5 Tools)

| Tool | Disclosed | Type | PoC | Status |
|---|---|---|---|---|
| BlueHammer | April 3, 2026 | LPE → SYSTEM (file read) | Full | ✅ Patched — CVE-2026-33825 |
| RedSun | April 16, 2026 | LPE → SYSTEM (file write) | Full | 🔴 Unpatched — Day 32 |
| UnDefend | April 16, 2026 | Defender DoS/Blind | Full | 🔴 Unpatched — Day 32 |
| YellowKey | May 12, 2026 | BitLocker Bypass | Full | 🔴 Unpatched — Day 6 |
| GreenPlasma | May 12, 2026 | EoP (CTFMON Section) | **Partial** | 🔴 Unpatched — Day 6 |
| **MiniPlasma** | **May 14, 2026** | **LPE → SYSTEM (`cldflt.sys`, CVE-2020-17103 patch ineffective)** | **Full** | **🔴 Unpatched — Day 4** |

The researcher's blog post explicitly threatened: *"Next patch tuesday will have a big surprise for you Microsoft."* — suggesting more disclosures are incoming.

**Operational chaining risk:** GreenPlasma, if completed, would provide an additional path to SYSTEM that doesn't touch Defender at all — bypassing detections tuned for BlueHammer/RedSun behavioral patterns. An attacker completing the GreenPlasma chain would have a clean, detection-evasive SYSTEM escalation route on any Windows 11/Server 2022/2025 endpoint.

---

## Risk Assessment

**Current state (partial PoC):**
The released PoC is not immediately exploitable to SYSTEM. A technically sophisticated attacker could potentially complete the chain — the section creation primitive is the hard part; the final weaponization is a known class of technique (section object hijacking). The time-to-full-exploitation by a capable researcher is uncertain but likely days to weeks.

**Future state (if completed PoC released):**
GreenPlasma becomes a direct complement to RedSun — a second SYSTEM escalation path operating on a completely different code path (CTFMON vs. Defender/TieringEngineService), making behavioral blocking significantly harder.

---

## Mitigation & Remediation

> ⚠️ No official patch. No CVE. All controls are compensating measures targeting the known partial PoC behavior.

| Action | Priority | Notes |
|---|---|---|
| **Monitor for `CfAbortOperation` calls from non-system processes** | 🔴 HIGH | `cldapi.dll` invocation outside of known cloud sync software (OneDrive, Dropbox) should be flagged — consistent with GreenPlasma and prior Nightmare-Eclipse tools |
| **Alert on `REG_OPTION_CREATE_LINK` key creation under `HKCU\Software\Policies\Microsoft\CloudFiles`** | 🔴 HIGH | Direct IOC for GreenPlasma step 1 |
| **Alert on `TreeSetNamedSecurityInfo` granting Everyone:GENERIC_ALL on policy keys** | 🔴 HIGH | DACL manipulation step — high-fidelity indicator |
| **Monitor CTFMON object creation in `BaseNamedObjects`** | 🟠 MEDIUM | Unexpected section object creation timing relative to CTFMON re-init is an indicator |
| **Restrict Cloud Files API access where OneDrive Files On-Demand is not needed** | 🟠 MEDIUM | Same mitigation as RedSun — disable CldFlt mini-filter driver on systems that don't require it |
| **Apply all available Nightmare-Eclipse detections** | 🔴 HIGH | Defender signatures for BlueHammer/RedSun behavioral patterns; tune EDR for cldapi.dll abuse |
| **Monitor MSRC for emergency advisory** | 🟠 HIGH | Watch for CVE assignment — GreenPlasma PoC completion would likely accelerate Microsoft's response |

---

## Detection

Hunt queries and behavioral indicators:

- **Process:** `ctfmon.exe` performing unexpected section object creation or receiving signals triggering re-initialization in short succession
- **Registry:** Creation of `HKCU\Software\Policies\Microsoft\CloudFiles` with `REG_OPTION_CREATE_LINK` attribute
- **API:** `CfAbortOperation` calls from processes other than known sync clients
- **DACL:** `TreeSetNamedSecurityInfo` calls granting broad permissions to policy-adjacent registry keys from low-privileged user context
- **Network:** Any of the above from a user session shortly before or after other Nightmare-Eclipse IOCs (RedSun/UnDefend patterns in the same session)

---

## References

- [GitHub PoC — Nightmare-Eclipse/GreenPlasma](https://github.com/Nightmare-Eclipse/GreenPlasma)
- [BleepingComputer — Windows BitLocker zero-day + GreenPlasma disclosure](https://www.bleepingcomputer.com/news/security/windows-bitlocker-zero-day-gives-access-to-protected-drives-poc-released/)
- [BornCity (German) — Technical breakdown of GreenPlasma CTFMON mechanism](https://borncity.com/blog/2026/05/13/chaotic-eclipse-zwei-0-day-windows-schwachstellen-yellowkey-greenplasma/)
- [Chaotic Eclipse Blog — Two more public disclosures](https://deadeclipse666.blogspot.com/2026/05/two-more-public-disclosures-it-will.html)
- [TrendShift — Nightmare-Eclipse/GreenPlasma GitHub trending](https://trendshift.io/repositories/27891)

---

## Update Log

| Date | Update |
|---|---|
| 2026-05-13 | Profile created. Day 1. Partial PoC public. No CVE, no patch, no Microsoft acknowledgment. Full SYSTEM shell component withheld by researcher. |
| 2026-05-18 | Day 6. No patch. Researcher released MiniPlasma (CVE-2020-17103, full PoC, SYSTEM shell — sixth tool in the series). Series table updated. |
