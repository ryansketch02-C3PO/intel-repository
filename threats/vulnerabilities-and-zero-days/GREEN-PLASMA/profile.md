# Green Plasma — Windows Local Privilege Escalation Zero-Day (Unpatched)

## Identity

| Field | Details |
|---|---|
| **Alias** | Green Plasma |
| **CVE** | **None — CVE not yet assigned** |
| **Vendor Advisory** | None — uncoordinated public disclosure |
| **Type** | Local Privilege Escalation (LPE) |
| **Class** | Improper access control via kernel memory section object creation |
| **Affected Product** | Microsoft Windows (all supported versions with Windows services + kernel-mode drivers) |
| **Patch Status** | 🔴 **UNPATCHED** — No patch available as of 2026-05-14; Microsoft has not issued a public response |
| **CVSS** | Not yet assigned; operational severity assessed **HIGH** given PoC availability and "Exploitation More Likely" analogue |
| **EPSS** | Not yet scored |
| **Exploit Maturity** | 🔴 Public PoC — published May 13, 2026 by researcher "Chaotic Eclipse" |
| **Microsoft Rating** | "**Exploitation More Likely**" (based on researcher assessment; official Microsoft advisory pending) |
| **Disclosed** | 2026-05-13 |
| **Discovered By** | "Chaotic Eclipse" (same researcher who disclosed Yellow Key BitLocker bypass on same day) |
| **Threat Level** | 🔴 HIGH — Unpatched, public PoC, "Exploitation More Likely"; researcher has threatened additional zero-day disclosures on future Patch Tuesdays |
| **Admiralty Grade** | B2 — Researcher-confirmed public PoC; Microsoft response pending |
| **ATT&CK** | T1068 (Exploitation for Privilege Escalation) |

---

## Overview

**Green Plasma** is an unpatched Windows local privilege escalation zero-day disclosed on **May 13, 2026** by security researcher "Chaotic Eclipse." The vulnerability allows an attacker with standard user access to escalate privileges to **SYSTEM level** by exploiting a flaw in how Windows handles memory section object creation in directories writable by normal users.

The technique involves manipulating Windows services — including kernel-mode drivers — through **arbitrary memory section object creation** in writable directories. By placing a specially crafted section object in a user-writable location that a privileged service later opens, an attacker can redirect the service into executing attacker-controlled code at SYSTEM privilege level.

While the researcher published only a **limited proof of concept** (stripping full SYSTEM shell functionality from the public release), security researchers have warned that the released code provides sufficient foundation for adversaries to develop a weaponized exploit. Full exploitation enables:

- Disabling endpoint protection
- Manipulating trusted processes
- Deploying malware with SYSTEM privileges
- Using the compromised host as a pivot point for lateral movement across enterprise networks

> "Full exploitation could enable attackers to disable endpoint protections, manipulate trusted processes, deploy malware with system privileges, or use compromised machines as pivot points for lateral movement across enterprise networks." — Security researchers reacting to PoC release

---

## Technical Analysis

### Root Cause

The vulnerability exploits a flaw in Windows' handling of **kernel-mode driver memory section objects** — specifically, the ability for a low-privileged user to create section objects in directories that privileged services later access. Windows NT object namespace allows low-privileged processes to create named objects in certain writable areas of the object namespace. When a privileged service opens a section object by name from a user-writable path without adequate validation, the attacker's malicious section object is loaded instead of the legitimate one.

This is related to the "phantom DLL" and object squatting class of vulnerabilities, but applied specifically to kernel memory sections rather than library files.

### Exploit Chain

1. Attacker (standard user) identifies a kernel-mode driver or privileged service that opens a named section object
2. The section object's lookup path includes a directory writable by standard users
3. Attacker creates a malicious section object at that path before the service starts/reloads
4. Privileged service opens the attacker's object, executing attacker-controlled code at SYSTEM
5. Attacker gains SYSTEM-level shell or persistent SYSTEM service

### PoC Scope

The public PoC deliberately strips the final SYSTEM shell step, providing the object squatting mechanism but not a clean SYSTEM execution primitive. Security researchers assess the gap between public PoC and weaponized exploit is **days to weeks**, not months.

### Chaining Potential

Green Plasma is highly dangerous when chained with:
- **CVE-2026-4361 (ZD-024)** — Outlook zero-click RCE → Green Plasma LPE = email-delivered SYSTEM compromise
- **Any phishing/social engineering initial access** → Green Plasma LPE = immediate privilege escalation to SYSTEM from any user-level beachhead

---

## Affected Systems

| Platform | Affected |
|---|---|
| Windows 11 (all builds) | ✅ Affected |
| Windows 10 (all builds) | ✅ Affected |
| Windows Server 2025 | ✅ Affected |
| Windows Server 2022 | ✅ Affected |
| Windows Server 2019/2016 | ✅ Likely affected (same driver model) |
| Earlier EOL Windows | Unknown — likely affected but unsupported |

---

## Mitigation (No Patch Available)

No official Microsoft patch exists. Interim mitigations are limited:

| Priority | Action |
|---|---|
| 🔴 HIGH | Monitor for **Green Plasma PoC indicators**: unusual section object creation in user-writable paths prior to service operations |
| 🔴 HIGH | **EDR behavioral rules**: alert on SYSTEM process spawning from user-context parents via unusual service manipulation |
| 🟠 MEDIUM | **Least-privilege enforcement**: minimize standard user write access to system directories; audit writable paths in the NT object namespace |
| 🟠 MEDIUM | **Kernel integrity policies**: WDAC (Windows Defender Application Control) and HVCI (Hypervisor-Protected Code Integrity) add meaningful barriers to this class of attack |
| 🟡 MEDIUM | Monitor for the researcher "Chaotic Eclipse" releasing updated PoC code with full SYSTEM execution |
| 🟡 LOW | Track Microsoft Security Update Guide for out-of-band patch; subscribe to MSRC alerts |

---

## Threat Actor Context

The researcher "Chaotic Eclipse" **has threatened to release additional zero-day exploits on future Patch Tuesdays**, indicating a pattern of adversarial disclosures timed to maximize exposure windows. This makes Green Plasma part of an ongoing disclosure campaign, not a one-time event.

The same researcher simultaneously disclosed **Yellow Key** — a BitLocker bypass requiring physical access — on the same date (May 13, 2026), suggesting access to a stockpile of pre-discovered vulnerabilities.

**Nation-state actors and sophisticated ransomware groups** routinely weaponize publicly available LPE PoCs within days of publication. Green Plasma should be treated as an **actively weaponizable threat** as of ~May 16–20, 2026.

---

## References

- Cyber Threat Brief May 14, 2026 (YouTube transcript — Bleeping Computer, SecurityWeek sourced)
- pwnhackers.substack.com — Green Plasma / Yellow Key disclosure coverage (May 14, 2026)
- Researcher "Chaotic Eclipse" — public PoC release (May 13, 2026)

---

*Profile created: 2026-05-14 | Author: C3PO | ZD-026 | Admiralty Grade: B2 | TLP: WHITE*
