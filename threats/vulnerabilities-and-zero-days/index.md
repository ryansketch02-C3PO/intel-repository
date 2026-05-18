# Vulnerabilities, Malware & Zero-Days — Jedi Archives

> Maintained by C3PO | Focus: Active zero-days, critical unpatched CVEs, and malware families relevant to aerospace, defense, and critical infrastructure
> Admiralty Scale grading applied. See README.md for full methodology.

---

## 🔴 Active Zero-Days (Unpatched)

| ID | Name | Type | Platform | CVE | Patch Status | Threat Level | Date Added |
|---|---|---|---|---|---|---|---|
| ZD-040 | MiniPlasma | LPE / Race Condition → SYSTEM via cldflt.sys | Windows 10 · Windows 11 · Server (all; NOT Insider Canary) | CVE-2020-17103 regression — UNASSIGNED (new) | 🔴 UNPATCHED — Works on fully patched May 2026 Win11 (KB5089549); Canary build may be fixed | 🟠 HIGH — Public PoC (GitHub); SYSTEM shell confirmed; local-only; 6th Chaotic Eclipse tool; predecessor tools already weaponized by Russia-geolocated actors within 24h | 2026-05-18 |
| ZD-031 | CVE-2026-42897 | XSS / Arbitrary Code Execution via Crafted Email | Microsoft Exchange Server (on-prem, all versions w/ OWA) | CVE-2026-42897 | 🔴 UNPATCHED — Out-of-band mitigations only | 🔴 HIGH — Actively exploited zero-day; email-borne; OWA session hijack + code execution; Exchange Online NOT affected | 2026-05-15 |
| ZD-026 | YellowKey | BitLocker Bypass / WinRE FStX Feature Update Simulation | Windows 11 · Server 2022/2025 (Win10 unaffected) | None — UNPATCHED | 🔴 UNPATCHED **(Day 3; full PoC public; TPM-only confirmed; TPM+PIN unconfirmed)** | 🔴 HIGH — Public PoC; encryption assurance bypass; same researcher as RedSun/UnDefend | 2026-05-13 |
| ZD-027 | GreenPlasma | EoP / CTFMON Arbitrary Section Creation | Windows 11 · Server 2022/2025 | None — UNPATCHED | 🔴 UNPATCHED **(Day 1; partial PoC — SYSTEM shell component withheld)** | 🟡 MEDIUM (🔴 HIGH if completed; cldapi.dll abuse; Nightmare-Eclipse fifth tool) | 2026-05-13 |
| ZD-002 | RedSun | LPE / Cloud Files API + NTFS Junction | Windows 10/11/Server 2019+ | None — UNPATCHED | 🔴 UNPATCHED **(Day 27 — PT SHIPPED, NO FIX; CISA KEV watch May 15)** | 🔴 HIGH | 2026-04-18 |
| ZD-003 | UnDefend | DoS / Defender Blind + Telemetry Falsification | Windows 10/11/Server 2019+ | None — UNPATCHED | 🔴 UNPATCHED **(Day 27 — PT SHIPPED, NO FIX; CISA KEV watch May 15)** | 🟡 MEDIUM (🔴 HIGH chained; telemetry manipulation confirmed) | 2026-04-18 |
| ZD-016 | CVE-2026-0300 | Unauthenticated RCE / Buffer Overflow | PAN-OS PA-Series & VM-Series firewalls (User-ID Auth Portal) | CVE-2026-0300 | ✅ PATCHED — May 13, 2026 ✅ CISA KEV; FCEB deadline May 9 PASSED | 🔴 HIGH — CL-STA-1132 (China-nexus); exploitation since April 9; 5,400+ exposed | 2026-05-06 |

---

## 🔴 Active Zero-Days (Partially Patched — One Half Still Unpatched)

