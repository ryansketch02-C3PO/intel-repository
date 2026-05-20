# Firefox 151 / Thunderbird 140.11 — May 2026 Security Batch

## Identity

| Field | Details |
|---|---|
| **CVE (Primary — CVSS 9.8)** | CVE-2026-8956 · CVE-2026-8974 |
| **Other Notable CVEs** | CVE-2026-8953 · CVE-2026-8958 · CVE-2026-8959 · CVE-2026-8945 + 25+ additional |
| **CIS Advisory** | MS-ISAC Advisory 2026-052 (May 19, 2026) |
| **Mozilla Advisory** | MFSA 2026-46 |
| **Type** | Integer Overflow RCE · Memory Safety Bugs · Multiple Sandbox Escapes · Privilege Escalation |
| **Affected Products** | Firefox < 151 · Firefox ESR < 140.11 · Firefox ESR < 115.36 · Firefox for iOS < 151.0 · Thunderbird < 151 · Thunderbird ESR < 140.11 |
| **Patch Status** | ✅ **PATCHED** — Firefox 151 / Firefox ESR 140.11 / Firefox ESR 115.36 / Thunderbird 151 / Thunderbird ESR 140.11 |
| **CVSS (highest)** | **9.8 CRITICAL** (CVE-2026-8956 — Integer Overflow, Networking JAR) |
| **CVSS (second)** | **9.8 CRITICAL** (CVE-2026-8974 — Memory safety bugs, Firefox ESR 140.10 + Firefox 150) |
| **Exploit Maturity** | 🟢 No reports of in-the-wild exploitation (CIS Advisory: *"There are currently no reports of these vulnerabilities being exploited in the wild"*) |
| **CISA KEV** | ❌ Not listed |
| **Disclosed** | 2026-05-19 |
| **Threat Level** | 🟠 HIGH — Two CVSS 9.8 critical CVEs; multiple sandbox escapes; ubiquitous browser attack surface; update should be prioritized on managed endpoints |
| **Admiralty Grade** | A2 — Mozilla-confirmed; no active exploitation |
| **ATT&CK** | T1189 (Drive-by Compromise) · T1203 (Exploitation for Client Execution) · T1068 (Exploitation for Privilege Escalation) |

---

## Overview

Mozilla released **Firefox 151** and **Thunderbird 140.11** on May 19, 2026, patching a batch of 30+ security vulnerabilities. The two highest-severity issues — both rated CVSS 9.8 Critical — are a **networking integer overflow** (CVE-2026-8956) and **memory safety bugs with evidence of memory corruption** (CVE-2026-8974). Additional notable issues include **four sandbox escape paths** and several privilege escalation and information disclosure vulnerabilities.

There are **no confirmed in-the-wild exploits** for this batch as of the date of this profile. However, the presence of two CVSS 9.8 critical vulnerabilities, multiple sandbox escapes, and Mozilla's standard "with enough effort some could be exploited to run arbitrary code" language for the memory safety bugs warrants prompt patching across managed endpoints.

**A&D relevance:** Firefox and Thunderbird are deployed across defense contractor networks, government agencies, and IC-adjacent environments. Sandbox escapes are of particular concern in classified or compartmented environments where browser isolation is a control boundary.

---

## Key CVEs

### CVE-2026-8956 — Integer Overflow in Networking: JAR Component (CVSS 9.8 CRITICAL)
- **Severity:** CVSS 9.8 Critical (AV:N/AC:L/PR:N/UI:N/S:U/C:H/I:H/A:H)
- **Type:** Integer Overflow — network-reachable, no user interaction required in the most severe configurations
- **Fixed in:** Firefox 151, Firefox ESR 140.11, Thunderbird 151, Thunderbird ESR 140.11
- **Impact:** Code execution, data integrity compromise, denial of service
- **Notes:** No authentication or interaction required per CVSS vector — highest exploitability of the batch

### CVE-2026-8974 — Memory Safety Bugs (CVSS 9.8 CRITICAL)
- **Severity:** CVSS 9.8 Critical
- **Type:** Memory corruption / memory safety bugs
- **Fixed in:** Firefox 151, Firefox ESR 140.11
- **Mozilla statement:** *"Memory safety bugs present in Firefox ESR 140.10 and Firefox 150. Some of these bugs showed evidence of memory corruption and we presume that with enough effort some of these could have been exploited to run arbitrary code."*
- **Notes:** Standard Mozilla memory safety disclosure; "evidence of memory corruption" + "with enough effort" = credible but non-trivial exploitation path

### CVE-2026-8953 — Sandbox Escape via Use-After-Free in Disability Access APIs
- **Type:** UAF → Sandbox Escape
- **Fixed in:** Firefox 151, Firefox ESR 140.11, Thunderbird 151, Thunderbird ESR 140.11
- **Notes:** Accessibility APIs have historically been a reliable sandbox escape vector (e.g., CVE-2024-9680); UAF exploitation in this component may be weaponizable

