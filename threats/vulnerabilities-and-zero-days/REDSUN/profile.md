# RedSun — Windows Defender Zero-Day Local Privilege Escalation

## Identity

| Field | Details |
|---|---|
| **Vulnerability Name** | RedSun |
| **CVE** | None assigned — **UNPATCHED as of 2026-04-21** |
| **Type** | Local Privilege Escalation (LPE) |
| **Class** | Defender Cloud-File Remediation Path Abuse — Standard User Privilege |
| **Affected Platforms** | Windows 10, Windows 11, Windows Server 2019 and later |
| **Patch Status** | 🔴 **UNPATCHED** — No official patch, no CVE, no Microsoft timeline |
| **PoC Status** | 🔴 **PUBLIC** — Full C++ source on GitHub (Nightmare-Eclipse/RedSun) |
| **Discovered By** | Chaotic Eclipse / Nightmare-Eclipse (pseudonymous researcher) |
| **Public Disclosure** | April 16, 2026 (released simultaneously with UnDefend) |
| **Confirmed Working** | Will Dormann (independent validation); Huntress (in-the-wild exploitation) |
| **Exploited in Wild** | ✅ YES — confirmed April 16, 2026 alongside BlueHammer and UnDefend |
| **Reliability** | ~100% on fully patched Windows 10/11 and Server 2019+ |
| **Threat Level** | 🔴 HIGH |
| **Admiralty Grade** | A1 |

---

## Overview

RedSun is the second local privilege escalation zero-day in Chaotic Eclipse's April 2026 trio, dropped alongside UnDefend on April 16 — five days after Microsoft patched the first exploit (BlueHammer, CVE-2026-33825). It achieves the same outcome as BlueHammer — a standard user account reaches SYSTEM — but via an entirely different attack path, making it effective even on systems where BlueHammer was patched.

**BlueHammer** abused Defender's *signature update path* to perform a privileged **file read** — dumping the SAM hive, extracting NTLM hashes, then using pass-the-hash to escalate.

**RedSun** abuses Defender's *cloud-tagged file remediation path* to perform a privileged **file write** — dropping an attacker-controlled binary directly into `C:\Windows\System32`, then triggering a built-in Windows COM service to execute it as SYSTEM. No credentials are accessed or stolen. The escalation is a straight line: file write, COM activation, SYSTEM shell.

The exploit requires no kernel bugs, no memory corruption, no admin rights, no internet connection, and no user interaction. It works on every supported Windows version with Defender real-time protection enabled — which is essentially every Windows machine.

---

## Technical Analysis

### Four Windows Features, One Vulnerability

RedSun chains four legitimate, individually benign Windows components together. The vulnerability emerges from their interaction, not from any single flaw:

**1. Batch Opportunistic Locks (OPLOCKs)**
An oplock lets a process pause another process's file access at a precise moment. RedSun uses a batch oplock as a timing instrument — it freezes Defender mid-operation at a deterministic, reproducible point, creating a controlled race window rather than a timing-dependent one.

**2. Cloud Files API (CfApi.dll)**
The Cloud Files API provides the infrastructure for sync providers like OneDrive. RedSun uses it to create a placeholder file — a lightweight stub that keeps a filename valid even after the original file is deleted. This holds the target path open through the directory swap.

**3. Volume Shadow Copy Service (VSS)**
When Defender detects a cloud-tagged malicious file, it creates a VSS snapshot as part of its remediation workflow. RedSun monitors the NT object directory for a new `HarddiskVolumeShadowCopy*` device appearing — confirming Defender has entered the remediation stage — before proceeding.

**4. NTFS Junction Points (Mount Point Reparse)**
NTFS junctions redirect filesystem operations from one directory to another. Critically, standard users can create them without elevated privileges. RedSun replaces its working directory with a junction pointing to `C:\Windows\System32`. Any write through the original path is silently redirected into System32 by the kernel — Defender has no awareness of the redirection.

### Exploit Walkthrough

**Phase 1 — Getting the File Write**

