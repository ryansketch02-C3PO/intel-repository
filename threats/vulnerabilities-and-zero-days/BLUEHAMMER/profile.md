# BlueHammer — Windows Zero-Day LPE

## Identity

| Field | Details |
|---|---|
| **Vulnerability Name** | BlueHammer |
| **CVE** | **CVE-2026-33825** (confirmed — CVSS 7.8 High) |
| **Type** | Local Privilege Escalation (LPE) |
| **Class** | TOCTOU Race Condition + Path Confusion + Legitimate API Abuse |
| **Affected Platforms** | Windows 10, Windows 11, Windows Server (all builds as of April 2026) |
| **Patch Status** | ✅ **PATCHED** — CVE-2026-33825, April 14, 2026 (Defender platform update 4.18.26050.3011) |
| **PoC Status** | 🔴 **PUBLIC** — Full source code + precompiled binary on GitHub |
| **Discovered By** | Chaotic Eclipse / Nightmare Eclipse / deadeclipse666 (pseudonymous researcher) |
| **Public Disclosure** | April 3, 2026 (uncoordinated — dumped on GitHub after MSRC dispute) |
| **Confirmed Working** | Will Dormann (Tharros) | Cyderes Howler Cell | Multiple independent researchers |
| **Threat Level** | 🔴 HIGH |
| **Admiralty Grade** | A1 — confirmed by multiple independent credible researchers + Microsoft patch |

---

## Overview

BlueHammer landed in the security community's lap on April 3, 2026 when a pseudonymous researcher — frustrated with Microsoft's Security Response Center after allegedly being told to submit a video demonstration — dropped a fully working Local Privilege Escalation exploit for Windows directly to GitHub. No coordinated disclosure. No CVE. No patch. Just working exploit code and a pointed message to MSRC: *"I was not bluffing Microsoft, and I'm doing it again."*

What makes BlueHammer genuinely alarming is what it *doesn't* need: no kernel exploit, no memory corruption, no heap spray, no shellcode. The vulnerability exists entirely in the **interaction** between four legitimate, documented, fully functional Windows components — Microsoft Defender's update workflow, Volume Shadow Copy Service (VSS), the Windows Cloud Files API, and opportunistic locks (oplocks). Every component works exactly as Microsoft designed it. The vulnerability only emerges when they're chained in the right sequence with precise timing.

The end result: any low-privileged local user on a fully patched Windows 10 or Windows 11 machine can escalate to **NT AUTHORITY\SYSTEM** in under a minute. On Windows Server, exploitation yields local Administrator (still operationally significant for lateral movement).

By April 8, 2026, the Cyderes Howler Cell team had fixed the bugs in the original PoC and confirmed 100% reliable exploitation. A polished fork (SNEK_BlueWarHammer) with full Visual Studio build instructions and a precompiled binary followed shortly after. The barrier to entry is now minimal.

---

## Technical Analysis

### The Kill Chain

BlueHammer chains four Windows subsystems to force a privileged credential exposure:

```
1. TRIGGER: Force a Defender signature update
        ↓
2. TRAP: Defender creates a temporary VSS snapshot as part of remediation workflow
        ↓
3. FREEZE: Cloud Files API callbacks + oplocks stall Defender mid-operation
           (Defender is blocked waiting for a response that never comes)
        ↓
4. STEAL: While Defender is frozen, the VSS snapshot is still mounted —
          SAM, SYSTEM, and SECURITY registry hives are accessible
        ↓
5. ESCALATE: Extract + decrypt NTLM hashes → reset local Admin password →
             duplicate token → assign SYSTEM integrity → spawn SYSTEM shell
        ↓
6. COVER TRACKS: Restore original NTLM hash → Admin password unchanged from user's perspective
```

### Step-by-Step Breakdown

**Step 1 — Prerequisites check**
The exploit queries Windows Update COM interfaces for a pending Defender signature update. No pending update = no execution. This is the main timing dependency that makes reliability variable.

**Step 2 — Trigger scan and shadow copy creation**
Drops an EICAR test file (`foo.exe`) to bait Defender into scanning. Opens `RstrtMgr.dll` with a batch oplock as a tripwire — when Defender accesses it, the oplock breaks, signaling Defender is active and creating a VSS snapshot as part of its remediation workflow. A worker thread continuously enumerates `\\Device` watching for new `HarddiskVolumeShadowCopy*` devices.

**Step 3 — Freeze Defender (the magic)**
Registers the current directory as a **Cloud Files sync root** (`CfRegisterSyncRoot`, `CfConnectSyncRoot`). Drops a randomly named `.lock` placeholder inside it. When Defender enumerates the directory and touches the placeholder, `CfCallbackFetchPlaceHolders` fires, checks if the caller is the WinDefend service process, and when confirmed, immediately acquires a second batch oplock on that file. The callback refuses to return. Defender is now stalled at `GetOverlappedResult(hlock, &ovd, &nwf, TRUE)` — blocked indefinitely. The VSS snapshot remains mounted.

