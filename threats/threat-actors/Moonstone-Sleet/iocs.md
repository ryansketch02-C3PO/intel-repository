# IOCs — Moonstone Sleet / Storm-1789 (Scoundrel #034)

> Source: Microsoft Threat Intelligence (May 2024), Rewterz Advisory (2024)
> TLP: WHITE | Last updated: 2026-05-03

---

## Domains / Infrastructure

| Indicator | Type | Campaign | Notes |
|---|---|---|---|
| `detankwar[.]com` | Domain | DeTankWar game | Fake game website — game download + public-facing presence |
| `defitankzone[.]com` | Domain | DeTankWar game | Alternate game domain |
| StarGlow Ventures domain (varies) | Domain | StarGlow Ventures campaign | Fake software company; hosted dummy unsubscribe page with tracking pixel |
| C.C. Waterfall domain (varies) | Domain | C.C. Waterfall campaign | Fake IT consulting company |

---

## File Indicators

| Indicator | Type | Notes |
|---|---|---|
| `putty.exe` (trojanized) | Trojanized PuTTY | Not distinguishable by filename alone — verify hash against known-good PuTTY builds |
| `url.txt` (alongside trojanized putty.exe) | Companion file | Contains actor-controlled IP address + password; always delivered as pair |
| `delfi-tank-unity.exe` | DeTankWar game executable | Loads malicious DLLs bundled with game installation |
| SplitLoader DLL | Malware loader | Dropped to disk by Stage 2; executed via scheduled task or Run key |
| FakePenny ransomware (loader + encryptor) | Ransomware | Custom two-component ransomware; $6.6M BTC demand |

---

## Registry Indicators

| Key / Value | Notes |
|---|---|
| `HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Run\[SplitLoader name]` | SplitLoader persistence via Run key |
| New scheduled task pointing to SplitLoader DLL | Alternate SplitLoader persistence |
| New malicious Windows service created by YouieLoad | YouieLoad persistence mechanism |

---

## Behavioral Indicators

| Indicator | Description | Confidence |
|---|---|---|
| `putty.exe` receiving IP + password from user → spawning loader process | Trojanized PuTTY execution chain | 🔴 HIGH |
| `curl` executed from within npm package install script | Malicious npm package drop | 🔴 HIGH |
| Game executable (`.exe`) loading unsigned DLLs from same directory | DeTankWar / YouieLoad chain | 🔴 HIGH |
| New Windows service created by game/media process | YouieLoad service persistence | 🔴 HIGH |
| Network and user enumeration commands shortly after game/tool execution | YouieLoad discovery phase | 🔴 HIGH |
| LSASS memory access from npm-installed package or non-security process | Credential theft phase | 🔴 HIGH |
| `delfi-tank-unity.exe` process on system | DeTankWar game installed | 🔴 HIGH |
| Fileless payload execution from memory (no on-disk PE) | YouieLoad in-memory loading | 🟡 MEDIUM — requires behavioral EDR |
| FakePenny ransom note with NotPetya-style content | FakePenny ransomware active | 🔴 HIGH (active incident) |
| Unsolicited email from `@starglow[.]ventures` or `@ccwaterfall[.]com` equivalents | Fake company phishing | 🟡 MEDIUM |
| Email containing 1×1 tracking pixel from unknown software company solicitation | StarGlow Ventures-style recon | 🟡 MEDIUM |

---

## Microsoft Defender Detection Names

| Detection | Component |
|---|---|
| `Behavior:Win64/PennyCrypt` | FakePenny ransomware |
| `TrojanDropper:Win32/SplitLoader` | SplitLoader Stage 2/3 |
| `TrojanDropper:Win64/YouieLoad` | YouieLoad game loader |
| `HackTool:Win32/Mimikatz` | Post-compromise credential theft |
| `HackTool:Win64/Mimikatz` | Post-compromise credential theft |

---

## KQL Hunting Queries (Microsoft Defender XDR)

```kql
// Hunt 1: Detect Moonstone Sleet malware family activity
let MoonstoneSleet_threats = dynamic([
    "Behavior:Win64/PennyCrypt",
    "HackTool:Win32/Mimikatz",
    "HackTool:Win64/Mimikatz",
    "TrojanDropper:Win32/SplitLoader",
    "TrojanDropper:Win64/YouieLoad"
]);
SecurityAlert
| where ProviderName == "MDATP"
| extend ThreatName = tostring(parse_json(ExtendedProperties).ThreatName)
| where ThreatName in (MoonstoneSleet_threats)
| project TimeGenerated, AlertName, ThreatName, DeviceName, Entities

// Hunt 2: Trojanized PuTTY — putty.exe spawning unusual child processes
DeviceProcessEvents
| where FileName =~ "putty.exe"
| where ProcessCommandLine has_any ("-ssh", "-telnet")
| where InitiatingProcessParentFileName !in ("explorer.exe", "cmd.exe", "powershell.exe")
| extend suspicious_child = ChildProcessName
| where suspicious_child !in ("putty.exe", "pageant.exe", "psftp.exe", "pscp.exe")
| project Timestamp, DeviceName, ProcessCommandLine, ChildProcessName, AccountName

// Hunt 3: Malicious npm package — curl from node process
DeviceProcessEvents
| where InitiatingProcessFileName =~ "node.exe"
| where FileName =~ "curl.exe"
| where ProcessCommandLine has_any ("-o", "--output", "http://", "https://")
| where not(ProcessCommandLine has_any ("npmjs.com", "registry.npm", "github.com"))
| project Timestamp, DeviceName, InitiatingProcessCommandLine, ProcessCommandLine, AccountName

// Hunt 4: New services created by game/media executables
DeviceEvents
| where ActionType == "ServiceInstalled"
| join kind=inner (
    DeviceProcessEvents
    | where FileName !in ("services.exe", "svchost.exe", "msiexec.exe", "setup.exe")
    | where FolderPath !has @"C:\Windows\System32"
    | where FolderPath !has @"C:\Program Files"
) on $left.InitiatingProcessId == $right.ProcessId
| project Timestamp, DeviceName, ActionType, InitiatingProcessFileName, FolderPath, AdditionalFields

// Hunt 5: YouieLoad — fileless in-memory payload detection proxy (behavioral)
DeviceProcessEvents
| where InitiatingProcessFileName =~ "delfi-tank-unity.exe"
| project Timestamp, DeviceName, FileName, ProcessCommandLine, InitiatingProcessFileName

// Hunt 6: DeTankWar domains in network traffic
DeviceNetworkEvents
| where RemoteUrl has_any ("detankwar", "defitankzone", "defitankland", "tankwarszone")
| project Timestamp, DeviceName, RemoteUrl, RemoteIP, InitiatingProcessFileName
```

---

## FakePenny Ransomware Characteristics

| Attribute | Detail |
|---|---|
| Components | Loader (Stage 1) + Encryptor (Stage 2) — two-component design |
| Ransom demand | $6.6 million USD in Bitcoin |
| Ransom note style | Closely matches NotPetya ransom note (Seashell Blizzard / Sandworm) |
| Deployment timing | 2 months after initial compromise — after intelligence collection phase complete |
| Targets | Organizations previously compromised via other Moonstone Sleet campaigns |
| Defender detection | `Behavior:Win64/PennyCrypt` |

---

*IOC file created: 2026-05-03 | Author: C3PO | TLP: WHITE*
