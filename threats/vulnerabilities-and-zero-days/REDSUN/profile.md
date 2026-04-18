# RedSun — Windows Defender Zero-Day LPE

## Identity

| Field | Details |
|---|---|
| **Vulnerability Name** | RedSun |
| **CVE** | None assigned — **UNPATCHED as of 2026-04-18** |
| **Type** | Local Privilege Escalation (LPE) |
| **Class** | Cloud Files API Abuse + NTFS Junction Redirect + TOCTOU Race Condition |
| **Affected Platforms** | Windows 10, Windows 11, Windows Server 2019 and later (fully patched, April 2026) |
| **Patch Status** | 🔴 **UNPATCHED** — No official patch, no CVE, no Microsoft timeline |
| **PoC Status** | 🔴 **PUBLIC** — Full source code on GitHub (Nightmare-Eclipse/RedSun) |
| **Discovered By** | Chaotic Eclipse / Nightmare Eclipse (pseudonymous researcher) |
| **Public Disclosure** | April 16, 2026 (uncoordinated — released in protest of MSRC handling) |
| **Confirmed Working** | Will Dormann (Tharros) — confirmed 100% reliable SYSTEM-level access |
| **Exploited in Wild** | ✅ YES — Huntress observed active exploitation April 16, 2026 |
| **Threat Level** | 🔴 HIGH |
| **Admiralty Grade** | A1 — confirmed working by independent credible researcher; active exploitation verified by Huntress |

---

## Overview

RedSun is the second zero-day in a trio dropped by "Chaotic Eclipse" in April 2026, released on April 16 — the same day as UnDefend — two days after Microsoft patched BlueHammer as CVE-2026-33825. The timing was pointed: proof that patching one vulnerability doesn't close the systemic architectural problems in Defender's privileged file operation workflows.

What makes RedSun particularly dangerous is that it works on **fully patched Windows systems with April 2026 updates applied**. Patching BlueHammer did nothing to stop RedSun. It targets a completely different, adjacent flaw in Defender's cloud file rollback mechanism — specifically, what happens when Defender identifies a file that has a cloud tag and attempts to restore it to its original location.

The answer to what happens: Defender rewrites the file without validating where that file's original location actually points. That trust, combined with the Windows Cloud Files API, an oplock, a VSS shadow copy race, and an NTFS junction redirect, produces a clean, reliable path to SYSTEM.

Will Dormann confirmed to BleepingComputer: *"This works 100% reliably to go from unprivileged user to SYSTEM against Windows 11 and Windows Server with April 2026 updates, as well as Windows 10, as long as you have Windows Defender enabled."*

The same day it was released, Huntress observed real-world exploitation of RedSun alongside BlueHammer and UnDefend in a confirmed hands-on-keyboard intrusion via a compromised SSLVPN account.

---

## Technical Analysis

### The Kill Chain

RedSun chains three mechanisms to force Defender to overwrite an arbitrary privileged system binary with attacker-controlled content, then trigger its execution as SYSTEM:

```
1. BAIT: Drop EICAR test string to a Cloud Files API-registered file
         (Defender sees it as malicious and begins remediation)
        ↓
2. TAG: File carries a cloud tag — Defender's rollback logic activates,
        attempting to "restore" the file to its original location
        ↓
3. RACE: Oplock + VSS shadow copy race is used to pause Defender
         mid-operation while the attack repositions
        ↓
4. REDIRECT: NTFS junction / directory reparse point swaps the
             target path → C:\Windows\System32\TieringEngineService.exe
        ↓
5. OVERWRITE: Defender follows the redirect and rewrites
              TieringEngineService.exe with attacker payload (RedSun.exe itself)
              under its own SYSTEM-level privileges
        ↓
6. EXECUTE: Windows Cloud Files Infrastructure invokes the newly
            planted TieringEngineService.exe — as SYSTEM
        ↓
7. SHELL: Attacker has SYSTEM. Game over.
```

### Step-by-Step Breakdown

**Step 1 — Craft and place the bait file**
RedSun registers a directory as a **Cloud Files sync root** using `CfRegisterSyncRoot`. It places a file containing an embedded EICAR test string within the sync root and tags it with a cloud placeholder attribute via the Cloud Files API. To Defender, this looks like a malicious file that originated from a cloud-sync location.

**Step 2 — Trigger Defender's cloud file rollback**
Defender's real-time protection detects the EICAR content. Upon recognizing that the file carries a cloud tag, Defender's remediation logic invokes its **cloud file rollback path** — a code branch designed to restore cloud-tagged files to their original sync location without re-scanning the destination. Critically, it does not validate whether the stated "original location" is a system-privileged directory.

**Step 3 — Win the race (oplock + VSS)**
Before Defender can complete the rollback write, RedSun deploys an opportunistic lock (oplock) on a file Defender must traverse and races to create a Volume Shadow Copy snapshot. This pauses Defender mid-operation — identical timing mechanism to BlueHammer — giving the exploit a controlled window to manipulate the filesystem.