**Step 4 — Read SAM from inside the shadow copy**
With Defender frozen, the exploit opens registry hive files directly from the shadow copy device path — files that are normally runtime-locked and inaccessible to unprivileged processes:
- `\\Device\\HarddiskVolumeShadowCopy[N]\\Windows\\System32\\Config\\SAM`
- `\\Device\\HarddiskVolumeShadowCopy[N]\\Windows\\System32\\Config\\SYSTEM`
- `\\Device\\HarddiskVolumeShadowCopy[N]\\Windows\\System32\\Config\\SECURITY`

**Step 5 — Decrypt credentials and escalate**
Reads SYSTEM hive keys (`JD`, `Skew1`, `GBG`, `Data`) from `Control\Lsa`, reconstructs the 16-byte boot key, decrypts the LSA secret key, extracts the Password Encryption Key from SAM, decrypts NTLM hashes for all local accounts using AES/DES.

Then:
1. `SamiChangePasswordUser` — force-resets a local Administrator's password to `$PWNed666!!!WDFAIL`
2. `LogonUserEx` — logs in as that Administrator
3. Duplicates Administrator's security token, assigns SYSTEM integrity
4. `CreateService` — creates a malicious temporary Windows Service
5. Service spawns `cmd.exe` running as `NT AUTHORITY\SYSTEM`

**Step 6 — Clean up**
`SamiChangePasswordUser` again — restores the original NTLM hash. From the user's perspective, their password was never changed.

### Why Traditional Mitigations Don't Work

Because BlueHammer abuses legitimate Windows APIs rather than corrupting kernel memory:
- **Kernel Control Flow Guard** — no protection
- **Exploit Guard** — no protection
- **Hardware-enforced stack protection** — no protection
- **Standard endpoint hardening** — no protection
- **Defender signature `Exploit:Win32/DfndrPEBluHmr.BB`** — catches original compiled binary ONLY; a basic recompile defeats it entirely. The underlying technique is undetected.

---

## Affected Systems

| Platform | Exploitation Result | Notes |
|---|---|---|
| Windows 10 (all builds) | NT AUTHORITY\SYSTEM | Fully confirmed |
| Windows 11 (all builds) | NT AUTHORITY\SYSTEM | Fully confirmed |
| Windows Server | Local Administrator | Confirmed; SYSTEM not reached but admin is sufficient for lateral movement |

**Requires:** Local access + Windows Defender running + a pending Defender signature update

**Does NOT work against:** Systems with Credential Guard fully enabled (blocks SAM hash extraction)

---

## Public PoC Status

| Repository | Status | Notes |
|---|---|---|
| Original (Chaotic Eclipse / deadeclipse666) | Public — contains bugs, less reliable | Original disclosure |
| SNEK_BlueWarHammer fork | Public — fixed, precompiled binary | Full VS2022 build instructions + precompiled Release v1.0.0 |

---

## IOCs & Detection

### File Indicators
| Indicator | Type | Notes |
|---|---|---|
| `Exploit:Win32/DfndrPEBluHmr.BB` | Defender signature | Original PoC binary only — recompile bypasses |
| `SNEK_BlueWarHammer.exe` | Known malicious filename | SNEK fork |
| `SHA256: c6baa5ec9ea2c2802a90acad5a53453d176a02e04a31ac8e9b7b34b5e3329b84` | Binary hash | SNEK fork v1.0.0 |
| `MD5: 5f9ee4e52da38191c3fdf2da567cc903` | Binary hash | SNEK fork v1.0.0 |
| `SHA1: c885f1e77f08428cc628d9cb86cf4a10c09dd3b1` | Binary hash | SNEK fork v1.0.0 |

### Behavioral Indicators (High Confidence)
| Behavior | Event / API | Confidence |
|---|---|---|
| VSS enumeration from user-space process | `NtQueryDirectoryObject` targeting `HarddiskVolumeShadowCopy*` from non-system/non-backup process | 🔴 HIGH |
| Cloud Files sync root registration by untrusted process | `CfRegisterSyncRoot` outside OneDrive/Dropbox/Box/known sync software | 🔴 HIGH |
| Low-privileged process spawning Windows Services | `CreateService` from non-admin user process | 🔴 HIGH |
| Local Admin password change + immediate revert | Event ID 4723 followed by 4724 in rapid succession on same account | 🔴 HIGH |
| Anomalous `msmpeng.exe` (Defender) activity on junctions/symlinks | EDR process telemetry | 🟡 MEDIUM |
| Unexpected VSS snapshot creation outside backup windows | VSS Event Log IDs 8193, 8194, 8196 | 🟡 MEDIUM |

### Sigma-Style Hunt Queries
```yaml
# Hunt: VSS enumeration from user-space
Process: NtQueryDirectoryObject
ObjectName: "*HarddiskVolumeShadowCopy*"
ProcessIntegrity: NOT "System"

# Hunt: Cloud Files sync root registration outside known apps
Process: CfRegisterSyncRoot
CallerProcess: NOT ["OneDrive.exe", "Dropbox.exe", "Box.exe"]

# Hunt: Low-privileged CreateService call
EventID: 7045
AND ProcessToken: Low/Medium integrity

# Hunt: Rapid local admin password change + revert
EventID: 4724
TimeWindow: 60s
AccountType: LocalAdministrator
Followed by: EventID: 4723 on same account
```