1. Create a temp working directory under `%TEMP%`; drop a file named `TieringEngineService.exe` containing an EICAR test string (stored reversed at compile time, flipped at runtime to avoid static detection)
2. Open the file with `FILE_EXECUTE` access — sufficient to trigger Defender's real-time scanner
3. Acquire a batch oplock on the file. When Defender's remediation workflow accesses the file, the oplock breaks — pausing Defender at that precise moment
4. Within that window: delete the original file (POSIX semantics via `FileDispositionInformationEx`), register the directory as a Cloud Files sync root, create a placeholder file with the same name to keep the path valid
5. Rename the working directory away; create a new directory with the same name; convert it to an NTFS junction pointing to `\\??\C:\Windows\System32`
6. Loop `NtCreateFile` calls with `GENERIC_WRITE / FILE_SUPERSEDE` against `TieringEngineService.exe` until the race is won. On success, copy the exploit's own binary to `C:\Windows\System32\TieringEngineService.exe`

Defender resumes its write operation, follows the junction, and writes the attacker-controlled binary into System32 under SYSTEM privileges. **No reparse point validation exists in `MpSvc.dll`'s write-back path.** A single call to `DeviceIoControl(FSCTL_GET_REPARSE_POINT)` or `GetFinalPathNameByHandle` before the write would have prevented this.

**Phase 2 — Executing as SYSTEM**

1. Activate CLSID `{50d185b9-fff3-4656-92c7-e4018da4361d}` via DCOM — the Storage Tiers Management Engine COM server
2. This COM object's normal behavior is to launch `TieringEngineService.exe` from System32 as SYSTEM
3. Since the binary has been replaced, Windows launches the attacker's payload instead
4. On second execution, the payload confirms `LOCAL_SYSTEM` context, connects to named pipe `\\.\pipe\REDSUN` created during Phase 1, duplicates the SYSTEM token into the user's session, and spawns `conhost.exe` on the user's desktop — an interactive SYSTEM shell

No credentials stolen. No memory corruption. No kernel exploit. Standard Windows API calls throughout.

### Root Cause

The vulnerability lives in `MpSvc.dll` — the Malware Protection Engine binary that `MsMpEng.exe` loads for all scan, detection, and remediation logic. When Defender detects a cloud-tagged malicious file, it restores the file to its original detection path without verifying whether that path has been replaced with a reparse point. The write-back chain performs no junction/reparse validation at any stage.

---

## Affected Systems

| Platform | Status |
|---|---|
| Windows 10 (all supported builds) | ✅ Vulnerable |
| Windows 11 (all supported builds, incl. 25H2) | ✅ Vulnerable |
| Windows Server 2019 | ✅ Vulnerable |
| Windows Server 2022 | ✅ Vulnerable |
| Windows Server 2025 | ✅ Vulnerable |

**Requirements:** Microsoft Defender real-time protection enabled + `cldapi.dll` present (standard on all supported Windows versions). Local user account only — no elevation needed.

**Does NOT require:** Internet connection, admin rights, kernel exploit, memory corruption, user interaction.

---

## Relationship to BlueHammer and UnDefend

| Tool | Attack Path | Primitive | Patch Status |
|---|---|---|---|
| BlueHammer | Defender signature update path | Privileged file **read** → SAM dump → NTLM hash → SYSTEM | ✅ Patched (CVE-2026-33825, April 14) |
| RedSun | Defender cloud-file remediation path | Privileged file **write** → System32 binary → COM execution → SYSTEM | ❌ **Unpatched** |
| UnDefend | Defender update mechanism | Blocks signature updates; Defender runs blind | ❌ **Unpatched** |

**Chained kill chain (observed April 16):**
```
Initial access (stolen VPN credential)
  → Reconnaissance: whoami /priv, cmdkey /list, net group, AD mapping
  → UnDefend: freeze Defender signatures → AV blind to exploit binaries
  → RedSun: LPE → SYSTEM
  → Post-exploitation: credential harvesting, lateral movement prep
  → Containment: Huntress isolated affected org
```

---

## IOCs and Detection

### File / Process Indicators

