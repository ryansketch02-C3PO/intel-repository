# Campaign Report: Mach-O Man (aka "North Korea's Safari")

**Status:** Active  
**Threat Actor:** Lazarus Group (Famous Chollima / Chollima Division)  
**Attribution:** North Korea (DPRK) – State-Sponsored  
**First Observed:** April 2026  
**Disclosed:** April 21, 2026 (Bitso Quetzal Team + ANY.RUN)  
**Last Updated:** 2026-04-30  

---

## Executive Summary

Lazarus Group is running an active macOS-targeting campaign using a newly identified modular malware kit called **"Mach-O Man"**, discovered by Bitso's Quetzal Team. The campaign employs **ClickFix** social engineering — fake meeting invitations delivered over Telegram — to trick targets into running malicious Terminal commands. Once executed, the kit performs credential theft, Keychain exfiltration, and data exfiltration via Telegram, then self-destructs.

The campaign sits within a broader offensive cluster dubbed **"North Korea's Safari"** and overlaps temporally with the Lazarus-attributed KelpDAO and Drift crypto exchange incidents (~$500M stolen in the same month).

> ⚠️ **Note:** The Mach-O Man tooling has been observed in use by threat actors beyond Lazarus, suggesting the kit has been shared or sold within the broader eCrime ecosystem.

---

## Targeting

| Dimension | Detail |
|---|---|
| **Primary Sectors** | Cryptocurrency exchanges, DeFi, Fintech, Web3 |
| **High-Value Targets** | Developers, executives, decision-makers using macOS |
| **Geographic Focus** | Global; confirmed victims in crypto/Web3 space |
| **Initial Contact Vector** | Telegram (via compromised or impersonated contacts) |

---

## Attack Chain

### Stage 1 – Social Engineering (ClickFix Lure)
- Attacker contacts victim via **Telegram**, often using a compromised colleague's account
- Victim receives an urgent meeting invite for Zoom, Microsoft Teams, or Google Meet
- Link points to a convincing fake site (e.g., `update-teams.live`, `livemicrosft.com`) impersonating the collaboration platform
- Page displays a simulated connection error and instructs the user to paste a command into their Terminal to "fix" it
- Because the user runs the command manually, **macOS Gatekeeper does not block it**

### Stage 2 – Stager (`teamsSDK.bin`)
- curl command executes the initial stager
- Stager supports impersonating Google, Zoom, Teams, or "System" (generic macOS prompts)
- Downloads a fake app bundle (`.app`) impersonating the chosen platform
- Applies **ad-hoc code signing** via macOS `codesign` to make the bundle appear legitimate
- Prompts the user for their macOS password via a fake dialog — window shakes on first two attempts, accepts on the third (deliberate trust-building design)

### Stage 3 – Profiler & Persistence
- **Profiler binary** enumerates: hostname, UUID, CPU, OS details, running processes, browser extensions (Brave, Chrome, Firefox, Safari, Opera, Vivaldi)
- ⚠️ Known bug: infinite loop in profiler causes noticeable CPU spikes — a potential detection signal
- **Persistence module** drops a renamed binary (`Onedrive`) into a hidden path under a folder labeled `Antivirus Service`
- Registers a LaunchAgent: `com.onedrive.launcher.plist` for execution at login

### Stage 4 – Stealer (`macrasv2`) & Exfiltration
- Collects browser extension data, SQLite credential databases, macOS Keychain items
- Compresses stolen data into `user_ext.zip`
- Exfiltrates via the **Telegram Bot API**
- Executes `delete_self.sh` — removes itself and all components
- ⚠️ **OPSEC failure**: Telegram bot token was left exposed in the binary, allowing defenders to monitor/disrupt the channel

---

## Malware Components

| Binary | Role |
|---|---|
| `teamsSDK.bin` | Stager / initial dropper |
| Profiler binary | Host enumeration, browser data collection |
| Persistence module | LaunchAgent installation (`com.onedrive.launcher.plist`) |
| `macrasv2` | Credential stealer, Keychain exfiltration, data exfiltration |
| `delete_self.sh` | Post-exfil cleanup / self-deletion |

**Language:** Go (compiled as native Mach-O binaries; compatible with Intel and Apple Silicon)  
**Masquerading:** OneDrive, Zoom, Teams, Google Meet  

---

## MITRE ATT&CK Mapping

