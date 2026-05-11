# Vulnerabilities, Malware & Zero-Days — Jedi Archives

> Maintained by C3PO | Focus: Active zero-days, critical unpatched CVEs, and malware families relevant to aerospace, defense, and critical infrastructure
> Admiralty Scale grading applied. See README.md for full methodology.

---

## 🔴 Active Zero-Days (Unpatched)

| ID | Name | Type | Platform | CVE | Patch Status | Threat Level | Date Added |
|---|---|---|---|---|---|---|---|
| ZD-002 | RedSun | LPE / Cloud Files API + NTFS Junction | Windows 10/11/Server 2019+ | None — UNPATCHED | 🔴 UNPATCHED **(Day 25; PT TOMORROW May 12)** | 🔴 HIGH | 2026-04-18 |
| ZD-003 | UnDefend | DoS / Defender Blind + Telemetry Falsification | Windows 10/11/Server 2019+ | None — UNPATCHED | 🔴 UNPATCHED **(Day 25; PT TOMORROW May 12)** | 🟡 MEDIUM (🔴 HIGH chained; telemetry manipulation confirmed) | 2026-04-18 |
| ZD-016 | CVE-2026-0300 | Unauthenticated RCE / Buffer Overflow | PAN-OS PA-Series & VM-Series firewalls (User-ID Auth Portal) | CVE-2026-0300 | 🔴 UNPATCHED — **Patches ETA May 13 (tomorrow)** ✅ CISA KEV; FCEB deadline May 9 PASSED | 🔴 HIGH — CL-STA-1132 (China-nexus); exploitation since April 9; 5,400+ exposed; patch tomorrow | 2026-05-06 |

---

## 🔴 Active Zero-Days (Partially Patched — One Half Still Unpatched)

| ID | Name | Type | Platform | CVE | Patch Status | Threat Level | Date Added |
|---|---|---|---|---|---|---|---|
| ZD-017 | Dirty Frag | LPE / Page Cache Write via IPSec ESP + RxRPC in-place decryption | Linux kernel 4.10–7.0 (all major distros) | CVE-2026-43284 (ESP) + CVE-2026-43500 (RxRPC) | ⚠️ PARTIAL — ESP (CVE-2026-43284) patched upstream + AlmaLinux/CloudLinux; **RxRPC (CVE-2026-43500) UNPATCHED**; RHEL/Ubuntu kernel packages pending | 🔴 HIGH — Public PoC; active exploitation confirmed (Microsoft); container escape path; Copy Fail mitigation provides NO protection | 2026-05-11 |

---

## 📋 N-Day CVEs (Patched, Actively Exploited or High Risk)

### Recently Added (2026-05-06)

| ID | Name | Type | Platform | CVE | Patch Status | Threat Level | Date Added |
|---|---|---|---|---|---|---|---|
| — | CVE-2026-23918 | Double Free DoS + RCE / mod_http2 | Apache HTTP Server 2.4.66 only | CVE-2026-23918 | ✅ PATCHED — Apache 2.4.67 (May 4, 2026) | 🟠 HIGH — Trivial DoS; credible RCE on Debian/Docker; no in-the-wild exploitation yet | 2026-05-06 |
| — | CVE-2026-4670 | Authentication Bypass / Backend Command Port | Progress MOVEit Automation (MFT) | CVE-2026-4670 | ✅ PATCHED — 2024.1.8 / 2025.0.9 / 2025.1.5 | 🟠 HIGH — CVSS 9.8; **<100 exposed instances** (Censys, May 2026); MFT threat-actor interest; no active exploitation confirmed | 2026-05-06 |

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
| ZD-013 | cPanel Auth Bypass | Pre-Auth CRLF Injection → WHM Root | cPanel & WHM (all versions after 11.40) · WP Squared | CVE-2026-41940 | ✅ PATCHED — April 28, 2026 | 🔴 CRITICAL — CISA KEV; 40K+ hosts compromised; ransomware + APT | 2026-04-30 |
| ZD-014 | Copy Fail | LPE / Page Cache Poisoning via Kernel Crypto API | Linux Kernel 4.14 through 7.0-rc (all distros since July 2017) | CVE-2026-31431 | ✅ PATCHED — All major distros patched; **CISA KEV FCEB deadline May 15** | 🔴 HIGH (✅ CISA KEV; active exploitation; **deadline 4 days away**) | 2026-05-01 |

---

## 🦠 Malware Families

*Entries added as tracked. Check back regularly.*

---

*Last updated: 2026-05-11 | Added DIRTYFRAG (CVE-2026-43284 + CVE-2026-43500); RedSun/UnDefend Day 25 — **PT TOMORROW**; CVE-2026-0300 patches tomorrow (May 13); COPYFAIL KEV deadline May 15 (4 days) | Entry count: 18*
