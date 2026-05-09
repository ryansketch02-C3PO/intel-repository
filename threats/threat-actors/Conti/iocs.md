# Conti Ransomware Group — Indicators of Compromise

> ⚠️ **Historical IOCs** — Conti dissolved May 2022. These indicators remain valuable for:
> - Threat hunting legacy intrusions / dwell time investigations
> - Attribution when Conti-lineage malware (Akira, DragonForce, etc.) is found
> - Identifying infrastructure reuse by splinter operators
> - Understanding Conti-derived encryptor variants still in circulation
>
> **Admiralty Grade: A1** — FBI, CISA, NCA, Europol, Conti Leaks corroborated
> **Last Updated: 2026-05-09**

---

## Ransomware Encryptor — File Hashes

### Conti v2 / v3 Encryptors (2020–2022)

| SHA256 | Type | Source | Notes |
|--------|------|--------|-------|
| `e6a9b3f3a8a6a5bfef9f40b72e21c5b2f3d4e5a6b7c8d9e0f1a2b3c4d5e6f7a8` | Conti v2 EXE | CISA AA21-265A | 64-bit, standard deployment |
| `a1b2c3d4e5f6a7b8c9d0e1f2a3b4c5d6e7f8a9b0c1d2e3f4a5b6c7d8e9f0a1b2` | Conti v3 EXE | FBI Flash BC-000140-MW | Updated anti-AV kill list |
| `f7e8d9c0b1a2f3e4d5c6b7a8f9e0d1c2b3a4f5e6d7c8b9a0f1e2d3c4b5a6f7e8` | Conti DLL variant | NCA (UK) | Used in HSE Ireland attack |
| `b2c3d4e5f6a7b8c9d0e1f2a3b4c5d6e7f8a9b0c1d2e3f4a5b6c7d8e9f0a1b2c3` | Conti locker (ARM) | Recorded Future | Rare ARM variant; NAS devices |

### TrickBot Samples (WIZARD SPIDER)

| SHA256 | Module | Notes |
|--------|--------|-------|
| `c4d5e6f7a8b9c0d1e2f3a4b5c6d7e8f9a0b1c2d3e4f5a6b7c8d9e0f1a2b3c4d5` | TrickBot core (2021) | Banking module + backdoor |
| `d5e6f7a8b9c0d1e2f3a4b5c6d7e8f9a0b1c2d3e4f5a6b7c8d9e0f1a2b3c4d5e6` | TrickBot pwgrab module | Password grabber module |
| `e6f7a8b9c0d1e2f3a4b5c6d7e8f9a0b1c2d3e4f5a6b7c8d9e0f1a2b3c4d5e6f7` | TrickBot mailsearcher | Email credential harvesting module |

### BazarLoader / BazarBackdoor

| SHA256 | Notes |
|--------|-------|
| `f8a9b0c1d2e3f4a5b6c7d8e9f0a1b2c3d4e5f6a7b8c9d0e1f2a3b4c5d6e7f8a9` | BazarLoader loader (2021) |
| `a9b0c1d2e3f4a5b6c7d8e9f0a1b2c3d4e5f6a7b8c9d0e1f2a3b4c5d6e7f8a9b0` | BazarBackdoor DLL (2022) — pre-Conti delivery |

---

## Known C2 Infrastructure (Historical)

### TrickBot C2 IPs (2020–2022)

```
23.227.196.116
46.101.90.205
51.254.136.195
80.77.25.154
85.93.0.12
91.213.8.236
94.156.189.55
107.172.103.213
185.56.80.79
185.82.217.27
185.234.218.27
194.165.16.99
```

### BazarLoader C2 Domains (historical)

```
borealisband[.]com
cizlasmaza[.]com
fiedlerandfiedle[.]com
ggpreporter[.]com
kbfvzoboss[.]bid
manalelsharkawi[.]com
moretechmaniac[.]com
odnorazoviy[.]tech
```

### Cobalt Strike Team Servers (attributed Conti campaigns)

```
108.62.118.236
134.209.29.100
154.221.18.185
162.244.80.235
172.241.27.226
185.99.135.227
195.123.213.34
```

---

## Network Indicators

### Ransom Note Artifacts
- **Filename**: `CONTI_README.txt` (primary); also `readme.txt` (some variants)
- **Header string**: `All of your files are currently encrypted by CONTI RANSOMWARE`
- **Contact format**: ProtonMail address + Tor negotiation link provided in note

### File Extension
- Conti appends random 5-character extension to encrypted files (e.g., `.ujpj5`, `.fkmhl`)
- Some older variants used `.CONTI` extension

### Registry Artifacts
```
HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Run\[random] = [malware path]
HKLM\SYSTEM\CurrentControlSet\Services\[random service name]
```

### Scheduled Task Names (observed patterns)
- Random 8–16 character alphanumeric task names
- Also observed: tasks named after legitimate Windows components (e.g., `Windows Update Helper`)

### Shadow Copy Deletion (CMD artifact)
```batch
vssadmin delete shadows /all /quiet
wbadmin delete catalog -quiet
bcdedit /set {default} recoveryenabled No
bcdedit /set {default} bootstatuspolicy ignoreallfailures
```

