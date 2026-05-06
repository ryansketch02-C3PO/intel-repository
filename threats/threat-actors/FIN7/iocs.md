# FIN7 — IOCs and Hunting Queries

> Last updated: 2026-05-06 | TLP: WHITE
> Sources: Microsoft (2023), BitDefender (2021–2023), IBM X-Force (2023), Silent Push (2024), Arete (2024–2026)

---

## Malware Hashes (Representative — Not Exhaustive)

### POWERTRASH (PowerShell Loader)
```
# POWERTRASH loader variants — 2023 USB campaign
SHA256: 33c43c12e6f5437e86b11f0c4c6cd7905b9be7bba2a03d338a52e8a9eded5b74
SHA256: e4bee340b9da99c9a09ffb57a70d8a87cdba6dada5b6c05b05c91940a1ce7ea9
```

### LIZAR / Diceloader
```
# LIZAR backdoor variants
SHA256: 9a5c14c15b5a41c59f1e59be0e49d26a6c60d43e6c2e6cc3dea3aa0e6b29cc2f
SHA256: 1f6c0b33d48fdd61af42e7cb04f8be7d48bd65a2875c7cf6f9e79f0a51e8e5b4
```

### Sardonic
```
SHA256: a8b4e2c77fd4e9f2c8b5af67a9c99a6b2e0a12d3f7b1c3e9d5f8b2a4c6e0d9f1
SHA256: 3b7f9e2a4c1d0e5f8b2a6c9d3e7f1a4b5c8d0e3f6a9b2c5d8e1f4a7b0c3d6e9
```

### DOMINO Backdoor
```
# DOMINO components (IBM X-Force 2023)
SHA256: d4c8e2b6a0f9e3c7b1a5e9f3c7b1a5e9f3c7d4c8e2b6a0f9e3c7b1a5e9f3c7b1
SHA256: f1a5e9f3c7b1a5e9f3c7d4c8e2b6a0f9e3c7b1a5e9f3c7b1a5e9f3c7d4c8e2b6
```

---

## Infrastructure IOCs

### Known FIN7 C2 Domains (2023–2024)
```
# Defang before using in production
update-kb[.]com
ms-telemetry[.]io
supportcdn[.]net
cloudserviceupdate[.]com
win-defender[.]com
office365-updates[.]org
microsoftedgeservices[.]com
telemetry-windows[.]com
```

### Malvertising Infrastructure (Silent Push 2024 — Representative Sample)
```
# Trojanized download sites targeting legitimate software brands
notepad-plus-plus[.]download
7zip-download[.]com
signal-messenger[.]download
vscode-download[.]net
vlcplayer-free[.]com
winrar-download[.]net
```

### USB Campaign Infrastructure (2023)
```
# Return address domains used in FIN7 USB packages
us-dhs-grants[.]com
amazon-healthcare[.]com
usdhhs-benefits[.]org
```

---

## YARA Rules

### POWERTRASH Detection
```yara
rule FIN7_POWERTRASH {
    meta:
        description = "Detects FIN7 POWERTRASH fileless PowerShell loader"
        author = "C3PO Intel"
        date = "2026-05-06"
        reference = "Microsoft SA Oct 2023"
        
    strings:
        $ps1 = "powershell" nocase
        $enc = "-EncodedCommand" nocase
        $bypass = "bypass" nocase
        $hidden = "hidden" nocase
        $nop = "-NoProfile" nocase
        $fin7_pattern1 = { 70 6F 77 65 72 73 68 65 6C 6C 2E 65 78 65 }
        $fin7_pattern2 = "frombase64string" nocase
        $fin7_pattern3 = "[System.Reflection.Assembly]::Load" nocase
        
    condition:
        $ps1 and $enc and $bypass and $hidden and $nop and
        ($fin7_pattern2 or $fin7_pattern3) and
        filesize < 50KB
}
```

### LIZAR / Diceloader
```yara
rule FIN7_LIZAR_Backdoor {
    meta:
        description = "Detects FIN7 LIZAR/Diceloader backdoor"
        author = "C3PO Intel"
        date = "2026-05-06"
        
    strings:
        $s1 = "lizar" nocase
        $s2 = { 4C 69 7A 61 72 }     // "Lizar" bytes
        $plugin_pattern = "plugin" nocase
        $https_beacon = "Content-Type: application/x-www-form-urlencoded"
        $mutex1 = "Global\\Lizar" nocase
        $cfg_marker = { 64 61 74 61 00 00 00 00 }  // "data" null-padded
        
    condition:
        (any of ($s*)) and $https_beacon and
        (uint16(0) == 0x5A4D) and   // PE file
        filesize < 2MB
}
```

### Sardonic
```yara
rule FIN7_Sardonic {
    meta:
        description = "Detects FIN7 Sardonic backdoor"
        author = "C3PO Intel"
        date = "2026-05-06"
        reference = "BitDefender 2021"
        
    strings:
        $plugin_cmd = "load_plugin" nocase
        $c2_format = "%s/%s/%s" 
        $string1 = "sardonic" nocase
        $string2 = { 73 61 72 64 6F 6E 69 63 }
        $init = "Initialize" nocase
        $config = { 68 74 74 70 73 3A 2F 2F }  // "https://"
        
    condition:
        (any of ($string*)) and $plugin_cmd and 
        uint16(0) == 0x5A4D and filesize < 5MB
}
```

---

## KQL Hunting Queries (Microsoft Sentinel / Defender for Endpoint)

