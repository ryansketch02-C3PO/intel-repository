# UnDefend — Windows Defender Zero-Day DoS / Definition Update Block

## Identity

| Field | Details |
|---|---|
| **Vulnerability Name** | UnDefend |
| **CVE** | None assigned — **UNPATCHED as of 2026-04-27** |
| **Type** | Denial-of-Service (DoS) / Security Tool Degradation |
| **Class** | Defender Update Mechanism Abuse — Standard User Privilege |
| **Affected Platforms** | Windows 10, Windows 11, Windows Server 2019 and later |
| **Patch Status** | 🔴 **UNPATCHED** — No official patch, no CVE, no Microsoft timeline (19 days as of 2026-05-05; confirmed next window: May 12 Patch Tuesday) |
| **PoC Status** | 🔴 **PUBLIC** — Source code on GitHub (Nightmare-Eclipse repository) |
| **Discovered By** | Chaotic Eclipse / Nightmare Eclipse (pseudonymous researcher) |
| **Public Disclosure** | April 16, 2026 (released simultaneously with RedSun) |
| **Confirmed Working** | Huntress — observed active exploitation April 16, 2026 |
| **Exploited in Wild** | ✅ YES — confirmed in live intrusion alongside BlueHammer and RedSun |
| **Threat Level** | 🟡 MEDIUM (standalone) → 🔴 HIGH (as an enabler in a chained attack) |
| **Admiralty Grade** | A1 — active exploitation confirmed by Huntress in verified intrusion |

---

## Overview

UnDefend is the third zero-day in Chaotic Eclipse's April 2026 disclosure trio, dropped alongside RedSun on April 16. Unlike its siblings, UnDefend doesn't hand an attacker SYSTEM access. What it does is arguably more insidious: it **blinds Windows Defender**, blocking signature definition updates so that Defender's detection coverage quietly stagnates while the attacker operates freely.

The concept is straightforward — a standard, unprivileged local user can trigger a condition that prevents Defender from pulling new virus definitions. On systems where Defender is the primary (or only) endpoint protection control, this has a compounding effect: the longer UnDefend runs, the wider the gap between Defender's known-threat database and real-world malware. If an attacker with a reasonably new tool is operating in the environment, a degraded Defender becomes progressively less likely to catch them.

In the observed April 16 attack, UnDefend was deployed **first**, before RedSun escalated to SYSTEM. The tactical logic is clear: blind the antivirus, then run the privilege escalation. The kill chain is cleaner when your LPE exploit isn't being scanned by an up-to-date threat database.

Additionally, Chaotic Eclipse noted that on systems where Microsoft pushes a **major Defender platform update**, UnDefend can be used to **disable Defender entirely** — not just freeze its signatures. That broader impact depends on timing and update cadence, but represents the worst-case potential.

---

## Technical Analysis

### What UnDefend Does

UnDefend exploits a flaw in Defender's **signature update mechanism** that can be triggered by a standard user without elevated privileges. The specific technical details of the update mechanism abuse have not been fully published (the PoC is on GitHub but detailed writeups remain limited compared to BlueHammer/RedSun), however the confirmed behavioral outcome is:

1. **Defender's definition update process is blocked** — signature pulls fail or stall indefinitely
2. **Defender continues running** but operates on an increasingly stale threat database
3. **On major platform update cycles:** the condition can cascade into a full Defender disable

The exploit does not require the attacker to have admin rights, does not appear in standard AV/EDR process telemetry as malicious, and is difficult to distinguish from legitimate update failures (network timeouts, service hiccups) without specific behavioral monitoring.

### Chaining Logic — Why UnDefend Matters

UnDefend on its own is a supporting capability, not a primary attack. Its value emerges in chained operations:

```
Stage 1 — UnDefend:
  Block Defender signature updates
  → Defender's detection coverage freezes at last-known-good state
  → New or modified exploit tools less likely to be caught
        ↓
Stage 2 — RedSun or BlueHammer:
  Execute LPE exploit with reduced detection risk
  → Attacker reaches SYSTEM
        ↓
Stage 3 — Post-Exploitation:
  Credential harvesting, lateral movement, ransomware staging
  → Defender still running but blind to new TTPs
```

