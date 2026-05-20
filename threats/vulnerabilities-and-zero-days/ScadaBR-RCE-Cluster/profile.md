# ScadaBR RCE Cluster — CVE-2026-8602–8605 (CISA-Disclosed ICS/SCADA Unauth RCE)

## Identity

| Field | Details |
|---|---|
| **CVEs** | CVE-2026-8602 · CVE-2026-8603 · CVE-2026-8604 · CVE-2026-8605 |
| **Vendor Advisory** | CISA advisory — disclosed May 20, 2026 |
| **Type** | Missing Authentication for Critical Functions + OS Command Injection + CSRF → Unauthenticated RCE |
| **Class** | CWE-306 (Missing Auth) · CWE-78 (OS Command Injection) · CWE-352 (CSRF) |
| **Affected Products** | ScadaBR 1.2.0 (ICS/SCADA HMI platform) |
| **Patch Status** | ⚠️ **PATCH STATUS UNKNOWN** — CISA disclosed; vendor guidance pending; remove external exposure immediately |
| **CVSS** | **9.1** (all four CVEs) |
| **Exploit Maturity** | ⚠️ DISCLOSED — Exploitation likelihood rated HIGH; no active exploitation confirmed |
| **CISA KEV** | Not yet listed — CISA-disclosed advisory |
| **Disclosed** | May 20, 2026 |
| **Threat Level** | 🟠 HIGH — CVSS 9.1; ICS/OT targeting; three chainable vuln classes; unauthenticated RCE path viable; no active exploitation confirmed but high-value target class |
| **Admiralty Grade** | A2 — CISA-disclosed |
| **ATT&CK** | T1190 (Exploit Public-Facing Application) · T1059.004 (Unix Shell) · T1059 (Scripting) · T1498 (Network DoS — ICS impact) |

---

## Overview

CISA disclosed four high-severity vulnerabilities in ScadaBR 1.2.0 — an open-source ICS/SCADA HMI (Human-Machine Interface) platform used in industrial control environments. The four CVEs span three vulnerability classes that can be chained to achieve unauthenticated remote code execution against exposed ScadaBR instances.

### Why ScadaBR Matters

ScadaBR is widely deployed in utilities, water/wastewater, energy, and manufacturing SCADA environments globally. Compromise of a ScadaBR HMI provides:
- Direct visibility into and control over physical processes
- Pivot into the OT/ICS network segment
- Potential for process disruption, sensor spoofing, or physical damage

---

## Vulnerabilities

| CVE | Type | Impact |
|---|---|---|
| CVE-2026-8602 | Missing Authentication for Critical Functions | Unauthenticated access to restricted admin functions |
| CVE-2026-8603 | OS Command Injection | Arbitrary OS command execution via injected input |
| CVE-2026-8604 | CSRF | Force authenticated users to perform unauthorized actions |
| CVE-2026-8605 | Missing Authentication / additional auth bypass | Extends the missing-auth attack surface |

**Chain:** CVE-2026-8602/8605 (missing auth) → CVE-2026-8603 (OS command injection) = unauthenticated RCE

---

## Mitigations

- **Remove external exposure** — ScadaBR should never be internet-facing; place behind a firewall immediately
- **Restrict to admin stations** — limit network access to trusted administrator workstations only
- **Enforce authentication** — deploy a WAF or reverse proxy with authentication in front of the application
- **Monitor** — watch for unusual process execution, configuration changes, and unexpected outbound connections
- Follow CISA advisory and vendor CSI guidance for patches when available

---

*Added: 2026-05-20 | CISA-disclosed | Vendor patch pending*