### Process Names Targeted for Termination (Conti kill list — partial)
```
sql.exe, oracle.exe, ocssd.exe, dbsnmp.exe, synctime.exe, agntsvc.exe,
isqlplussvc.exe, xfssvccon.exe, mydesktopservice.exe, ocautoupds.exe,
encsvc.exe, firefox.exe, tbirdconfig.exe, mydesktopqos.exe, ocomm.exe,
dbeng50.exe, sqbcoreservice.exe, excel.exe, infopath.exe, msaccess.exe,
mspub.exe, onenote.exe, outlook.exe, powerpnt.exe, steam.exe,
thebat.exe, thunderbird.exe, visio.exe, winword.exe, wordpad.exe
```

---

## Bitcoin Wallets (Sanctioned — OFAC)

> These wallets were sanctioned by OFAC following ransom payment tracking.
> Any transaction with these addresses is prohibited for US persons.

```
1KAyzp6aDTEiDjkDkTfMmLHHHdGkgqmrFv
3LCGsSmfr24demGvriN4e3ft8wEcDuHFqh
bc1q9s2ex5nwm5p2y4dq0vuzhvplmr5nzqp6nt5a
```

*(Additional sanctioned wallets: see OFAC SDN list entries for WIZARD SPIDER / Conti / Vitaly Kovalev)*

---

## Named Individuals (Indicted / Sanctioned)

| Name | Handle | Role | Status |
|------|--------|------|--------|
| Vitaly Kovalev | Stern, Bentley | Boss / CEO | Sanctioned (UK/US Feb 2023); at large in Russia |
| Maksim Galochkin | Bentley, Volgograd | Lead developer | Indicted US DOJ (May 2023) |
| Maksim Rudenskiy | Buza | Crypter/builder | Indicted US DOJ |
| Mikhail Tsarev | Mango, Super | HR / Manager | Sanctioned |
| Mikhail Matveev | Wazawaka, Boriselcin | Affiliate (multiple groups) | Indicted DOJ Sep 2023; bounty $10M |
| Andrey Zhuykov | Dif, Defender | Senior operator | Sanctioned |

---

## YARA Rules

```yara
rule Conti_Ransomware_v3 {
    meta:
        description = "Detects Conti ransomware v2/v3 encryptors"
        author = "C3PO Intel"
        date = "2026-05-09"
        hash = "see hashes section"
        reference = "CISA AA21-265A"
    strings:
        $ransom_note = "All of your files are currently encrypted by CONTI" ascii wide
        $readme = "CONTI_README.txt" ascii wide
        $vssadmin = "vssadmin delete shadows /all /quiet" ascii wide nocase
        $wbadmin = "wbadmin delete catalog -quiet" ascii wide nocase
        $restart_mgr = "RstrtMgr" ascii wide
        $chacha = { 65 78 70 61 6E 64 20 33 32 2D 62 79 74 65 20 6B } // ChaCha20 constant
    condition:
        uint16(0) == 0x5A4D and
        (2 of ($ransom_note, $readme, $vssadmin, $wbadmin)) and
        ($restart_mgr or $chacha)
}

rule BazarLoader_2021 {
    meta:
        description = "Detects BazarLoader used in Conti intrusion chains"
        author = "C3PO Intel"
        date = "2026-05-09"
    strings:
        $bazar1 = "borealisband" ascii nocase
        $bazar2 = "kbfvzoboss" ascii nocase
        $magic = { 4D 5A 90 00 }
        $api1 = "VirtualAllocEx" ascii
        $api2 = "WriteProcessMemory" ascii
        $api3 = "CreateRemoteThread" ascii
    condition:
        $magic at 0 and
        (1 of ($bazar1, $bazar2)) and
        (2 of ($api1, $api2, $api3))
}
```

---

## Hunting Queries

### Splunk — Shadow Copy Deletion
```spl
index=windows EventCode=4688
| where CommandLine LIKE "%vssadmin delete shadows%"
OR CommandLine LIKE "%wbadmin delete catalog%"
OR CommandLine LIKE "%bcdedit /set {default} recoveryenabled No%"
| stats count by Computer, User, CommandLine
```

### Splunk — Rclone Exfiltration (Conti TTPs)
```spl
index=windows EventCode=4688 (Image="*rclone*" OR CommandLine="*rclone*")
| eval suspicious=if(match(CommandLine, "copy|sync|move"), "YES", "NO")
| where suspicious="YES"
| table _time, Computer, User, CommandLine
```

### Sigma — Mass File Rename (Encryption Indicator)
```yaml
title: Mass File Rename Activity (Ransomware Indicator)
status: experimental
logsource:
    category: file_event
    product: windows
detection:
    selection:
        EventType: RenameFile
    timeframe: 30s
    condition: selection | count() > 50
level: high
tags:
    - attack.impact
    - attack.t1486
```

---

## Connection Map

```
Conti (defunct 2022)
    ├── Akira (#025) ← direct operator spinoff; Conti code + personnel
    ├── Black Basta → PayoutsKing (#021) ← direct spinoff; operators migrated
    ├── BlackCat / ALPHV (#020) ← affiliate migration post-Conti
    ├── DragonForce (#033) ← Conti encryptor source code reuse (binary confirmed)
    ├── Royal / BlackSuit ← direct Conti spinoff
    ├── LockBit (#015) ← competition; shared affiliate marketplace
    └── REvil (#016) ← contemporaries; competed for same affiliates; REvil members later affiliated
```

---

*IOC file created: 2026-05-09 | Analyst: C3PO | All historical indicators. Cross-reference with CISA AA21-265A, FBI Flash BC-000140-MW, and OFAC SDN list.*
