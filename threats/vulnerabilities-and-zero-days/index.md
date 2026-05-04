# Vulnerabilities, Malware & Zero-Days — Jedi Archives

> Maintained by C3PO | Focus: Active zero-days, critical unpatched CVEs, and malware families relevant to aerospace, defense, and critical infrastructure
> Admiralty Scale grading applied. See README.md for full methodology.

---

## 🔴 Active Zero-Days (Unpatched)

| ID | Name | Type | Platform | CVE | Patch Status | Threat Level | Date Added |
|---|---|---|---|---|---|---|---|
| ZD-002 | RedSun | LPE / Cloud Files API + NTFS Junction | Windows 10/11/Server 2019+ | None — UNPATCHED | 🔴 UNPATCHED (18 days, no Microsoft timeline; May PT ~May 13 next window) | 🔴 HIGH | 2026-04-18 |
| ZD-003 | UnDefend | DoS / Defender Blind + Telemetry Falsification | Windows 10/11/Server 2019+ | None — UNPATCHED | 🔴 UNPATCHED (18 days, no Microsoft timeline; May PT ~May 13 next window) | 🟡 MEDIUM (🔴 HIGH chained; telemetry manipulation confirmed) | 2026-04-18 |

---

## 📋 N-Day CVEs (Patched but Actively Exploited)

| ID | Name | Type | Platform | CVE | Patch Status | Threat Level | Date Added |
|---|---|---|---|---|---|---|---|
| ZD-001 | BlueHammer | LPE / Race Condition | Windows 10/11/Server | CVE-2026-33825 | ✅ PATCHED — April 2026 Patch Tuesday | 🔴 HIGH | 2026-04-14 |
| ZD-004 | MCPwn | Auth Bypass / Management Plane Takeover | Nginx UI (nginx-ui) | CVE-2026-33032 + CVE-2026-27944 | ✅ PATCHED — v2.3.6 | 🔴 HIGH | 2026-04-27 |
| ZD-005 | CVE-2026-3868 | Buffer Overflow DoS / ICS Management Interface | Moxa Secure Routers (TN-4900, EDR, OnCell, EDF-G1002-BP) | CVE-2026-3868 + CVE-2026-3867 | ✅ PATCHED — Firmware v3.24 | 🟡 MEDIUM (🔴 HIGH in ICS/OT) | 2026-04-27 |
| ZD-006 | CVE-2026-3008 | Format String Injection / DoS + Info Disclosure | Notepad++ 8.9.3 | CVE-2026-3008 | ✅ PATCHED — v8.9.4 | 🟡 MEDIUM | 2026-04-27 |
| ZD-007 | CVE-2026-32202 | Spoofing / Protection Mechanism Failure | Windows Shell | CVE-2026-32202 | ✅ PATCHED — April 2026 Patch Tuesday | 🟡 LOW-MEDIUM | 2026-04-27 |
| ZD-008 | CVE-2026-33824 | Double Free RCE / IKEv2 Fragment Reassembly | Windows IKE Extension (all supported Windows) | CVE-2026-33824 | ✅ PATCHED — April 2026 Patch Tuesday | 🔴 HIGH | 2026-04-27 |
| ZD-009 | CVE-2026-20180 | Authenticated RCE / Path Traversal | Cisco ISE 3.1–3.4 | CVE-2026-20180 + CVE-2026-20186 | ✅ PATCHED — ISE 3.2 P8 / 3.3 P8 / 3.4 P4 | 🔴 HIGH | 2026-04-27 |
| ZD-010 | CVE-2026-33827 | Race Condition RCE / TCP/IP Stack | Windows 10/11/Server (all supported) | CVE-2026-33827 | ✅ PATCHED — April 2026 Patch Tuesday | 🔴 HIGH | 2026-04-27 |
| ZD-011 | FIRESTARTER | Patch-Resistant Backdoor / Cisco ASA FTD | Cisco ASA, Firepower 1000/2100/4100/9300, Secure FW 200/1200/3100/4200/6100 | CVE-2025-20333 + CVE-2025-20362 | ⚠️ ACTIVE — Reimage required | 🔴 CRITICAL | 2026-04-27 |
| ZD-012 | GhostBearer | Pre-Auth SQLi / Credential Theft | LiteLLM (all versions < 1.83.7) | CVE-2026-42208 | ✅ PATCHED — LiteLLM v1.83.7 (recommend v1.83.10-stable) | 🔴 CRITICAL (actively exploited) | 2026-04-29 |
| ZD-013 | cPanel Auth Bypass | Authentication Bypass / Login Flow | cPanel & WHM (all versions after 11.40, see profile for per-branch fixed versions) | CVE-2026-41940 | ✅ PATCHED — Emergency update April 28–29, 2026 | 🔴 CRITICAL (actively exploited) | 2026-04-30 |
| ZD-014 | Copy Fail | LPE / Page Cache Poisoning via Kernel Crypto API | Linux Kernel 4.14 through 7.0-rc (all distros since July 2017) | CVE-2026-31431 | ⚠️ PARTIAL — Upstream patched (7.0, 6.19.12, 6.18.22); Ubuntu kmod mitigation out; AlmaLinux patched; RHEL/Amazon Linux/SUSE kernel backports PENDING | 🔴 HIGH (✅ CISA KEV; preliminary exploitation active; enterprise distros largely unpatched) | 2026-05-01 |

---

## 🦠 Malware Families

*Entries added as tracked. Check back regularly.*

---

*Last updated: 2026-05-04 (RedSun + UnDefend 18-day mark; CopyFail CISA KEV) | Entry count: 14*