| Indicator | Description | Confidence |
|---|---|---|
| `TieringEngineService.exe` replaced in `C:\Windows\System32` | Core payload delivery artifact | 🔴 HIGH |
| `RedSun.exe` or renamed variants in `%USERPROFILE%\Pictures\` or `Downloads\` | Staging location observed in wild | 🔴 HIGH |
| EICAR string (or obfuscated reverse) in binaries dropped to user directories | Exploit trigger mechanism | 🟡 MEDIUM |
| Named pipe `\\.\pipe\REDSUN` created | Phase 2 communication channel | 🔴 HIGH |
| New `HarddiskVolumeShadowCopy*` device from user-space process | VSS monitoring step of exploit | 🔴 HIGH |
| `CfRegisterSyncRoot` called by non-OneDrive/non-sync process | Cloud Files API abuse | 🔴 HIGH |
| NTFS junction created in `%TEMP%` pointing to `System32` | Directory swap step | 🔴 HIGH |
| `MsMpEng.exe` writing to `System32` paths | Defender performing redirected write | 🔴 HIGH |
| COM activation of CLSID `{50d185b9-fff3-4656-92c7-e4018da4361d}` from non-admin session | Phase 2 execution trigger | 🔴 HIGH |

### MITRE ATT&CK Mapping

| Technique | ID | Description |
|---|---|---|
| Exploitation for Privilege Escalation | T1068 | LPE from standard user to SYSTEM |
| Abuse Elevation Control Mechanism | T1548 | Redirecting SYSTEM-privileged Defender write |
| Hijack Execution Flow: Services File Permissions Weakness | T1574.010 | Replacing TieringEngineService.exe |
| Hide Artifacts: NTFS File Attributes | T1564.004 | Junction/reparse point manipulation |
| Masquerading | T1036 | Exploit binaries renamed to avoid suspicion |

### Detection Rules (Behavioral)

```yaml
# Alert: TieringEngineService.exe written to by MsMpEng
EventType: FileWrite
TargetPath: "C:\\Windows\\System32\\TieringEngineService.exe"
WritingProcess: MsMpEng.exe
Severity: CRITICAL

# Alert: VSS Shadow Copy enumeration from user-space
EventType: ProcessAccess
TargetObject|contains: "HarddiskVolumeShadowCopy"
CallerIntegrity: Medium OR Low
ExcludeProcess: vssadmin.exe, wbadmin.exe, svchost.exe
Severity: HIGH

# Alert: Cloud Files sync root registered by untrusted process
Function: CfRegisterSyncRoot
CallerPath|not contains: OneDrive, Dropbox, Box, iCloudDrive
Severity: HIGH

# Alert: NTFS junction in %TEMP% pointing to System32
EventType: JunctionCreated
SourcePath|contains: "%TEMP%"
TargetPath|contains: "System32"
Severity: CRITICAL

# Alert: Storage Tiers COM object activated by non-admin
CLSID: 50d185b9-fff3-4656-92c7-e4018da4361d
CallerIntegrity: Medium OR Low
Severity: HIGH