---

## Mitigations

> ✅ **PATCHED — CVE-2026-33825, April 14, 2026.** Apply April 2026 Windows updates immediately. Compensating controls below remain valuable as defence-in-depth.

| Control | Priority | Impact |
|---|---|---|
| **Enable Windows Defender Credential Guard** | 🔴 IMMEDIATE | Blocks NTLM hash extraction from SAM — defeats the core BlueHammer kill chain. Best single control available. Run `msinfo32` to verify it's active. | 
| **Deploy Microsoft LAPS** | 🔴 IMMEDIATE | Randomizes local admin passwords per machine. Even if one hash is extracted, it can't be replayed laterally across the fleet. |
| **Enforce Least Privilege** | 🔴 IMMEDIATE | BlueHammer requires a local user account. Limit what accounts exist and what they can access — particularly Cloud Files APIs and VSS interfaces. |
| **Behavioral EDR (TTP-based)** | 🔴 HIGH | Signature-based AV won't catch modified PoC variants. EDR with behavioral rules (VSS enumeration, Cloud Files sync root anomalies, rogue service creation) is required. |
| **AppLocker / WDAC** | 🟡 HIGH | Block unsigned service binary execution — interrupts the final step of the BlueHammer chain. |
| **Monitor Event IDs 4723/4724** | 🟡 HIGH | Alert on rapid local admin password change + immediate revert. Unusual pattern with no legitimate baseline. |
| **JIT Privilege Access (Entra ID PIM)** | 🟡 MEDIUM | Reduce standing admin accounts; time-limit privilege elevation. |
| **Privileged Access Workstations (PAWs)** | 🟡 MEDIUM | Limits what a compromised standard user account can interact with. |
| **Apply April 2026 Windows updates** | ✅ DONE | CVE-2026-33825 patched April 14. Defender platform update 4.18.26050.3011. Deploy immediately if not already applied. |

---

## Threat Context

BlueHammer is a **ransomware pre-cursor primitive**. LPE exploits like this are the bridge between "attacker has a phishing foothold" and "attacker has full domain compromise." Ransomware groups running 24-hour attack cycles (see Storm-1175 / Medusa) routinely integrate public LPE PoCs within days of release.

This is particularly relevant given the current threat landscape (April 2026):
- **Storm-1175** (Medusa Ransomware) documented 24-hour attack cycles, actively targeting enterprises
- **Cl0p (#018)** and other RaaS groups actively hunting for LPE primitives to accelerate campaigns
- Three simultaneous zero-day emergencies in April 2026 (BlueHammer, Fortinet FortiClient EMS CVE-2026-35616, Chrome WebGPU CVE-2026-5281) are stretching security team bandwidth

---

## Disclosure Timeline

| Date | Event |
|---|---|
| ~March 26, 2026 | Chaotic Eclipse publishes blog post threatening disclosure after MSRC dispute |
| April 2–3, 2026 | PoC dropped publicly on GitHub (deadeclipse666) — no CVE, no patch, no coordinated disclosure |
| April 7–8, 2026 | Cyderes Howler Cell publishes full technical analysis; bugs fixed; end-to-end exploitation confirmed |
| April 8, 2026 | Help Net Security, RH-ISAC, multiple outlets publish coverage; Will Dormann confirms exploit |
| ~April 8, 2026 | SNEK_BlueWarHammer fork published with precompiled binary and full build docs |
| April 8–9, 2026 | Microsoft Defender signature update released: `Exploit:Win32/DfndrPEBluHmr.BB` (catches original only) |
| April 14, 2026 | **Microsoft patches BlueHammer — CVE-2026-33825 (April Patch Tuesday).** Defender platform update 4.18.26050.3011. |
| April 16, 2026 | Chaotic Eclipse drops RedSun + UnDefend — two further unpatched Defender zero-days. BlueHammer patch does NOT cover them. |
| April 27, 2026 | RedSun and UnDefend remain unpatched. BlueHammer fully remediated on patched systems. |

---

## References

- [Cyderes Howler Cell — BlueHammer Inside the Windows Zero-Day](https://www.cyderes.com/howler-cell/windows-zero-day-bluehammer)
- [Help Net Security — BlueHammer Windows Zero-Day Exploit Leaked](https://www.helpnetsecurity.com/2026/04/08/bluehammer-windows-zero-day-exploit-leaked/)
- [RH-ISAC — BlueHammer LPE Zero-Day Analysis](https://rhisac.org/threat-intelligence/bluehammer-windows-local-privilege-escalation-zero-day-publicly-released/)
- [Networkcraft — BlueHammer: The Unpatched Windows Zero-Day](https://networkcraft.net/bluehammer-windows-zero-day-local-privilege-escalation-2026/)
- [TechChannel News — Researcher Drops BlueHammer Zero-Day on GitHub](https://techchannel.news/anonymous-researcher-drops-bluehammer-zero-day-exploit-for-windows-on-github/)