### CVE-2026-8958 — Information Disclosure + Sandbox Escape in Security: Process Sandboxing
- **Type:** Info disclosure + process sandbox escape
- **Fixed in:** Firefox 151, Firefox ESR 140.11
- **Notes:** Direct process sandboxing bypass — the most operationally dangerous sandbox escape class

### CVE-2026-8959 — Sandbox Escape via Incorrect Boundary Conditions in Widget: Win32
- **Type:** Win32-specific sandbox escape
- **Fixed in:** Firefox 151, Firefox ESR 140.11
- **Notes:** Windows-only; incorrect boundary condition in Widget component on Win32 path

### CVE-2026-8945 — Sandbox Escape in Firefox for Android
- **Type:** Protection mechanism failure → sandbox escape
- **Fixed in:** Firefox 151 (Android only)
- **Notes:** Android-specific; CVSS 7.5 High

### Additional High-Severity CVEs

| CVE | Type | Severity | Fixed In |
|---|---|---|---|
| CVE-2026-8973 | Memory safety bugs (Firefox 150 only) | CVSS 9.8 | Firefox 151 |
| CVE-2026-8946 | Incorrect boundary conditions — Audio/Video: Web Codecs | High | Firefox 151, ESR 140.11 |
| CVE-2026-8947 | Use-after-free — DOM | High | Firefox 151, ESR 140.11 |
| CVE-2026-8975 | Memory safety bugs (ESR 115.36/140.11/Firefox 151) | High | Firefox 151, ESR 140.11, ESR 115.36 |
| CVE-2026-8948 | Same-origin policy bypass — DOM | High | Firefox 151 |
| CVE-2026-8401 | Sandbox escape — Profile Backup component | High | Firefox 151 |
| CVE-2026-8952 | Privilege escalation — Application Update | Medium | Firefox 151, ESR 140.11 |

---

## Affected Versions

| Product | Vulnerable | Fixed |
|---|---|---|
| Firefox | < 151 | ✅ 151 |
| Firefox ESR | < 140.11 | ✅ 140.11 |
| Firefox ESR (legacy) | < 115.36 | ✅ 115.36 |
| Firefox for iOS | < 151.0 | ✅ 151.0 |
| Thunderbird | < 151 | ✅ 151 |
| Thunderbird ESR | < 140.11 | ✅ 140.11 |

---

## Remediation

| Priority | Action |
|---|---|
| 🔴 HIGH | Update Firefox to 151 / ESR 140.11 across all managed endpoints |
| 🔴 HIGH | Update Thunderbird to 151 / ESR 140.11 on all email client deployments |
| 🟠 HIGH | Prioritize patching in classified/high-assurance environments where sandbox boundary is a control (CVE-2026-8953/8958/8959 sandbox escapes) |
| 🟡 MEDIUM | Update Firefox ESR 115.x deployments to ESR 115.36 if on legacy support branch |

Enterprise deployments via Group Policy, SCCM, Intune, or similar MDM platforms should push this update as a priority patch within 7 days given the CVSS 9.8 ceiling. There is no confirmed exploitation to suggest immediate emergency patching is required, but the window between disclosure and weaponization historically narrows as sandbox escape techniques get published.

---

## Detection

No active exploitation indicators to provide. Post-patch:
- Verify Firefox/Thunderbird version on all endpoints (≥ 151 or ≥ ESR 140.11)
- Audit any browser management exceptions that may be delaying updates

---

## MITRE ATT&CK Mapping

| Tactic | Technique | Notes |
|---|---|---|
| Initial Access | T1189 — Drive-by Compromise | Malicious web page triggers browser vulnerability |
| Execution | T1203 — Exploitation for Client Execution | Browser-based code execution on victim host |
| Privilege Escalation | T1068 — Exploitation for Privilege Escalation | Sandbox escape → OS-level code execution |
| Persistence | T1574 — Hijack Execution Flow | Post-exploitation; not specific to this CVE |

---

## Disclosure Timeline

| Date | Event |
|---|---|
| 2026-05-19 | Mozilla Foundation Security Advisory (MFSA 2026-46) published; Firefox 151 + Thunderbird 140.11 released |
| 2026-05-19 | CIS Advisory MS-ISAC 2026-052 published; 30+ CVEs catalogued |
| 2026-05-20 | Profile created |

---

## References

- [Mozilla Foundation Security Advisory MFSA 2026-46](https://www.mozilla.org/en-US/security/advisories/mfsa2026-46/)
- [CIS Advisory 2026-052 — Multiple Vulnerabilities in Mozilla Products (May 19, 2026)](https://www.cisecurity.org/advisory/multiple-vulnerabilities-in-mozilla-products-could-allow-for-arbitrary-code-execution_2026-052)
- [Tenable — CVE-2026-8956](https://www.tenable.com/cve/CVE-2026-8956)
- [Feedly — CVE-2026-8945 (sandbox escape)](https://feedly.com/cve/CVE-2026-8945)

---

*Profile created: 2026-05-20 | Author: C3PO | Admiralty Grade: A2 | TLP: WHITE*