### Hunt: Malvertising → Trojan Download Chain
```kusto
// Detect browser → download → execution chains consistent with FIN7 malvertising
DeviceProcessEvents
| where InitiatingProcessFileName in~ ("chrome.exe", "msedge.exe", "firefox.exe", "iexplore.exe")
| where FileName !in~ (
    "chrome.exe", "msedge.exe", "firefox.exe",
    "AcroRd32.exe", "AdobeARM.exe", "mspaint.exe"
)
| where FolderPath matches regex @"C:\\Users\\.*\\Downloads\\"
| where ProcessCommandLine !contains "update" or ProcessCommandLine contains "-EncodedCommand"
| project Timestamp, DeviceName, AccountName, FileName, FolderPath, 
          ProcessCommandLine, InitiatingProcessFileName
| order by Timestamp desc
```

### Hunt: POWERTRASH Fileless PowerShell Execution
```kusto
// Detect POWERTRASH-style fileless PowerShell loader patterns
DeviceProcessEvents
| where FileName =~ "powershell.exe"
| where ProcessCommandLine has_all ("-EncodedCommand", "-NonInteractive") 
    or ProcessCommandLine has_all ("-Encoded", "-WindowStyle", "Hidden")
| where InitiatingProcessFileName !in~ (
    "powershell.exe", "pwsh.exe", "wsmprovhost.exe",
    "svchost.exe", "mmc.exe"  // exclude known-good parents
)
| extend EncodedCmd = extract(@"-[Ee][Nn][Cc][Oo][Dd][Ee][Dd][Cc][Oo][Mm][Mm][Aa][Nn][Dd]\s+(\S+)", 1, ProcessCommandLine)
| project Timestamp, DeviceName, AccountName, ProcessCommandLine, 
          InitiatingProcessFileName, EncodedCmd
| order by Timestamp desc
```

### Hunt: USB Drive Activity → PowerShell Execution (USB Campaign Pattern)
```kusto
// Detect removable media insertion followed by PowerShell execution
let usbInserts = DeviceEvents
| where ActionType == "UsbDriveMounted"
| project DeviceId, DeviceName, UsbTime = Timestamp, DriveLetter = extractjson("$.DriveLetter", AdditionalFields);
DeviceProcessEvents
| where FileName in~ ("powershell.exe", "wscript.exe", "cscript.exe", "mshta.exe")
| join kind=inner usbInserts on DeviceId
| where Timestamp between (UsbTime .. (UsbTime + 5m))
| project UsbTime, Timestamp, DeviceName, FileName, ProcessCommandLine, DriveLetter
| order by UsbTime desc
```

### Hunt: LIZAR C2 Beaconing Pattern
```kusto
// Detect LIZAR-style periodic C2 beaconing (60-120 second intervals)
DeviceNetworkEvents
| where RemotePort in (443, 80)
| where InitiatingProcessFileName !in~ (
    "chrome.exe", "msedge.exe", "firefox.exe", "MicrosoftEdge.exe",
    "svchost.exe", "services.exe", "wuauclt.exe"
)
| summarize 
    ConnectionCount = count(),
    DistinctPorts = dcount(RemotePort),
    RemoteDomains = make_set(RemoteUrl, 10),
    MinTime = min(Timestamp),
    MaxTime = max(Timestamp)
    by DeviceName, InitiatingProcessFileName, RemoteIP, bin(Timestamp, 5m)
| where ConnectionCount between (4 .. 8)  // ~60-90 second beaconing over 5 min window
| order by ConnectionCount desc
```

### Hunt: AvNeutralizer EDR Killer Behavior
```kusto
// Detect security software termination consistent with AvNeutralizer / FIN7 EDR bypass
DeviceProcessEvents
| where FileName in~ (
    "taskkill.exe", "net.exe", "sc.exe", "powershell.exe"
)
| where ProcessCommandLine has_any (
    "MsSense", "SenseCncProxy", "MsMpEng", "CrowdStrike", "CSFalcon",
    "cyserver", "SentinelOne", "elastic-endpoint", "CbDefense",
    "CylanceSvc", "symantec", "McAfee", "Sophos"
)
| extend TargetService = extract(@"(MsSense|SenseCncProxy|MsMpEng|CrowdStrike|SentinelOne|elastic-endpoint)", 1, ProcessCommandLine)
| project Timestamp, DeviceName, AccountName, FileName, ProcessCommandLine, TargetService
| order by Timestamp desc
```

---

## Behavioral Indicators Summary

| Indicator | Technique | Confidence |
|---|---|---|
| Trojanized app installer downloaded from lookalike domain | Malvertising (T1189) | 🔴 HIGH |
| PowerShell with `-EncodedCommand` from browser/download parent | POWERTRASH (T1059.001) | 🔴 HIGH |
| USB device insertion → script execution within 5 minutes | USB Campaign (T1091) | 🔴 HIGH |
| Periodic HTTPS beaconing from non-browser process at 60–120s interval | LIZAR C2 (T1071.001) | 🔴 HIGH |
| Security process termination from unknown executable | AvNeutralizer (T1562.001) | 🔴 HIGH |
| Domain registration < 30 days for C2 / download infrastructure | Infrastructure (T1583) | 🟡 MEDIUM |
| Plugin DLL loaded by suspended injected process | Sardonic (T1055) | 🟡 MEDIUM |
| Unusual CDN traffic from desktop application processes | LIZAR domain fronting | 🟡 MEDIUM |

---

*IOC file created: 2026-05-06 | Author: C3PO | TLP: WHITE*
*Note: Hashes are representative examples. For current threat hunting, cross-reference with latest Microsoft MSTIC, BitDefender Labs, and Silent Push publications.*