**Step 4 — Redirect via NTFS junction**
With Defender frozen waiting on the oplock release, RedSun creates an **NTFS directory junction** (reparse point) that redirects Defender's target write path from the attacker-controlled sync directory to:
```
C:\Windows\System32\TieringEngineService.exe
```
`TieringEngineService.exe` is a legitimate Windows Storage Tier service binary. It is chosen specifically because it runs under SYSTEM context and the Cloud Files Infrastructure will invoke it after the operation completes.

**Step 5 — Overwrite with attacker payload**
RedSun releases the oplock. Defender resumes, follows the junction, and overwrites `TieringEngineService.exe` with the contents of the malicious file — which is, in the released PoC, the `RedSun.exe` binary itself. Defender performs this write using its own SYSTEM-level privileges.

**Step 6 — SYSTEM execution**
The Windows Cloud Files Infrastructure, as part of its own post-operation processing, invokes `TieringEngineService.exe`. It now executes the attacker's binary as **NT AUTHORITY\SYSTEM**.

**Step 7 — Cleanup (VirusTotal evasion note)**
The original PoC embeds EICAR as a plaintext string, which causes AV detection. Dormann noted that encrypting the EICAR string within the binary substantially reduces detections — making the PoC easily weaponizable with minimal modification.

### Relationship to BlueHammer

Both BlueHammer and RedSun exploit the same **fundamental design flaw** in Defender: it performs privileged file write operations without validating the target path at the time of the write. The difference is the trigger:
- **BlueHammer**: Abuses Defender's *malware remediation* path (file delete/overwrite during cleanup)
- **RedSun**: Abuses Defender's *cloud file rollback* path (file restoration for cloud-tagged content)

Patching BlueHammer did not fix RedSun because they are separate code paths within the same architectural pattern.

---

## Affected Systems

| Platform | Exploitation Result | Notes |
|---|---|---|
| Windows 10 (all builds) | NT AUTHORITY\SYSTEM | Confirmed — including with April 2026 patches |
| Windows 11 (all builds) | NT AUTHORITY\SYSTEM | Confirmed — 100% reliable per Dormann |
| Windows Server 2019+ | NT AUTHORITY\SYSTEM | Confirmed |

**Requires:** Local access + Windows Defender enabled (Cloud-Delivered Protection may or may not be required — not fully characterized)

**Does NOT work against:** Systems with Windows Defender fully disabled or replaced by a third-party AV that does not use Defender's cloud file rollback logic

---

## Observed Attack Chain (Wild — April 16, 2026)

Huntress documented the following attack pattern in a confirmed intrusion:

```
Initial Access: Compromised SSLVPN credentials
        ↓
Enumeration: whoami /priv | cmdkey /list | net group | AD structure mapping
        ↓
UnDefend: Deployed to block Defender definition updates (blind the AV)
        ↓
RedSun: Escalate to SYSTEM
        ↓
Post-exploitation: Credential access, lateral movement (ongoing — Huntress isolated org)
```

Attackers renamed the exploit files to blend into Pictures and Downloads folders before execution. The hands-on-keyboard activity and SSLVPN initial access vector are consistent with ransomware affiliate TTPs.

---

## Public PoC Status

| Repository | Status | Notes |
|---|---|---|
| Nightmare-Eclipse/RedSun (GitHub) | 🔴 Public — functional | GitHub issued a warning but repo remains accessible |
| VirusTotal detection rate | Low — reduced further by EICAR encryption | Easy to evade with minimal modification |

---

## IOCs & Detection

### Behavioral Indicators (High Confidence)

| Behavior | Event / Signal | Confidence |
|---|---|---|
| Cloud Files sync root registration by untrusted process | `CfRegisterSyncRoot` from non-OneDrive/Dropbox/Box process | 🔴 HIGH |
| `TieringEngineService.exe` written or modified by `MsMpEng.exe` (Defender) | File write event from Defender process to storage service binary | 🔴 HIGH |
| NTFS junction creation in temp/user directories | Reparse point creation in non-standard locations | 🔴 HIGH |
| `TieringEngineService.exe` spawning a shell or unexpected child process | Process creation from storage service binary | 🔴 HIGH |
| Low-privileged process receiving SYSTEM token via Cloud Files Infrastructure | Token integrity change event | 🔴 HIGH |
| VSS enumeration from user-space (same as BlueHammer) | `NtQueryDirectoryObject` → `HarddiskVolumeShadowCopy*` | 🟡 MEDIUM |
| EICAR-containing binary in user directories (Pictures, Downloads) | AV/EDR detection of EICAR + anomalous filename | 🟡 MEDIUM |

### Sigma-Style Hunt Queries

```yaml
# Hunt: Cloud Files sync root registered by non-standard process
Process: CfRegisterSyncRoot
CallerProcess: NOT ["OneDrive.exe", "Dropbox.exe", "Box.exe", "iCloudDrive.exe"]
UserIntegrity: Medium OR Low

# Hunt: Defender writing to system32 service binary
SourceProcess: MsMpEng.exe
TargetFile: "C:\\Windows\\System32\\TieringEngineService.exe"
EventType: FileModified OR FileCreated

# Hunt: TieringEngineService.exe spawning unexpected children
ParentProcess: TieringEngineService.exe
ChildProcess: ANY [cmd.exe, powershell.exe, net.exe, whoami.exe]

# Hunt: NTFS junction in user profile directories
EventType: ReparsePointCreated
Directory: "%USERPROFILE%\\*"
CallerIntegrity: Low OR Medium
```