| Tactic | Technique | ID |
|---|---|---|
| Initial Access | Phishing: Spearphishing via Service | T1566.003 |
| Execution | User Execution: Malicious File | T1204.002 |
| Execution | Command and Scripting Interpreter: Unix Shell | T1059.004 |
| Persistence | Create or Modify System Process: Launch Agent | T1543.001 |
| Defense Evasion | Masquerading | T1036 |
| Defense Evasion | Code Signing | T1553.002 |
| Privilege Escalation | Abuse Elevation Control Mechanism: Sudo and Sudo Caching | T1548.003 |
| Credential Access | Credentials from Password Stores: Keychain | T1555.001 |
| Credential Access | Steal Web Session Cookie | T1539 |
| Collection | Data from Local System | T1005 |
| Exfiltration | Exfiltration Over Web Service: Exfiltration to Code Repository | T1567 |
| Exfiltration | Automated Exfiltration | T1020 |

---

## Indicators of Compromise (IOCs)

### Network
| Indicator | Type | Note |
|---|---|---|
| `172.86.113.102` | IP | C2 infrastructure |
| `144.172.114.220` | IP | C2 infrastructure |
| `update-teams.live` | Domain | Fake Teams phishing site |
| `livemicrosft.com` | Domain | Fake Microsoft phishing site |
| Port `8888`, `9999` | Port | Primary C2 comms |
| `Go-http-client` | User-Agent | Go-based HTTP comms signature |

### File Artifacts
| Artifact | Type | Note |
|---|---|---|
| `teamsSDK.bin` | Binary | Stager |
| `macrasv2` | Binary | Stealer |
| `com.onedrive.launcher.plist` | LaunchAgent | Persistence |
| `user_ext.zip` | Archive | Exfil package |
| `delete_self.sh` | Script | Cleanup |
| `Antivirus Service/` | Directory | Persistence staging folder |

> **Full SHA-256 hashes**: Published by Bitso Quetzal Team — see [original report](https://quetzal.bitso.com/p/north-koreas-safari-hunting-for-rats)

---

## Detection Opportunities

- **CPU spike** from the profiler's infinite loop bug — anomalous sustained CPU usage from an unsigned process
- **LaunchAgent creation** at `~/Library/LaunchAgents/com.onedrive.launcher.plist` (suspicious OneDrive masquerading path)
- **Terminal executing curl** to download `.bin` files — especially after pasting from clipboard
- **Telegram API traffic** from non-user processes (`macrasv2`, `Go-http-client` UA)
- **ad-hoc code signing** applied to freshly downloaded `.app` bundles
- **Exposed Telegram bot token** — can be used to monitor or disrupt exfil channel

---

## Recommendations

1. **Block Terminal ClickFix lures**: Train users to never paste commands from websites into Terminal. Consider alerting on macOS Terminal executions of curl/bash chains originating from browser clipboard.
2. **Audit LaunchAgents**: Monitor for new LaunchAgents referencing OneDrive or Antivirus Service outside of known-good paths.
3. **EDR for macOS**: Deploy EDR with macOS Mach-O binary behavioral detection; traditional signature-based tools miss this campaign.
4. **Telegram monitoring**: Flag outbound Telegram Bot API traffic from non-standard processes.
5. **MFA hygiene**: Require phishing-resistant MFA (FIDO2/passkeys) — stolen session tokens won't be enough for authenticated access.
6. **Crypto/Fintech posture**: Treat macOS as a high-value attack surface, especially on developer and executive machines with access to wallets, keys, or production infra.

---

## Threat Actor Context

| Field | Detail |
|---|---|
| **Aliases** | Lazarus Group, Famous Chollima, Hidden Cobra, Guardians of Peace |
| **Sponsoring State** | North Korea (DPRK) |
| **Primary Motivation** | Financial (crypto theft to fund state programs) |
| **Estimated Total Theft** | ~$6.7 billion since 2017 (CertiK estimate) |
| **Prior macOS Tools** | AppleJeus, RustBucket |
| **Tooling Now Shared** | Mach-O Man kit being used by other threat actors |

---

## References

- [Bitso Quetzal Team – North Korea's Safari (Original Research)](https://quetzal.bitso.com/p/north-koreas-safari-hunting-for-rats)
- [ANY.RUN – New Lazarus APT Campaign: "Mach-O Man" macOS Malware Kit](https://any.run/cybersecurity-blog/lazarus-macos-malware-mach-o-man/)
- [SOC Prime – North Korea's Safari Campaign Delivers RATs](https://socprime.com/active-threats/north-koreas-safari-campaign-delivers-rats/)
- [Bitcoin.com – Mach-O Man Malware Steals macOS Keychain Data](https://news.bitcoin.com/mach-o-man-malware-steals-macos-keychain-data-in-lazarus-group-crypto-campaign/)
- [CoinDesk – CertiK Analysis via Binance Square](https://www.binance.com/en/square/post/315438294414513)

---

*Report authored by C-3PO Intel | intel-repo threat tracking*