| ID | Name | Type | Platform | CVE | Patch Status | Threat Level | Date Added |
|---|---|---|---|---|---|---|---|
| ZD-032 | ssh-keysign-pwn | LPE / SSH Private Key Read via SUID Binary Abuse | Linux kernel (all distros before May 14, 2026 upstream patch) | CVE TBD | ⚠️ PARTIAL — Upstream patched May 14, 2026; **all distro packages pending** | 🟠 HIGH — Public PoC; SSH host key theft enables server impersonation; Yann Horn fix proposed 6 years ago, finally merged | 2026-05-15 |
| ZD-029 | Fragnesia | LPE / Page Cache Write via XFRM/ESP + TCP Logic Bug | Linux kernel (all before May 13, 2026 upstream) | **CVE-2026-46300** (CVSS 7.8) | ⚠️ PARTIAL — Upstream kernel patched May 13, 2026; **all major distribution packages still pending** | 🔴 HIGH — Public PoC (full root shell); **no race condition** = more reliable than Dirty Frag; container escape path; distros unpatched | 2026-05-14 |
| ZD-017 | Dirty Frag | LPE / Page Cache Write via IPSec ESP + RxRPC in-place decryption | Linux kernel 4.10–7.0 (all major distros) | CVE-2026-43284 (ESP) + CVE-2026-43500 (RxRPC) | ⚠️ PARTIAL — ESP (CVE-2026-43284) patched upstream + AlmaLinux/CloudLinux; **RxRPC (CVE-2026-43500) UNPATCHED**; RHEL/Ubuntu kernel packages pending | 🔴 HIGH — Public PoC; active exploitation confirmed (Microsoft); container escape path; Copy Fail mitigation provides NO protection | 2026-05-11 |

---

## 📋 N-Day CVEs (Patched, Actively Exploited or High Risk)

### Recently Added (2026-05-18)

| ID | Name | Type | Platform | CVE | Patch Status | Threat Level | Date Added |
|---|---|---|---|---|---|---|---|
| ZD-042 | OpenDSM RCE Chain | Missing Auth + OS Command Injection → RCE (chainable) | OpenDSM (all versions with Docker remote-user config; `report_network_map.php`) | CVE-2026-28515 (CVSS 9.3) + CVE-2026-28516 (CVSS 9.3) | ⚠️ PATCH STATUS UNKNOWN — exploitation attempts confirmed as of May 18, 2026; vendor advisory pending | 🟠 HIGH — Two chained CVEs (CVSS 9.3 each); auth bypass + OS command injection → RCE in 5 HTTP requests; SQL injection also available; exploitation attempts confirmed | 2026-05-18 |
| ZD-041 | CVE-2026-1281 | Code Injection / Unauthenticated RCE | Ivanti Endpoint Manager Mobile (EPMM) | CVE-2026-1281 | ✅ PATCHED — Ivanti EPMM patch available; ✅ CISA KEV added May 13, 2026; FCEB deadline set | 🔴 HIGH — CISA KEV; unauthenticated remote code execution on MDM infrastructure; 83% of observed exploitation from single Russian-geolocated IP (Duggan USA research); nation-state interest in MDM compromise for device fleet access | 2026-05-18 |
| ZD-039 | NGINX Rift | Heap Buffer Overflow / DoS (reliable) + RCE (ASLR-off or advanced) | NGINX Open Source 0.6.27–1.30.0 · NGINX Plus R32–R36 + F5 dependent products | CVE-2026-42945 (CVSS 9.2) | ✅ PATCHED — NGINX 1.30.1/1.31.0; NGINX Plus R32 P6/R36 P4; AlmaLinux patched; RHEL/Ubuntu pending | 🔴 HIGH — CVSS 9.2; public PoC; **active exploitation confirmed** (VulnCheck; Chinese IP + Vulnhuntr + PHP web shell); 18-year-old bug; ubiquitous attack surface | 2026-05-18 |

### Recently Added (2026-05-15)