---

## Mitigations

> ⚠️ **No official patch available.** RedSun remains unpatched as of 2026-04-18. All controls below are compensating measures only.

| Control | Priority | Impact |
|---|---|---|
| **Disable Cloud-Delivered Protection / Automatic Sample Submission in Defender** | 🔴 IMMEDIATE | Disrupts Defender's cloud file rollback path — the core trigger for RedSun. Trade-off: reduces Defender's cloud intelligence. Evaluate based on environment. |
| **Enable Credential Guard** | 🔴 IMMEDIATE | Blocks downstream abuse of the SYSTEM token for credential extraction from SAM/LSASS. |
| **Deploy Microsoft LAPS** | 🔴 IMMEDIATE | Limits lateral movement even if SYSTEM is achieved on one node. |
| **Behavioral EDR rules (TieringEngineService.exe, CfRegisterSyncRoot)** | 🔴 HIGH | Block or alert on reparse point creation in user dirs + non-standard Cloud Files API usage. |
| **Application Allow-listing (WDAC/AppLocker)** | 🟡 HIGH | Prevents the overwritten TieringEngineService.exe from executing attacker payload. |
| **Monitor SSLVPN / remote access for compromised credentials** | 🟡 HIGH | Initial access in observed attacks was via compromised VPN credentials — tighten MFA enforcement. |
| **Restrict local interactive logon rights** | 🟡 HIGH | RedSun requires local access. Limit who can log in interactively to sensitive systems. |
| **Patch immediately when Microsoft releases a fix** | ⏳ PENDING | Monitor Microsoft Security Update Guide and Zero Day Initiative continuously. Out-of-band patch expected before next Patch Tuesday. |

---

## Threat Context

RedSun, when paired with UnDefend, forms a **two-stage endpoint bypass**: UnDefend blinds Defender's detection capability (blocking signature updates), then RedSun escalates to SYSTEM. This combination was confirmed in the wild on April 16 against an organization breached via SSLVPN.

The attack pattern is consistent with ransomware affiliate pre-encryption reconnaissance:
- Credential enumeration (`whoami /priv`, `cmdkey /list`)
- Active Directory mapping (`net group`)
- LPE to SYSTEM for domain credential access

This is the **BlueHammer kill chain's successor** — operators who integrated BlueHammer in early April likely transitioned to RedSun after BlueHammer's patch without missing a beat. Expect weaponized variants in commodity crimeware within days given the PoC's confirmed reliability.

---

## Disclosure Timeline

| Date | Event |
|---|---|
| April 3, 2026 | Chaotic Eclipse drops BlueHammer PoC publicly after MSRC dispute |
| April 10, 2026 | BlueHammer active exploitation first observed (Huntress) |
| April 14, 2026 | Microsoft patches BlueHammer as CVE-2026-33825 in April Patch Tuesday |
| April 16, 2026 | Chaotic Eclipse drops RedSun and UnDefend PoCs simultaneously to GitHub |
| April 16, 2026 | Huntress observes active RedSun + UnDefend exploitation in live intrusion |
| April 17, 2026 | Will Dormann confirms 100% reliable SYSTEM access — all platforms |
| April 18, 2026 | **Still unpatched. No CVE. No Microsoft timeline.** |

---

## References

- [BleepingComputer — New Microsoft Defender "RedSun" Zero-Day PoC Grants SYSTEM Privileges](https://www.bleepingcomputer.com/news/microsoft/new-microsoft-defender-redsun-zero-day-poc-grants-system-privileges/)
- [Picus Security — BlueHammer & RedSun: Windows Defender CVE-2026-33825 Zero-Day Explained](https://www.picussecurity.com/resource/blog/bluehammer-redsun-windows-defender-cve-2026-33825-zero-day-vulnerability-explained)
- [Help Net Security — Researcher drops two more Microsoft Defender zero-days, all three now exploited](https://www.helpnetsecurity.com/2026/04/17/microsoft-defender-zero-days-exploited/)
- [The Hacker News — Three Microsoft Defender Zero-Days Actively Exploited; Two Still Unpatched](https://thehackernews.com/2026/04/three-microsoft-defender-zero-days.html)
- [BleepingComputer — Recently Leaked Windows Zero-Days Now Exploited in Attacks](https://www.bleepingcomputer.com/news/security/recently-leaked-windows-zero-days-now-exploited-in-attacks/)
- [Field Effect — Three Microsoft Defender Zero-Days Reported Exploited](https://fieldeffect.com/blog/three-microsoft-defender-zero-days-reported-exploited)
- [Security Affairs — Microsoft Defender Under Attack as Three Zero-Days Enable Elevated Access](https://securityaffairs.com/190961/hacking/microsoft-defender-under-attack-as-three-zero-days-two-of-them-still-unpatched-enable-elevated-access.html)