This is not theoretical — it is the exact sequence Huntress observed on April 16.

### Potential for Full Defender Disable

Chaotic Eclipse stated that UnDefend can, under certain conditions, **disable Defender entirely** when Microsoft is pushing a major Defender platform update (as opposed to routine signature updates). The mechanism appears to exploit a race or state condition in the platform update workflow. This has not been independently confirmed in public reporting but was included in the researcher's disclosure. The risk is real enough to warrant monitoring.

---

## Affected Systems

| Platform | Impact | Notes |
|---|---|---|
| Windows 10 (all builds) | Defender definition updates blocked | Confirmed |
| Windows 11 (all builds) | Defender definition updates blocked | Confirmed |
| Windows Server 2019+ | Defender definition updates blocked | Confirmed |
| Any Windows w/ Defender platform update in progress | Potential full Defender disable | Per researcher — not yet independently confirmed |

**Requires:** Local user access only — no elevation needed

**Mitigated by:** Third-party AV/EDR that does not rely on Defender's update mechanism; Defender for Endpoint (cloud-managed) may have additional resilience but not confirmed

---

## Observed Attack Chain (Wild — April 16, 2026)

Huntress documented the following confirmed intrusion:

```
Initial Access: Compromised SSLVPN credentials
        ↓
Reconnaissance: whoami /priv | cmdkey /list | net group | AD structure mapping
        ↓
Exploit Files: Renamed and dropped to C:\Users\[user]\Pictures\ and Downloads\
        ↓
UnDefend: Launched first — blocks Defender signature updates
        ↓
RedSun: Escalate privileges to SYSTEM
        ↓
Post-Exploitation: Credential enumeration, AD recon, lateral movement prep
        ↓
Containment: Huntress isolated the affected organization
```

The staging of UnDefend before RedSun reflects deliberate TTPs — attackers understood the interaction between the two exploits and sequenced them intentionally.

---

## Public PoC Status

| Repository | Status | Notes |
|---|---|---|
| Nightmare-Eclipse repository (GitHub) | 🔴 Public — functional | GitHub issued a warning; repo remains accessible |
| Technical writeup depth | Limited compared to BlueHammer/RedSun | Mechanism not fully reverse-engineered publicly yet |

---

## IOCs & Detection

### Behavioral Indicators

| Behavior | Event / Signal | Confidence |
|---|---|---|
| Defender signature update failures without network explanation | `MpCmdRun.exe` update tasks returning error codes repeatedly | 🔴 HIGH (in context of active threat) |
| Defender definition timestamp frozen / not advancing | Signature date unchanged across multiple update windows | 🟡 MEDIUM |
| Defender service state changes not initiated by admin | Service control events from standard user context | 🔴 HIGH |
| Exploit files in Pictures/Downloads with unusual names | File names inconsistent with user activity pattern | 🟡 MEDIUM |
| UnDefend + RedSun/BlueHammer executed within same session | Process lineage from same user context in short window | 🔴 HIGH |
| `MsMpEng.exe` or Defender platform entering unexpected states | Service crash, restart, or state enum anomalies | 🟡 MEDIUM |

### Sigma-Style Hunt Queries

```yaml
# Hunt: Defender update failures from non-network causes
Process: MpCmdRun.exe
Arguments: "*SignatureUpdate*" OR "*UpdateSignature*"
Result: ERROR OR FAILURE
Frequency: >2 failures in 10 minutes
ExcludeReason: NetworkTimeout

# Hunt: Defender definition date not advancing
Monitor: HKLM\SOFTWARE\Microsoft\Windows Defender\Signature Updates\SignatureVersion
Alert: Value unchanged over 4+ hour window when system is online

# Hunt: Defender service manipulation by standard user
EventID: 7036 OR 7040
ServiceName: WinDefend OR MsMpEng
InitiatingProcessIntegrity: Medium OR Low

# Hunt: Suspicious files in user Downloads/Pictures
Directory: "%USERPROFILE%\\Downloads\\" OR "%USERPROFILE%\\Pictures\\"
FileExtension: .exe OR .ps1 OR .bat
SignedStatus: Unsigned
TimeWindow: 30 minutes before any Defender update failure event
```