| ID | Name | Type | Platform | CVE | Patch Status | Threat Level | Date Added |
|---|---|---|---|---|---|---|---|
| ZD-030 | CVE-2026-20182 | Auth Bypass / Remote Admin Privilege Escalation | Cisco Catalyst SD-WAN Controller + Manager | CVE-2026-20182 | ✅ PATCHED — May 15, 2026 ✅ CISA KEV; FCEB deadline May 17, 2026 | 🔴 CRITICAL — CVSS 10.0; CISA KEV same day as patch; exploited by UAT-8616; 6th SD-WAN zero-day in 2026; full admin access to SD-WAN management plane | 2026-05-15 |
| ZD-033 | Pack2TheRoot | LPE / TOCTOU Race Condition on Transaction Flags | Linux — PackageKit 1.0.2–1.3.4 (Ubuntu/Debian/RHEL/Fedora/SUSE/Rocky/Amazon Linux) | CVE-2026-41651 | ✅ PATCHED — PackageKit 1.3.5; distro packages available (RHEL 8/9, Debian, Ubuntu, SUSE) | 🔴 HIGH — CVSS 8.8; actively exploited; public PoC within 24h of disclosure; any low-priv local user → root via crafted package + RPM/DEB scriptlets; 12+ year window; PoC trivially weaponisable | 2026-05-15 |
| ZD-034 | CVE-2026-41103 | Auth Bypass / Incorrect Authentication Algorithm | Microsoft SSO Plugin for Jira & Confluence (< v1.3.3) | CVE-2026-41103 | ✅ PATCHED — v1.3.3 | 🔴 CRITICAL — CVSS 9.1; unauthenticated network attacker can forge Entra ID identity; **actively exploited**; full read/write access to Jira & Confluence without authentication; "Exploitation More Likely" | 2026-05-15 |
| ZD-035 | CVE-2026-35421 | RCE / Heap Buffer Overflow | Windows GDI (Windows 10/11/Server — all supported) | CVE-2026-35421 | ✅ PATCHED — May 2026 Patch Tuesday | 🟠 HIGH — CVSS 7.8; local trigger via crafted Enhanced Metafile (EMF) in Microsoft Paint; social engineering / malicious file delivery vector | 2026-05-15 |
| ZD-036 | CVE-2026-40365 | RCE / Insufficient Access Control | Microsoft SharePoint Server | CVE-2026-40365 | ✅ PATCHED — May 2026 Patch Tuesday | 🟠 HIGH — CVSS 8.8; authenticated attacker (Site Owner+); network-based; arbitrary code injection + remote execution on SharePoint Server | 2026-05-15 |
| ZD-037 | CVE-2026-32161 | RCE / Use-After-Free + Race Condition | Windows Native WiFi Miniport Driver (all supported Windows) | CVE-2026-32161 | ✅ PATCHED — May 2026 Patch Tuesday | 🟠 HIGH — CVSS 7.5; unauthenticated adjacent network RCE; no user interaction required; wireless attack surface | 2026-05-15 |
| ZD-038 | CVE-2026-40402 | EoP / Hyper-V Guest-to-Host | Windows Hyper-V (all supported Windows Server / Windows 11) | CVE-2026-40402 | ✅ PATCHED — May 2026 Patch Tuesday | 🟠 HIGH — CVSS 9.3; guest-to-host escape risk in multi-tenant and private cloud environments; outsized blast radius in shared infrastructure | 2026-05-15 |

### Recently Added (2026-05-14)

| ID | Name | Type | Platform | CVE | Patch Status | Threat Level | Date Added |
|---|---|---|---|---|---|---|---|
| ZD-028 | CVE-2026-45185 | Unauthenticated RCE / Use-After-Free in GNU TLS Shutdown | Exim MTA 4.97–4.99.2 **(GNU TLS builds only** — Debian/Ubuntu) | CVE-2026-45185 | ✅ PATCHED — Exim 4.99.3 | 🔴 HIGH — CVSS 9.8; unauthenticated RCE on mail servers; OpenSSL builds NOT affected; Sandworm weaponized Exim RCE within days in 2019 (CVE-2019-10149) — high mass-exploitation risk | 2026-05-14 |
| — | CVE-2026-31635 | Remote DoS / Kernel Panic (Inverted rxrpc Authenticator Length Check) | Linux kernel (rxgk/rxrpc-enabled; AFS environments — mainstream distros not affected) | CVE-2026-31635 | ✅ PATCHED — Upstream fixed Apr 24, 2026; RHEL 10 fix deferred; RHEL 6–9/Debian stable/SUSE not affected | 🟡 MEDIUM — CVSS 7.5; remote unauthenticated DoS; narrow scope (AFS/rxgk only); no active exploitation | 2026-05-18 |

### Recently Added (2026-05-13)

