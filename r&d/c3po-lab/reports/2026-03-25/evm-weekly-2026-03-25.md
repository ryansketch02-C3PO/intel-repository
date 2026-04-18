# 📋 EVM Weekly Intel Report
**Enterprise Vulnerability Management**
Week of: March 17–24, 2026

---

## 🔴 Critical / Actively Exploited
> CVEs being actively exploited in the wild. Patch immediately.

- **[CVE-2026-4681 — PTC Windchill & FlexPLM RCE](https://www.bleepingcomputer.com/news/security/ptc-warns-of-imminent-threat-from-critical-windchill-flexplm-rce-bug/)** | CVSS: 10.0
  - Deserialization of untrusted data allows unauthenticated RCE. Affects Windchill PDMLink 11.0 M030 through 13.1.3.0 and FlexPLM 11.0–13.x (all CPS). **No patch yet — in active development.** German federal police (BKA) dispatched agents overnight to warn companies nationwide of imminent exploitation. PTC confirms "credible evidence of imminent threat by a third-party group."
  - **Mitigation:** Apply vendor-provided Apache/IIS servlet path block rule immediately to all deployments including non-internet-facing. Prioritize internet-facing first. Disconnect or shut down if mitigation impossible.
  - **IOCs:** Presence of `GW.class` or `dpr_<8hex>.jsp` on Windchill server = attacker has pre-staged for RCE. Suspicious requests with `run?p=` or `.jsp?c=` patterns + unusual User-Agent activity.
  - Patch available: ❌ In development | CISA KEV: ❌ (not yet listed)
  - ⚠️ **AEROSPACE/DEFENSE FLAG:** PLM systems are core to weapons system design, defense manufacturing, and supply chains. **This is the top priority vuln this week for the sector.**

- **[CVE-2026-20131 — Cisco Secure Firewall Management Center (FMC) Java RCE](https://www.cve.org/CVERecord?id=CVE-2026-20131)** | CVSS: Critical
  - Deserialization of untrusted data in FMC and Cisco SCC web management interface. Unauthenticated remote attacker executes arbitrary Java code as root.
  - Patch available: ✅ | CISA KEV: ✅ (added 3/19, due 3/22 — **federal deadline already passed**)
  - 🔴 **Known ransomware campaigns confirmed**

- **[CVE-2026-20963 — Microsoft SharePoint Deserialization RCE](https://www.cve.org/CVERecord?id=CVE-2026-20963)**
  - Deserialization of untrusted data allows unauthenticated network-based code execution against SharePoint servers.
  - Patch available: ✅ | CISA KEV: ✅ (added 3/18, due 3/21 — **federal deadline passed**)

---

## 🟠 High Priority — Patch This Week
> High severity, not yet exploited but likely soon.

- **[CVE-2025-31277 — Apple Multiple Products Buffer Overflow](https://www.cve.org/CVERecord?id=CVE-2025-31277)** | CISA KEV: ✅ (3/20, due 4/3)
  - Safari, iOS, watchOS, visionOS, iPadOS, macOS, tvOS — maliciously crafted web content leads to memory corruption. All Apple platforms.

- **[CVE-2025-43520 — Apple Multiple Products Kernel Write (Classic Buffer Overflow)](https://www.cve.org/CVERecord?id=CVE-2025-43520)** | CISA KEV: ✅ (3/20, due 4/3)
  - Malicious app causes unexpected system termination or writes kernel memory. watchOS/iOS/iPadOS/macOS/visionOS/tvOS/iPadOS.

- **[CVE-2025-43510 — Apple Multiple Products Improper Locking](https://www.cve.org/CVERecord?id=CVE-2025-43510)** | CISA KEV: ✅ (3/20, due 4/3)
  - Malicious app causes unexpected changes in shared process memory. All Apple platforms.

- **[CVE-2025-54068 — Laravel Livewire Code Injection / RCE](https://www.cve.org/CVERecord?id=CVE-2025-54068)** | CISA KEV: ✅ (3/20, due 4/3)
  - Unauthenticated attackers achieve remote command execution in specific Livewire scenarios. Any Livewire-based PHP app at risk.

- **[CVE-2025-32432 — Craft CMS Remote Code Injection](https://www.cve.org/CVERecord?id=CVE-2025-32432)** | CISA KEV: ✅ (3/20, due 4/3)
  - Remote attacker executes arbitrary code. Actively listed by CISA.

- **[CVE-2026-3909 — Google Skia Out-of-Bounds Write](https://www.cve.org/CVERecord?id=CVE-2026-3909)** | CISA KEV: ✅
  - Affects Chrome, ChromeOS, Android, Flutter, and potentially others. Crafted HTML page triggers OOB memory access via Skia graphics library.

---

## 🟡 Watch List — Patch Next Cycle
> Medium severity or low exploitation likelihood. Keep on radar.

- **[CVE-2025-66376 — Zimbra ZCS Cross-Site Scripting](https://www.cve.org/CVERecord?id=CVE-2025-66376)** | CISA KEV: ✅ (3/18, due 4/1)
  - Classic UI XSS via CSS @import directives in email HTML. Nation-state actors have historically favored Zimbra as an entry point.

- **[CVE-2025-47813 — Wing FTP Server Information Disclosure](https://www.cve.org/CVERecord?id=CVE-2025-47813)** | CISA KEV: ✅ (3/16, due 3/30)
  - Error message containing sensitive information exposed via long UID cookie value. Low complexity, useful for recon.

---

## 🏭 Aerospace/Defense Vendor Bulletins
> Patches and advisories from aerospace/defense relevant vendors.

- **[PTC — Windchill & FlexPLM Critical RCE Advisory (CVE-2026-4681)](https://www.ptc.com/en/about/trust-center/advisory-center/active-advisories/windchill-flexplm-critical-vulnerability)**
  - Affects all supported Windchill (11.0 M030 → 13.1.3.0) and FlexPLM versions. CVSS 10.0 / CVSS4 9.3. No patch — apply servlet path mitigation now. German BKA response unprecedented for a software CVE. The velocity of the law enforcement response suggests possible nation-state discovery or planned exploitation campaign.

- **[FCC Bans All Foreign-Made Consumer Routers Over Supply Chain Risk](https://www.bleepingcomputer.com/news/security/fcc-bans-new-routers-made-outside-the-usa-over-security-risks/)**
  - FCC updated its Covered List to include all consumer routers manufactured outside the US — banning new model imports. Defense contractors and aerospace facilities with TP-Link, Netgear, or other foreign-made perimeter hardware should review inventory.

---

## 📊 Vuln Trend This Week
- **7 CISA KEV additions** March 16–20 — heavy Apple platform week plus Cisco FMC RCE (ransomware confirmed)
- **CVE-2026-4681 (Windchill)** is the story of the week: CVSS 10.0, no patch, law enforcement mobilization, credible imminent threat. Defense/aerospace PLM environments are direct targets.
- **Deserialization bugs dominating:** Cisco FMC, SharePoint, and Windchill are all deserialization-based RCE this week. Detection teams should tune for Java deserialization payloads and suspicious Java process spawning from webserver processes.
- Apple shipped 3 KEV-listed patches across the entire product stack simultaneously — unusual batching suggests coordinated discovery/reporting, possibly from a single research group.

---
*Report generated by C3PO 🤖 | Sources linked inline | Data range: March 17–24, 2026*
