# 🔍 Chaotic Eclipse — Indicators of Compromise

> **Last Updated:** 2026-05-13
> **Admiralty Grade:** A3 — Tools verified; researcher identity unconfirmed
> **TLP:** TLP:WHITE
>
> ⚠️ Chaotic Eclipse is a researcher, not an intrusion operator. IOCs below relate to their
> published tooling. Detections should focus on behavioral patterns — compiled variants
> bypass all signature-based detections within hours of release.

---

## Published Tools & Repositories

| Tool | GitHub Repository | Type | PoC Status |
|---|---|---|---|
| BlueHammer | `github.com/Nightmare-Eclipse/BlueHammer` | LPE → SYSTEM (Defender TOCTOU) | Full — patched CVE-2026-33825 |
| RedSun | `github.com/Nightmare-Eclipse/RedSun` | LPE → SYSTEM (Defender cloud-file write) | Full — unpatched |
| UnDefend | `github.com/Nightmare-Eclipse/UnDefend` | Defender DoS / telemetry falsification | Full — unpatched |
| YellowKey | `github.com/Nightmare-Eclipse/YellowKey` | BitLocker bypass via WinRE | Full — unpatched |
| GreenPlasma | `github.com/Nightmare-Eclipse/GreenPlasma` | EoP / CTFMON section creation | Partial — SYSTEM shell withheld |

---

## Online Presence

| Platform | Handle / URL |
|---|---|
| GitHub | `github.com/Nightmare-Eclipse` (created Mar 27, 2026) |
| X (Twitter) | `@ChaoticEclipse0` |
| Blog | `deadeclipse666.blogspot.com` |

---

## Defender Signatures (Original PoC Binaries Only)

> ⚠️ These signatures detect compiled samples from the original PoC source only.
> Recompiling with minor modifications bypasses all of the below.

| Signature | Tool | Notes |
|---|---|---|
| `Exploit:Win32/DfndrPEBluHmr.BB` | BlueHammer | Original binary only |
| `HackTool:Win32/RedSunExploit` | RedSun | Original binary only |
| `HackTool:Win32/UnDefend` | UnDefend | Original binary only |

---

## Known File Names (In-the-Wild Observed)

Threat actors using Nightmare-Eclipse tooling have been observed staging binaries under these names (Huntress, Blackswan):

| Filename | Tool | Notes |
|---|---|---|
| `BlueHammer.exe` | BlueHammer | Original PoC name |
| `RedSun.exe` | RedSun | Original PoC name |
| `FunnyApp.exe` | RedSun | Renamed variant — observed in Pictures/ and Downloads/ |
| `z.exe` | RedSun | Short-name variant — observed in two-letter subfolders |
| `YellowKey.exe` | YellowKey | Original PoC name |
| `GreenPlasma.exe` | GreenPlasma | Original PoC name |

---

## SNEK Fork — BlueHammer (Community-Fixed Variant)

| Hash Type | Value |
|---|---|
| SHA256 | `c6baa5ec9ea2c2802a90acad5a53453d176a02e04a31ac8e9b7b34b5e3329b84` |
| MD5 | `5f9ee4e52da38191c3fdf2da567cc903` |
| SHA1 | `c885f1e77f08428cc628d9cb86cf4a10c09dd3b1` |

---

## Behavioral IOCs — High Confidence

These are technique-level indicators applicable regardless of binary name/hash:

### BlueHammer / RedSun (cldapi.dll abuse)
| Behavior | Detection Point |
|---|---|
| `NtQueryDirectoryObject` targeting `HarddiskVolumeShadowCopy*` from non-system/non-backup process | ETW / EDR process telemetry |
| `CfRegisterSyncRoot` called outside known cloud sync software (OneDrive, Dropbox, Box) | API monitoring |
| Low-privileged process calling `CreateService` | Windows Security Event ID 7045 |
| Rapid `SamiChangePasswordUser` → `LogonUserEx` → `SamiChangePasswordUser` (restore) in short window | Event ID 4723/4724 in quick succession |
| Binary execution from `%USERPROFILE%\Pictures\` or `%USERPROFILE%\Downloads\` with SYSTEM token acquisition | EDR behavioral |

### UnDefend
| Behavior | Detection Point |
|---|---|
| `AntivirusSignatureLastUpdated` timestamp stale >4h on any endpoint | `Get-MpComputerStatus` PowerShell query / MDE |
| Defender health dashboard reporting healthy while signatures are stale | Cross-reference dashboard vs. direct endpoint query |
| Process with `-agressive` flag spawned as child of `cmd.exe` under Explorer | EDR process tree |
| Defender service stop event followed immediately by `mpavbase.vdm` file lock | ETW / file monitoring |

### YellowKey
| Behavior | Detection Point |
|---|---|
| `FsTx` directory creation on EFI System Partition or removable media | File integrity monitoring |
| BitLocker suspension event without corresponding legitimate update workflow | Windows Event ID 24677 (BitLocker-Management log) |
| WinRE launch on managed endpoint | `reagentc` invocation / WinRE boot event |

### GreenPlasma
| Behavior | Detection Point |
|---|---|
| `HKCU\Software\Policies\Microsoft\CloudFiles` key created with `REG_OPTION_CREATE_LINK` | Registry monitoring |
| `TreeSetNamedSecurityInfo` granting `Everyone:GENERIC_ALL` on policy-adjacent keys | API monitoring |
| `CfAbortOperation` (cldapi.dll) called repeatedly from non-sync-client process | API monitoring |
| Unexpected CTFMON section object creation timing | ETW named object monitoring |

---

## Staging Path Patterns

Nightmare-Eclipse tooling is consistently staged from user-writable directories:

```
%USERPROFILE%\Pictures\
%USERPROFILE%\Downloads\
%USERPROFILE%\Downloads\<two-letter subfolder>\   (e.g., Downloads\ab\)
%TEMP%\
```

Any privileged execution originating from these paths should be treated as high-confidence indicator.

---

## Kill Chain IOC Correlation

When UnDefend + RedSun are deployed together (confirmed in-the-wild pattern), look for the following sequence within the same user session:

1. `CfAbortOperation` or Defender definition directory lock (UnDefend stage)
2. Defender signature staleness begins accumulating
3. `CfRegisterSyncRoot` outside known sync client (RedSun setup)
4. `TieringEngineService.exe` modification or replacement in System32
5. SYSTEM-privileged child process spawned from user context

This five-step sequence in a single session is a high-confidence indicator of Nightmare-Eclipse toolchain deployment.

---

## References

- [Huntress — Nightmare-Eclipse Tooling in Real-World Intrusion](https://www.huntress.com/blog/nightmare-eclipse-intrusion)
- [Blackswan Cybersecurity — RedSun IOC Analysis](https://blackswan-cybersecurity.com)
- [Picus Security — BlueHammer/RedSun Explained](https://www.picussecurity.com/resource/blog/bluehammer-redsun-windows-defender-cve-2026-33825-zero-day-vulnerability-explained)
