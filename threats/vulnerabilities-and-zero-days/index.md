# Vulnerabilities, Malware & Zero-Days — Jedi Archives

> Maintained by C3PO | Focus: Active zero-days, critical unpatched CVEs, and malware families relevant to aerospace, defense, and critical infrastructure
> Admiralty Scale grading applied. See README.md for full methodology.

---

## 🔴 Active Zero-Days (Unpatched)

| ID | Name | Type | Platform | CVE | Patch Status | Threat Level | Date Added |
|---|---|---|---|---|---|---|---|
| ZD-002 | RedSun | LPE / Cloud Files API + NTFS Junction | Windows 10/11/Server 2019+ | None — UNPATCHED | 🔴 UNPATCHED | 🔴 HIGH | 2026-04-18 |
| ZD-003 | UnDefend | DoS / Defender Update Block | Windows 10/11/Server 2019+ | None — UNPATCHED | 🔴 UNPATCHED | 🟡 MEDIUM (🔴 HIGH chained) | 2026-04-18 |

---

## 📋 N-Day CVEs (Patched but Actively Exploited)

| ID | Name | Type | Platform | CVE | Patch Status | Threat Level | Date Added |
|---|---|---|---|---|---|---|---|
| ZD-001 | BlueHammer | LPE / Race Condition | Windows 10/11/Server | CVE-2026-33825 | ✅ PATCHED — April 2026 Patch Tuesday | 🔴 HIGH | 2026-04-14 |
| ZD-004 | MCPwn | Auth Bypass / Management Plane Takeover | Nginx UI (nginx-ui) | CVE-2026-33032 + CVE-2026-27944 | ✅ PATCHED — v2.3.6 | 🔴 HIGH | 2026-04-27 |
| ZD-005 | CVE-2026-3868 | Buffer Overflow DoS / ICS Management Interface | Moxa Secure Routers (TN-4900, EDR, OnCell, EDF-G1002-BP) | CVE-2026-3868 + CVE-2026-3867 | ✅ PATCHED — Firmware v3.24 | 🟡 MEDIUM (🔴 HIGH in ICS/OT) | 2026-04-27 |
| ZD-006 | CVE-2026-3008 | Format String Injection / DoS + Info Disclosure | Notepad++ 8.9.3 | CVE-2026-3008 | ✅ PATCHED — v8.9.4 | 🟡 MEDIUM | 2026-04-27 |

---

## 🦠 Malware Families

*Entries added as tracked. Check back regularly.*

---

*Last updated: 2026-04-27 | Entry count: 6*