### Key Monitoring Metric

**Check Defender signature currency on all endpoints at least every 4 hours.** A sudden increase in endpoints with stale definitions — especially if correlated with other anomalous activity — should be treated as a potential UnDefend deployment indicator.

---

## Mitigations

> ⚠️ **No official patch available.** UnDefend remains unpatched as of 2026-04-27. No CVE assigned, no Microsoft timeline published. All controls below are compensating measures.

| Control | Priority | Impact |
|---|---|---|
| **Monitor Defender signature update health centrally** | 🔴 IMMEDIATE | Detect UnDefend deployment quickly before damage compounds. Alert if definitions are >4 hours stale on any endpoint. Defender for Endpoint / MDE provides centralized signature visibility. |
| **Supplement Defender with a second endpoint detection layer** | 🔴 IMMEDIATE | UnDefend specifically targets Defender. A second EDR agent not reliant on Defender's update mechanism provides continuity of coverage. |
| **Restrict local interactive logon and local execution rights** | 🔴 HIGH | UnDefend only requires a local user account. Limit what accounts can log in interactively to sensitive systems. |
| **Enforce MFA on SSLVPN / remote access** | 🔴 HIGH | The April 16 attack entered via compromised VPN credentials. Strong MFA blocks the initial access vector that enabled UnDefend deployment. |
| **Behavioral EDR rules for Defender service manipulation** | 🟡 HIGH | Alert on Defender service state changes initiated by medium/low integrity processes. |
| **Monitor for exploit files in user directories** | 🟡 HIGH | Unsigned executables appearing in Pictures/Downloads, especially proximate to Defender events. |
| **Patch immediately when Microsoft releases a fix** | ⏳ PENDING | Out-of-band patch likely before next Patch Tuesday given active exploitation. Monitor Microsoft Security Update Guide. |

---

## Threat Context

UnDefend represents a category of attack that security teams often underestimate: **capability degradation**. It doesn't generate alerts. It doesn't crash systems. It doesn't leave obvious forensic artifacts. It just quietly makes Defender less effective — and most environments don't have instrumentation that would catch a stale signature database in real time.

In a ransomware affiliate workflow, UnDefend solves a real operational problem: modern AV/EDR is increasingly good at catching LPE exploits by behavioral signature. By freezing Defender's definitions before running the LPE, the attacker reduces the probability of detection at the most critical moment in their kill chain.

The April 2026 trio — BlueHammer → patched; RedSun + UnDefend → unpatched and actively exploited — represents a rapidly maturing attack playbook against Windows Defender as an enterprise security primitive. Organizations that rely on Defender as their **sole** endpoint protection layer are at meaningful risk until patches arrive.

---

## Disclosure Timeline

| Date | Event |
|---|---|
| April 3, 2026 | Chaotic Eclipse drops BlueHammer — protest against MSRC handling |
| April 10, 2026 | BlueHammer exploitation first observed (Huntress) |
| April 14, 2026 | Microsoft patches BlueHammer (CVE-2026-33825) in April Patch Tuesday |
| April 16, 2026 | Chaotic Eclipse drops RedSun and UnDefend simultaneously to GitHub |
| April 16, 2026 | Huntress observes live UnDefend + RedSun exploitation — hands-on-keyboard, SSLVPN entry |
| April 17, 2026 | Multiple security vendors confirm active exploitation of all three |
| April 18, 2026 | Multiple security advisories published across industry |
| April 21, 2026 | Vectra AI publishes in-the-wild behavioral analysis — confirms hands-on-keyboard intrusion pattern; UnDefend deployed as child of cmd.exe under Explorer with `-agressive` flag |
| April 27, 2026 | **Still unpatched. No CVE assigned. No Microsoft timeline published.** Active exploitation ongoing alongside RedSun. |

