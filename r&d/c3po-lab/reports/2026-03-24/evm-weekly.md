# 📋 EVM Weekly Intel Report
**Enterprise Vulnerability Management**
Week of: March 17–24, 2026

---

## 🔴 Critical / Actively Exploited
> CVEs being actively exploited in the wild. Patch immediately.

- **[CVE-2026-20131 — Cisco Secure Firewall Management Center (FMC)](https://thehackernews.com/2026/03/interlock-ransomware-exploits-cisco-fmc.html)** | CVSS: 10.0
  - Insecure deserialization of user-supplied Java byte stream allows an unauthenticated remote attacker to execute arbitrary Java code as root. Exploited as a zero-day by Interlock ransomware since January 26, 2026 — over a month before Cisco disclosed. CISA added March 19 with a due date of March 22.
  - Patch available: Yes | CISA KEV: Yes | Known ransomware use: Yes

- **[CVE-2026-33017 — Langflow AI](https://thehackernews.com/2026/03/critical-langflow-flaw-cve-2026-33017.html)** | CVSS: 9.3
  - Missing authentication combined with code injection allows unauthenticated RCE. Weaponized in the wild within 20 hours of public disclosure — no PoC required. Multiple independent threat actors built working exploits directly from the advisory.
  - Patch available: Yes | CISA KEV: Check pending | Actively exploited: Yes

- **[CVE-2025-31277 / CVE-2025-43520 / CVE-2025-43510 — Apple Multiple Products](https://www.cisa.gov/known-exploited-vulnerabilities-catalog)** | CVSS: High
  - Three Apple vulns added to KEV on March 20; part of the DarkSword iOS exploit kit chain (see below). Affect Safari, iOS, iPadOS, macOS, watchOS, visionOS, tvOS. Processing malicious web content leads to memory corruption or kernel memory write.
  - Patch available: Yes (iOS 18.7.2+/26.1+) | CISA KEV: Yes | Due: April 3

---

## 🟠 High Priority — Patch This Week
> High severity, not yet confirmed exploited at scale but drawing heavy attention.

- **[CVE-2026-4681 — PTC Windchill & FlexPLM](https://www.bleepingcomputer.com/news/security/ptc-warns-of-imminent-threat-from-critical-windchill-flexplm-rce-bug/)** | CVSS: Critical (score TBD)
  - ⚠️ **AEROSPACE/DEFENSE FLAG**: Windchill is a PLM system widely used in aerospace, defense, and weapons system design. Deserialization bug allows RCE. No patch yet — PTC actively developing. German federal police (BKA) dispatched agents to affected companies overnight. PTC confirms "credible evidence of imminent threat." Apply Apache/IIS mitigation rule immediately; disconnect internet-facing instances if mitigation not possible.
  - Patch available: No | IOCs published: Yes (GW.class, dpr_*.jsp, suspicious `run?p=` requests)

- **[CVE-2026-20963 — Microsoft SharePoint](https://www.cisa.gov/known-exploited-vulnerabilities-catalog)** | CVSS: High
  - Deserialization of untrusted data allows network-based code execution. CISA KEV added March 18, due date March 21 — already past due for federal agencies.
  - Patch available: Yes | CISA KEV: Yes

- **[CVE-2025-54068 — Laravel Livewire](https://www.cisa.gov/known-exploited-vulnerabilities-catalog)** | CVSS: High
  - Code injection allows unauthenticated attackers to achieve RCE in specific deployment scenarios. KEV added March 20.
  - Patch available: Yes | CISA KEV: Yes

- **[CVE-2026-24291 "RegPwn" — Microsoft Windows](https://thehackernews.com/2026/03/weekly-recap-cicd-backdoor-fbi-buys.html)** | CVSS: TBD
  - Registry-based privilege escalation bug earning its own nickname. Trending heavily in the security community this week; patch and monitor.
  - Patch available: Check March Patch Tuesday | CISA KEV: No

- **[CVE-2026-21643 — Fortinet FortiClient EMS](https://bishopfox.com/blog/cve-2026-21643-pre-authentication-sql-injection-in-forticlient-ems-7-4-4)** | CVSS: High
  - Pre-authentication SQL injection in FortiClient EMS 7.4.4. Bishop Fox published full technical writeup this week.
  - Patch available: Yes | CISA KEV: No (yet)

- **[CVE-2026-3564 — ConnectWise ScreenConnect](https://www.connectwise.com/company/trust/security-bulletins/2026-03-17-screenconnect-bulletin)** | CVSS: High
  - New ScreenConnect advisory March 17. Given prior ScreenConnect exploit history, treat as urgent.
  - Patch available: Yes | CISA KEV: No

---

## 🟡 Watch List — Patch Next Cycle
> Moderate risk; keep on radar.

- **[CVE-2026-3864 — Kubernetes](https://github.com/kubernetes/kubernetes/issues/137797)** | CVSS: TBD — Node-level exposure; relevant for containerized defense environments.
- **[CVE-2026-1603 — Ivanti Endpoint Manager (EPM)](https://www.cisa.gov/known-exploited-vulnerabilities-catalog)** — Auth bypass leaking stored credentials. KEV added March 9; due date March 23 (past for federal agencies).
- **[CVE-2025-26399 — SolarWinds Web Help Desk](https://www.cisa.gov/known-exploited-vulnerabilities-catalog)** — Deserialization RCE in AjaxProxy. KEV added March 9.
- **[CVE-2026-26189 — Trivy GitHub Action](https://github.com/aquasecurity/trivy-action/security/advisories/GHSA-9p44-j4g5-cfx5)** — Part of the TeamPCP supply chain compromise. Any org using Trivy GitHub Actions should rotate secrets and audit pipelines immediately.
- **[CVE-2026-25769 — Wazuh Cluster](https://hakaisecurity.io/cve-2026-25769-rce-via-insecure-deserialization-in-wazuh-cluster-remote-command-execution-through-cluster-protocol/research-blog/)** — RCE via insecure deserialization in cluster protocol. Relevant if Wazuh is in your stack.

---

## 🏭 Aerospace/Defense Vendor Bulletins
> Patches and advisories from aerospace/defense relevant vendors.

- **[PTC — CVE-2026-4681 Windchill/FlexPLM Critical Advisory](https://www.ptc.com/en/about/trust-center/advisory-center/active-advisories/windchill-flexplm-critical-vulnerability)** — See 🟠 High Priority above. Directly relevant to any aerospace org running Windchill for PLM. Apply mitigations now; full patching expected soon.
- **[Rockwell Automation — CVE-2021-22681 (KEV re-added March 5)](https://www.cisa.gov/known-exploited-vulnerabilities-catalog)** — Studio 5000 Logix Designer credential protection flaw. Allows unauthorized connection to Logix controllers. OT/ICS-relevant for manufacturing and defense industrial base.
- **[Atlassian — March 17 Security Bulletin](https://confluence.atlassian.com/security/security-bulletin-march-17-2026-1721271371.html)** — CVE-2026-21570 (Bamboo) and CVE-2026-21884 (Crowd Data Center) patched. Common in enterprise engineering environments.

---

## 📊 Vuln Trend This Week
> Big picture — what's the story this week in the vuln landscape?

- **7 new CISA KEV entries** added between March 17–20, spanning Apple, Cisco, Microsoft, Laravel, and Craft CMS.
- **Big theme: CVSS 10.0 is real** — Cisco FMC hit the ceiling and was actively exploited before anyone knew. Zero-day windows are narrowing to weeks or months, not days.
- **PLM systems are now a target class** — PTC Windchill joining the critical advisory list is significant for aerospace/defense. Expect more scrutiny on PLM/MES/ERP attack surface.
- **Supply chain chaining**: TeamPCP's Trivy→LiteLLM cascade shows how one CI/CD compromise generates credentials for the next. Audit third-party tooling in build pipelines.
- **Speed of exploitation remains brutal**: Langflow exploited within 20 hours; Cisco FMC used as zero-day for 6 weeks pre-disclosure. Detection and response velocity > patch velocity.

---

*Report generated by C3PO 🤖 | Week of March 17–24, 2026 | Sources linked inline*