| ID | Name | Type | Platform | CVE | Patch Status | Threat Level | Date Added |
|---|---|---|---|---|---|---|---|
| ZD-024 | CVE-2026-26083 | Missing Authorization RCE (Unauthenticated) | Fortinet FortiSandbox (on-prem, Cloud, PaaS) | CVE-2026-26083 | ✅ PATCHED — Fortinet FG-IR-26-136 (May 12, 2026) | 🔴 CRITICAL — CVSS 9.8; no auth required; security appliance + SOC visibility at risk; apply immediately | 2026-05-13 |
| ZD-025 | CVE-2026-40361 | Use-After-Free RCE (Local) | Microsoft Office Word · Microsoft 365 | CVE-2026-40361 | ✅ PATCHED — May 2026 Patch Tuesday | 🟠 HIGH — CVSS 8.4; local, no user interaction; post-exploitation chaining primitive | 2026-05-13 |
| ZD-021 | CVE-2026-41096 | Unauthenticated RCE / Heap Buffer Overflow | Windows DNS Client (all Windows 10/11/Server) | CVE-2026-41096 | ✅ PATCHED — May 2026 Patch Tuesday | 🔴 HIGH — CVSS 9.8; affects every Windows machine; MitM/rogue DNS → mass RCE; "Exploitation More Likely" | 2026-05-13 |
| ZD-022 | CVE-2026-41089 | Pre-Auth RCE / Stack Buffer Overflow | Windows Netlogon (Windows Server 2012–2025) | CVE-2026-41089 | ✅ PATCHED — May 2026 Patch Tuesday | 🔴 HIGH — CVSS 9.8; pre-auth RCE on domain controllers; patch ALL DCs in same window; forest-wide credential access if exploited | 2026-05-13 |
| ZD-023 | CVE-2026-44277 | Unauthenticated RCE / Improper Access Control | Fortinet FortiAuthenticator (IAM) < 6.5.7 / 6.6.9 / 8.0.3 | CVE-2026-44277 | ✅ PATCHED — FortiAuthenticator 6.5.7 / 6.6.9 / 8.0.3 | 🔴 HIGH — Critical; unauthenticated RCE on authentication infrastructure; RADIUS secrets at risk; widely deployed in A&D environments | 2026-05-13 |

### Recently Added (2026-05-12)

| ID | Name | Type | Platform | CVE | Patch Status | Threat Level | Date Added |
|---|---|---|---|---|---|---|---|
| ZD-019 | CVE-2026-6973 | Authenticated RCE / Memory Corruption | Ivanti EPMM (MDM) | CVE-2026-6973 | ✅ PATCHED — Apply immediately | 🔴 HIGH — Active exploitation confirmed by Ivanti; MDM compromise = device fleet control | 2026-05-12 |
| ZD-020 | Bleeding Llama | Heap OOB Read / Data Exfiltration | Ollama AI inference server (all versions < 0.17.1) | CVE-2026-7482 | ✅ PATCHED — Ollama 0.17.1 | 🔴 HIGH — CVSS 9.1; leaks API keys, env vars, chat data; unauthenticated on default deployments; AI lab threat | 2026-05-12 |

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
| ZD-014 | Copy Fail | LPE / Page Cache Poisoning via Kernel Crypto API | Linux Kernel 4.14 through 7.0-rc (all distros since July 2017) | CVE-2026-31431 | ✅ PATCHED — All major distros patched; ✅ CISA KEV FCEB deadline May 15 — **PASSED** | 🔴 HIGH (✅ CISA KEV; active exploitation; **FCEB deadline PASSED May 15**) | 2026-05-01 |

---

## 🦠 Malware Families

*Entries added as tracked. Check back regularly.*

---

*Last updated: 2026-05-18 | Added ZD-041 (CVE-2026-1281, Ivanti EPMM unauthenticated RCE, CISA KEV May 13) + ZD-042 (OpenDSM CVE-2026-28515 + CVE-2026-28516, missing auth + OS command injection, CVSS 9.3 each, chainable to RCE, exploitation attempts confirmed May 18); also added ZD-039 (CVE-2026-42945 NGINX Rift) + ZD-040 (MiniPlasma) earlier today; ZD-031 CVE-2026-42897 Exchange XSS CISA KEV deadline May 29; MiniPlasma (ZD-040) Day 1 unpatched; YellowKey (ZD-026) Day 6; RedSun (ZD-002)/UnDefend (ZD-003) Day 32 | Entry count: 42; + CVE-2026-31635 (Linux rxrpc remote DoS, patched upstream, narrow scope)*