---

## References

- [The Hacker News — Three Microsoft Defender Zero-Days Actively Exploited; Two Still Unpatched](https://thehackernews.com/2026/04/three-microsoft-defender-zero-days.html)
- [Help Net Security — Researcher drops two more Microsoft Defender zero-days, all three now exploited](https://www.helpnetsecurity.com/2026/04/17/microsoft-defender-zero-days-exploited/)
- [BleepingComputer — Recently Leaked Windows Zero-Days Now Exploited in Attacks](https://www.bleepingcomputer.com/news/security/recently-leaked-windows-zero-days-now-exploited-in-attacks/)
- [Field Effect — Three Microsoft Defender Zero-Days Reported Exploited](https://fieldeffect.com/blog/three-microsoft-defender-zero-days-reported-exploited)
- [Security Affairs — Microsoft Defender Under Attack as Three Zero-Days Enable Elevated Access](https://securityaffairs.com/190961/hacking/microsoft-defender-under-attack-as-three-zero-days-two-of-them-still-unpatched-enable-elevated-access.html)
- [Picus Security — BlueHammer & RedSun: Windows Defender CVE-2026-33825 Zero-Day Explained](https://www.picussecurity.com/resource/blog/bluehammer-redsun-windows-defender-cve-2026-33825-zero-day-vulnerability-explained)
---

## Intelligence Update — 2026-04-28

> **Status: STILL UNPATCHED.** No CVE assigned. No Microsoft patch timeline. Active exploitation ongoing as of April 28, 2026.

### Disclosure Timeline (continued)

| Date | Event |
|---|---|
| April 23, 2026 | SecurityWeek publishes more specific technical breakdown of UnDefend file-locking mechanism |
| April 23, 2026 | CISA adds CVE-2026-33825 (BlueHammer) to KEV; confirms UnDefend/RedSun remain unpatched |
| April 25, 2026 | Startup Defense analysis confirms UnDefend still unpatched; identifies Russia-geolocated source IPs in Huntress exploitation data |
| April 28, 2026 | **Still unpatched.** No CVE. No Microsoft patch timeline. |

### Technical Mechanism — Clarified (SecurityWeek, April 23)

Previous understanding characterised UnDefend as "blocking Defender's signature update mechanism." The SecurityWeek analysis provides more precise detail on the file-locking approach:

> *"UnDefend kills Defender by locking definition files. It monitors for changes to the definition updates and Microsoft's Malicious Software Removal Tool (MRT) folders to lock new files before Defender can use them, and locks backup definition files immediately after Defender's startup."*

**Mechanism breakdown:**
1. UnDefend monitors the Defender definition update directory and MRT folder for new file writes
2. When a new definition file appears, UnDefend acquires a lock on it before Defender can consume it
3. Defender's update process fails silently — no service stop, no hard error, no obvious alert
4. Backup definition files are locked immediately on Defender startup — preventing fallback recovery
5. Over time, Defender's detection coverage degrades as definitions stagnate

This is a more targeted approach than a generic service disruption — it surgically intercepts the update pipeline at the file I/O level, making it harder to detect than a traditional DoS.

### Attribution Signal (Huntress, April 23)

Huntress reported that source IPs observed in the confirmed hands-on-keyboard exploitation chain (UnDefend → RedSun) **geolocated to Russia**. This is not formal attribution — IP geolocation is trivially spoofed — but it is consistent with the pre-existing assessment that the activity pattern reflects skilled, targeted intrusion rather than commodity malware spray.

### Updated Detection Guidance

**Highest-fidelity UnDefend detection currently available:**

```powershell
# Check Defender signature currency — frozen timestamp = potential UnDefend
Get-MpComputerStatus | Select-Object AntivirusSignatureLastUpdated, AntivirusSignatureVersion

# Alert threshold: signature older than 72 hours on an online system
# Legitimate update failures (network timeout) look identical — correlate with network connectivity
```

**File lock monitoring (behavioral):**
```yaml
# Hunt: Unexpected file handle acquisition on Defender definition directories
Directory: C:\ProgramData\Microsoft\Windows Defender\Definition Updates\*
Directory: C:\Windows\System32\MRT\*
LockerProcess: NOT ["MsMpEng.exe", "WdFilter.sys", "svchost.exe (Defender service)"]
FileOperation: OpenFileExclusive
Severity: HIGH
```

---

## Intelligence Update — 2026-05-07

> **Status: STILL UNPATCHED.** No CVE assigned. No Microsoft patch timeline. Active exploitation ongoing as of May 7, 2026.
> UnDefend has been publicly available for **21 days** with no patch, no CVE, and no out-of-band advisory from Microsoft.

### Day 21 Status

| Date | Event |
|---|---|
| May 7, 2026 | **Still unpatched.** 21 days. No CVE. No KEV. No advisory. May 12 Patch Tuesday is 5 days away. |
| **May 12, 2026** | **May Patch Tuesday.** Confirmed next Microsoft release and last realistic patch window before 26-day threshold. Monitor MSRC. |

No material change from May 6. The combination of UnDefend (Defender blind + telemetry falsification) and RedSun (LPE to SYSTEM) remains one of the most operationally dangerous unpatched tool combinations in the current Windows threat landscape. Organizations relying on Defender telemetry dashboards have no reliable signal that UnDefend is active in their environment without direct PowerShell endpoint queries and secondary EDR coverage.

---

## Intelligence Update — 2026-05-06

> **Status: STILL UNPATCHED.** No CVE assigned. No Microsoft patch timeline. Active exploitation ongoing as of May 6, 2026.
> UnDefend has been publicly available for **20 days** with no patch, no CVE, and no out-of-band advisory from Microsoft.

### Disclosure Timeline (continued)

| Date | Event |
|---|---|
| May 5, 2026 | **Still unpatched.** 19 days. No material change from Microsoft. |
| May 6, 2026 | **Still unpatched.** 20 days. CISA KEV compliance deadline passes for BlueHammer (CVE-2026-33825). UnDefend has no KEV listing, no CVE, no federal mandate. |
| **May 12, 2026** | **May Patch Tuesday — confirmed.** Next Microsoft patch release and most likely window for RedSun and UnDefend fixes. Monitor MSRC. |

### CISA KEV Context

Today's CISA KEV deadline (May 6) applied only to BlueHammer, which is patched. UnDefend — despite confirmed active exploitation as an enabler in hands-on-keyboard intrusions and the presence of telemetry manipulation capabilities — has **no CVE**, **no KEV listing**, and **no federal remediation mandate** after 20 days.

This gap is operationally significant: organizations relying on CISA KEV as their primary vulnerability triage signal may not have UnDefend on their radar at all. The combination of no CVE + dashboard telemetry falsification (`-agressive` mode) + no federal mandate creates conditions for silent, durable deployment across enterprise environments.

### Threat Level Reassessment

At 20 days unpatched, UnDefend has transitioned from a tactical chaining tool to an **independent persistent threat**. Key factors driving this reassessment:

1. **Telemetry falsification** — `-agressive` mode makes MDE/SIEM dashboards show healthy endpoints while Defender is blind
2. **Cascading risk** — RedSun's LPE is significantly more effective on systems where UnDefend has degraded Defender coverage
3. **No detection pressure** — organizations without direct PowerShell endpoint queries and a second EDR layer have no reliable signal that UnDefend is active
4. **Expanding staleness** — every day without a patch widens the definition gap on any deployed UnDefend instance

### Patch Outlook

May 12 Patch Tuesday is the confirmed next Microsoft release window and the last realistic opportunity for a patch before UnDefend crosses the 26-day public exploitation threshold. If Microsoft ships a fix May 12, it will have taken significantly longer than BlueHammer's 11-day patch cycle, despite UnDefend representing equal or greater operational risk through its telemetry manipulation capabilities.

Microsoft's public posture: *"reviewing the reported issues and committed to addressing vulnerabilities through its standard security response process."* No CVE. No timeline.

---

## Intelligence Update — 2026-05-05

> **Status: STILL UNPATCHED.** No CVE assigned. No Microsoft patch timeline. Active exploitation ongoing as of May 5, 2026.
> UnDefend has been publicly available for **19 days** with no patch, no CVE, and no out-of-band advisory from Microsoft.

### Disclosure Timeline (continued)

| Date | Event |
|---|---|
| May 4, 2026 | **Still unpatched.** 18 days. No material change from Microsoft. |
| May 5, 2026 | **Still unpatched.** 19 days. No CVE, no patch, no advisory. |
| May 6, 2026 | CISA KEV deadline for FCEB agencies to patch BlueHammer (CVE-2026-33825). UnDefend has no KEV listing and no federal remediation mandate. |
| **May 12, 2026** | **May Patch Tuesday — confirmed.** Most likely window for RedSun and UnDefend patches. Monitor MSRC for advisory. |

### Threat Level Reassessment

At 19 days unpatched, UnDefend's telemetry manipulation capability (confirmed in `-agressive` mode) remains largely unknown to security operations teams relying on MDE/SIEM dashboards. The combination of:
- **Definition staleness** (silent, no alerts)
- **Health telemetry falsification** (dashboard shows healthy endpoint)
- **Stealthy file-lock mechanism** (no service stop, no hard error)

...means the majority of organizations without direct endpoint PowerShell queries have no reliable way to know if UnDefend is active in their environment. This is no longer just a chained enabler — it is an independent operational security risk that widens with every additional day unpatched. **May 12 is the last realistic opportunity for a patch before this vulnerability crosses the 26-day mark.**

Microsoft's public posture: *"reviewing the reported issues and committed to addressing vulnerabilities through its standard security response process."* No CVE assigned. No timeline.

---

## Intelligence Update — 2026-05-04

> **Status: STILL UNPATCHED.** No CVE assigned. No Microsoft patch timeline. Active exploitation ongoing as of May 4, 2026.
> UnDefend has now been publicly available for **18 days** with no patch, no CVE, and no out-of-band advisory from Microsoft.

### Disclosure Timeline (continued)

| Date | Event |
|---|---|
| May 4, 2026 | **Still unpatched.** No CVE. No Microsoft patch timeline. 18 days since public disclosure with active exploitation ongoing. |
| May 6, 2026 | CISA KEV deadline for FCEB agencies to patch BlueHammer (CVE-2026-33825). UnDefend has no KEV listing and no federal remediation mandate. |
| ~May 13, 2026 | May Patch Tuesday — next potential patch window (now confirmed as May 12). |

### Threat Level Reassessment

At 18 days unpatched, UnDefend's telemetry manipulation capability (confirmed in `-agressive` mode) remains largely unknown to security operations teams relying on MDE/SIEM dashboards. The combination of:
- **Definition staleness** (silent, no alerts)
- **Health telemetry falsification** (dashboard shows healthy endpoint)
- **Stealthy file-lock mechanism** (no service stop, no hard error)

...means the majority of organizations without direct endpoint PowerShell queries have no reliable way to know if UnDefend is active in their environment. This is no longer just a chained enabler — it is an independent operational security risk that widens with every additional day unpatched.

Microsoft's public posture: *"reviewing the reported issues and committed to addressing vulnerabilities through its standard security response process."* No timeline.

---

## Intelligence Update — 2026-04-30

> **Status: STILL UNPATCHED.** No CVE assigned. No Microsoft patch timeline. Active exploitation ongoing as of April 30, 2026.

### Disclosure Timeline (continued)

| Date | Event |
|---|---|
| April 29, 2026 | Ayinedjimi Consultants (FR) publishes analysis confirming UnDefend + RedSun both unpatched; notes no official workaround for UnDefend exists |
| April 30, 2026 | **Still unpatched.** No CVE. No Microsoft patch timeline. 14 days since public disclosure with active exploitation ongoing. |

### Updated Technical Understanding — Telemetry Manipulation (April 30)

Additional analysis has clarified a third capability within UnDefend beyond definition locking that was not fully documented in prior intelligence updates:

**UnDefend manipulates health telemetry sent to MDE and SIEM dashboards.**

In its aggressive mode (`-agressive` flag, confirmed in Huntress observations), UnDefend interferes with the health status data Defender reports upward to:
- Microsoft Defender for Endpoint (MDE) console
- Connected SIEM platforms (Sentinel, Splunk, etc.)
- Windows Security Center

**Practical impact:** Security operations consoles display a **healthy, up-to-date endpoint** while Defender's definitions are frozen and its detection coverage is degraded. This is a deliberate anti-detection layer — the gap between actual protection and reported protection is invisible to analysts relying on dashboard telemetry alone.

This elevates UnDefend’s threat profile beyond a simple DoS. It is an **active deception tool** that defeats the monitoring used to detect Defender degradation.

### Critical Detection Note

Given telemetry manipulation, dashboard-based Defender health monitoring **cannot be trusted** as the sole detection method for UnDefend. The highest-confidence detection remains direct endpoint query via PowerShell:

```powershell
# Run directly on endpoint or via PSRemoting/Intune — do NOT rely on MDE/SIEM dashboard reporting
Get-MpComputerStatus | Select-Object ComputerName, AntivirusSignatureLastUpdated, AntivirusSignatureVersion, RealTimeProtectionEnabled

# Alert threshold: AntivirusSignatureLastUpdated older than 72h on an online, active system
# Cross-reference against network connectivity logs to rule out legitimate update failures
```

### Best Available Compensating Controls (as of April 30)

| Control | Priority | Notes |
|---|---|---|
| **Direct endpoint PowerShell signature query** | 🔴 IMMEDIATE | Do not rely on MDE/SIEM dashboard alone — telemetry may be falsified by UnDefend `-agressive` mode |
| **Second EDR layer** | 🔴 IMMEDIATE | Primary gap; any EDR not reliant on Defender’s update mechanism maintains independent coverage |
| **Alert: Defender definition age > 72h** | 🔴 HIGH | Via direct endpoint query; treat as potential active UnDefend in context of other threat signals |
| **Monitor Defender definition directories for unexpected file locks** | 🟡 HIGH | Highest-fidelity behavioral detection available |
| **Block execution from Downloads/Pictures** | 🟡 HIGH | UnDefend is staged in same user-writable directories as RedSun |

### Patch Outlook

UnDefend has been publicly available for 14 days with confirmed active exploitation and no patch. The telemetry manipulation capability — now more fully understood — makes this a more serious enterprise risk than initially assessed. An out-of-band Microsoft patch remains expected but unconfirmed. Monitor the Windows Security Update Guide and MSRC for emergency advisories.

---

## Intelligence Update — 2026-05-08

### Status: STILL UNPATCHED — Day 23 | May 12 Patch Tuesday Is the Next Window

As of May 8, UnDefend remains unpatched with no CVE assigned, mirroring RedSun’s status. Both vulnerabilities are expected to be addressed on **May 12, 2026 Patch Tuesday** if Microsoft maintains its scheduled cycle.

UnDefend continues to be deployed **as a pre-exploitation layer alongside RedSun** in confirmed intrusions. The attack flow observed in the wild:
1. UnDefend degrades Defender detection capability and falsifies telemetry
2. RedSun executes LPE to SYSTEM under reduced detection coverage
3. Post-exploitation (credential dump, persistence, lateral movement) proceeds with limited EDR visibility

**Day count:** UnDefend public disclosure April 15, 2026 → May 8 = **23 days unpatched**.

### Elevated Risk: Telemetry Gap Still Active

The most operationally significant aspect of UnDefend remains the `-aggressive` mode’s ability to falsify Defender health telemetry. Security teams relying on MDE dashboards for endpoint health are still flying blind on any system where UnDefend has been deployed. **Direct endpoint PowerShell query (not dashboard) remains the only reliable detection path** — see detection section above.

### May 12 Expectations

Same as RedSun: watch MSRC for Defender platform updates. If no fix ships May 12, UnDefend will have been exploited in the wild for 30+ days without a patch — a significant enough gap that CISA KEV listing becomes likely.

*No change to mitigations or detection guidance from April 30 update.*

---

## Intelligence Update — 2026-05-11

### Status: STILL UNPATCHED — Day 25 | Patch Tuesday Is TOMORROW

UnDefend remains unpatched at **25 days**, mirroring RedSun exactly. **May 12 Patch Tuesday is tomorrow.**

UnDefend continues to be deployed as the **first stage in the RedSun kill chain** in confirmed intrusions:
1. UnDefend degrades Defender detection + falsifies health telemetry
2. RedSun executes LPE to SYSTEM under reduced visibility
3. Post-exploitation proceeds with limited EDR coverage

**Telemetry gap still active.** Any system where UnDefend has been run continues to report false-healthy Defender status to MDE dashboards. Direct PowerShell endpoint queries remain the only reliable detection path.

**If no fix ships May 12:** UnDefend crosses 30 days unpatched on May 15. At that threshold, both RedSun and UnDefend become strong CISA KEV candidates. The pair's continued exploitation in the wild without a patch would be notable enough for a joint CISA advisory.

| Date | Milestone |
|---|---|
| April 16, 2026 | UnDefend public PoC + active exploitation confirmed |
| May 8, 2026 | Day 23 — no patch |
| May 11, 2026 | Day 25 — PT eve |
| **May 12, 2026** | **Patch Tuesday — expected fix** |
| May 15, 2026 | Day 29 — if no fix: CISA KEV watch; escalate |

---

### 🔴 Status Update — Day 27 | May 12 Patch Tuesday SHIPPED — NO FIX | 2026-05-13

> **UnDefend has been publicly exploitable for 27 days. May 12 Patch Tuesday shipped without a fix.**

May 12 Patch Tuesday released 138 CVE-numbered vulnerabilities. Multiple analysis sources confirm **none of the included vulnerabilities are actively exploited or publicly disclosed** — meaning UnDefend was not addressed. UnDefend has now survived a complete Patch Tuesday cycle with no patch, no CVE, and no KEV listing.

The telemetry falsification risk compounds daily — endpoints running UnDefend continue to report healthy Defender status to MDE dashboards. Dashboard-based monitoring remains unreliable; direct PowerShell endpoint queries are the only reliable detection path.

**Day 30 threshold:** May 15, 2026 — CISA KEV listing and joint advisory with RedSun become highly probable.

| Date | Milestone |
|---|---|
| April 16, 2026 | UnDefend public PoC + active exploitation confirmed |
| May 11, 2026 | Day 25 — PT eve |
| May 12, 2026 | **Patch Tuesday shipped — NO FIX for UnDefend** |
| May 13, 2026 | Day 27 — this update |
| May 15, 2026 | Day 29 — CISA KEV watch threshold reached |
| May 18, 2026 | **Day 32 — this update; MiniPlasma released by same researcher** |
| ~June 9, 2026 | June Patch Tuesday — next scheduled window if no OOB patch |

---

### 🔴 Status Update — Day 32 | CISA KEV Threshold Passed | 2026-05-18

> **UnDefend has been publicly exploitable for 32 days with no patch, no CVE assignment, and no CISA KEV listing.**

**Day 30 threshold (May 15) has passed** with no CISA KEV listing. No out-of-band patch. Telemetry falsification risk continues to compound — affected endpoints continue reporting healthy Defender status to MDE dashboards while definition updates are blocked. Direct endpoint queries via `Get-MpComputerStatus` checking `AntivirusSignatureLastUpdated` remain the only reliable detection path.

**Researcher escalation:** Nightmare-Eclipse released **MiniPlasma** (~May 14–16, 2026) as their sixth disclosed tool, confirming continued active pressure against Microsoft. The RedSun/UnDefend pair remains the most dangerous combination in the researcher's catalog — LPE to SYSTEM (RedSun) + AV blinding (UnDefend) — and both remain unpatched.

**Next window:** June Patch Tuesday (~June 9, 2026).

*Profile updated: 2026-05-18 | Author: C3PO | TLP: WHITE*