# Alert: Unsigned executable dropped to Pictures or Downloads
Directory: "%USERPROFILE%\\Pictures\\" OR "%USERPROFILE%\\Downloads\\"
FileExtension: .exe
Signed: false
Severity: MEDIUM (escalate if correlated with above)
```

---

## Mitigations

> ⚠️ **No patch available.** All controls below are compensating measures until Microsoft releases a fix.

| Control | Priority | Notes |
|---|---|---|
| **Deploy behavioral detection rules above** | 🔴 IMMEDIATE | VSS enumeration + junction creation + MsMpEng write = high-confidence chain |
| **Monitor File Integrity on System32** | 🔴 IMMEDIATE | Alert on unexpected changes to `TieringEngineService.exe` specifically |
| **Enforce phishing-resistant MFA on VPN/remote access** | 🔴 HIGH | Observed attack entered via stolen VPN credential — this is the first gate |
| **Restrict execution from user-writable directories** | 🔴 HIGH | AppLocker / WDAC policy blocking .exe from Downloads, Pictures, Temp |
| **Supplement Defender with secondary EDR** | 🔴 HIGH | RedSun + UnDefend specifically target Defender; second layer maintains coverage |
| **Least-privilege enforcement** | 🟡 HIGH | Reduces post-exploitation blast radius; RedSun starts from standard user context |
| **Apply patch immediately when released** | ⏳ WATCH | Out-of-band Microsoft patch expected; monitor MSRC for emergency advisory |

---

## Disclosure Timeline

| Date | Event |
|---|---|
| April 3, 2026 | Chaotic Eclipse releases BlueHammer — protest against MSRC |
| April 10, 2026 | BlueHammer exploitation observed in the wild (Huntress) |
| April 14, 2026 | Microsoft patches BlueHammer (CVE-2026-33825, April Patch Tuesday) |
| April 16, 2026 | Chaotic Eclipse releases RedSun + UnDefend to GitHub |
| April 16, 2026 | Will Dormann independently confirms RedSun PoC effectiveness |
| April 16, 2026 | Huntress observes live chained exploitation (UnDefend → RedSun) |
| April 17, 2026 | CloudSEK, Cyderes, Help Net Security, The Hacker News publish detailed analyses |
| April 21, 2026 | **Still unpatched. No CVE. No Microsoft timeline. Active exploitation ongoing.** |

---

## References

- [Cyderes — RedSun Zero-Day: When Defender Becomes the Delivery Mechanism](https://www.cyderes.com/howler-cell/redsun-zero-day)
- [CloudSEK — RedSun: Windows 0day when Defender becomes the attacker](https://www.cloudsek.com/blog/redsun-windows-0day-when-defender-becomes-the-attacker)
- [The Hacker News — Three Microsoft Defender Zero-Days Actively Exploited](https://thehackernews.com/2026/04/three-microsoft-defender-zero-days.html)
- [Help Net Security — Researcher drops two more Microsoft Defender zero-days](https://www.helpnetsecurity.com/2026/04/17/microsoft-defender-zero-days-exploited/)
- [Black Swan Cybersecurity — Threat Advisory: RedSun Zero-Day](https://blackswan-cybersecurity.com/threat-advisory-redsun-zero-day-windows-defender-april-17-2026/)
- [Picus Security — BlueHammer & RedSun: Windows Defender CVE-2026-33825 Explained](https://www.picussecurity.com/resource/blog/bluehammer-redsun-windows-defender-cve-2026-33825-zero-day-vulnerability-explained)
- [ProArch — Microsoft Defender Zero-Day Vulnerabilities (BlueHammer, RedSun & UnDefend)](https://www.proarch.com/blog/threats-vulnerabilities/microsoft-defender-zero-days-bluehammer-redsun-undefend)
---

## Intelligence Update — 2026-04-27

> **Status: STILL UNPATCHED.** No CVE assigned. No Microsoft patch timeline. Active exploitation ongoing as of April 27, 2026.

### Disclosure Timeline (continued)

| Date | Event |
|---|---|
| April 22, 2026 | Qualys publishes VMDR detection (QID 92382); TruRisk Eliminate mitigation available |
| April 22, 2026 | Vectra AI publishes in-the-wild behavioral analysis — hands-on-keyboard intrusion pattern confirmed |
| April 27, 2026 | **Still unpatched.** RedSun works on fully patched April 2026 Windows systems |

### New Mitigation Option (Qualys — April 22)

Qualys TruRisk Eliminate has added a targeted compensating control: **disable the Cloud Files Mini Filter service**. This prevents the Windows Cloud Files platform from loading and blocks cloud file placeholder/on-demand hydration functionality — directly cutting off RedSun's exploitation path.

**Trade-off:** Disabling this service breaks OneDrive Files On-Demand and other OS-level cloud file system integrations.

Detection via Qualys VMDR QQL query:
```
vulnerabilities.vulnerability.qid:92382
```

### In-the-Wild Behavioral Pattern (Vectra AI — April 21)

Huntress and Vectra AI analysis confirms RedSun exploitation is **not automated spray** — this is targeted, hands-on-keyboard intrusion:

- Manual enumeration commands executed before exploitation (`whoami /priv` observed consistently)
- Exploit binaries staged deliberately in **low-noise user directories** (Pictures, Downloads)
- Child processes spawned under `Explorer.exe` to blend with normal user activity
- Pattern: **deliberate, targeted intrusion — not commodity malware**

**Observable anomalies at the network layer** (detectable even when endpoint EDR is compromised):
- SYSTEM-level process execution following Defender remediation activity in user directories
- Unusual outbound connections initiated from newly elevated SYSTEM session
- Anomalous privilege elevation correlated with process-level EDR signals
- Defender signature update suppression (UnDefend) preceding RedSun deployment

### Analyst Note

Two of the three Defender exploits (RedSun + UnDefend) remain unpatched with no vendor timeline. The combination creates a durable offensive window: escalate to SYSTEM via RedSun, blind Defender via UnDefend. Organizations relying solely on Defender for endpoint detection have a genuine gap — independent network-layer visibility is the primary compensating control until patches arrive.
