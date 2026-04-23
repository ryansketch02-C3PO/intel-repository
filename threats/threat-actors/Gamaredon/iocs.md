# Gamaredon (Primitive Bear / Armageddon) — Indicators of Compromise

> **TLP: WHITE** | Intel Grade: A1 | Last updated: 2026-04-23
> Sources: ESET (2024–2025), Unit 42/Palo Alto Networks (2022), CERT-UA, 360 Threat Intelligence Center (Sep 2025), CyberSec Sentinel (Jul 2025), Rewterz

---

## ⚠️ IOC CURRENCY NOTICE

Gamaredon rotates infrastructure **aggressively and constantly**:
- Domain/IP rotation: frequently daily or within hours via fast-flux DNS
- Cloudflare/DevTunnels subdomains may be active for <48 hours
- Hashes rotate per campaign and often per target
- **Behavioral detection is significantly more reliable than hash/domain blocking**

CERT-UA publishes the most current Gamaredon IOCs. Prioritize their advisories (UAC-0010 tag) for operational blocking.

---

## Network Indicators

### C2 Infrastructure Patterns (Current as of late 2025)

#### Cloudflare Tunnel Domains (2024–2025)
Gamaredon moved C2 behind Cloudflare Tunnels — true IP hidden behind Cloudflare infrastructure:
```
*.trycloudflare.com      [pattern — Cloudflare Tunnel generic]
*.cfargotunnel.com       [pattern — Cloudflare Tunnel specific]
```

#### Microsoft Dev Tunnels (2025)
```
*.devtunnels.ms          [pattern — Microsoft Dev Tunnels; Gamaredon using for C2 2025]
*.euw.devtunnels.ms      [EU West region specifically documented]
```

#### Cloudflare Workers Domains (2025, from 360 Threat Intelligence Center)
Short-lived (<48h) worker subdomains used for payload delivery:
```
dvorfiuao.3150wild.workers[.]dev
tskqbu.bronzevere.workers[.]dev
bdslmtlqh.bronzevere.workers[.]dev
jqrwbrbj.bronzevere.workers[.]dev
khycpsgbu.previoussusanna.workers[.]dev
oexvrm.embarrassed3627.workers[.]dev
xuwj.goldjan.workers[.]dev
gohiz.griercrimson.workers[.]dev
```

#### Historical .RU C2 Domains (Rewterz, CERT-UA)
> Note: Fast-flux; IPs behind these rotate constantly. Domain-level blocking is more effective than IP blocking.
```
loguna[.]ru
kb6ns[.]ru
litanq[.]ru
fulagam[.]ru
bulam[.]ru
niobiumo[.]ru
morbuso[.]ru
```

#### Telegram C2 Channels (IP delivery mechanism)
Gamaredon embeds encoded IPs in Telegram channel posts; malware queries the channel to retrieve current C2 IP.
```
hxxps://t[.]me/s/dracarc     [documented 2022, Unit 42]
api.telegra[.]ph/getPage/*   [Telegraph API used for C2 page queries]
```

Specific Telegraph pattern documented in Gamaredon–Turla collaboration (ESET 2025):
```
api.telegra[.]ph/getPage/canposgam-03-06?return_content=true
```

#### ESET-Domain C2 Masquerading (Turla collaboration chain, June 2025)
Turla masqueraded implant as ESET process; domain used in collaboration chain:
```
eset.ydns[.]eu               [Turla C2 masquerading as ESET; connected to Gamaredon access]
ekrn.ydns[.]eu               [resolves to 91.231.182[.]187 — Turla Kazuar delivery endpoint]
```

#### IP Addresses (Remcos RAT campaigns, July 2025)
```
198[.]23[.]251[.]10
92[.]82[.]184[.]33
```

---

## File Hashes

### Gamaredon Malware Samples (from public reporting)

#### MD5 Hashes (360 Threat Intelligence Center, Sep 2025)
```
98b540aeb2e2350f74ad36ddb4d3f66f
0459531e3cbc84ede6a1a75846a87495
f3deebe705478ec1a4ec5538ac3669cb
67896b57a4dcf614fb22283c130ab78b
```

#### MD5 Hashes (Rewterz, older samples)
```
0b27c465c9104e9a711c83ec4d5a04a4
9e0394e9f763aa8b128337a3cdab2b94
```

#### SHA-256 (Rewterz)
```
2d5482e29eb82caebbb19315c0f3f0eee20a9847f31d0ace2ed7307f2062e769
14037909d704c418a1d97835bcf7cf62239f0ad3dfd9fc4f4ca191f28fca894a
```

#### SHA-256 (Unit 42 — English language NATO-targeting samples, Aug 2022)
```
b1bc659006938eb5912832eb8412c609d2d875c001ab411d1b69d343515291b7   [MilitaryassistanceofUkraine.htm]
0b63f6e7621421de9968d46de243ef769a343b61597816615222387c45df80ae   [Necessary_military_assistance.rar]
3e72981a45dc4bdaa178a3013710873ad90634729ffdd4b2c79c9a3a00f76f43   [Ptero dropper calling Telegram C2]
```

#### SHA-1 (Rewterz)
```
361f045c0da62dd1ee6a4c3814818e1cf8ba86f5
a08cd2fbed3585657a6305d41faecbadab3f7d4e
```

#### SHA-256 / SHA-1 (Remcos RAT LNK campaigns, Jul 2025 — CyberSec Sentinel)
```
SHA-1: d2f97077fcf7e340a4262fa944ab13f133aa4e58
SHA-1: e444d001f2b69259f7845a5ffe9a44113d90e382
MD5: 560682cdcf395b5eb95487c7ef65c63e
MD5: ae8066bd5a66ce22f6a91bd935d4eee6
```

> For complete current hash listings, monitor CERT-UA advisories and VirusTotal tags: `gamaredon`, `ptero`, `UAC-0010`

---

## File System Indicators

### Artifacts & Staging Locations
```
%TEMP%\tmpXXXX.tmp.vbs              [random-named VBScript dropped in temp — 2024 campaigns]
%LOCALAPPDATA%\Winwordini.DAT       [staging area disguised as Word artifact]
%USERPROFILE%\AppData\Local\Temp\*  [rclone.exe exfil staging directory]
rclone.exe (non-IT contexts)        [used for Dropbox exfil]
TiVoDiag.exe (signed)               [abused for DLL sideloading — Remcos campaigns]
mindclient.dll                      [Remcos DLL sideload payload]
```

### Registry Persistence Keys
```
HKCU:\System\*                      [Gamaredon persistence — atypical location, low-profile]
HKCU:\...\RunOnce                   [secondary persistence]
HKCU:\Software\Microsoft\Windows\CurrentVersion\Run\*
```

### Suspicious File Patterns
- `.lnk` files appearing in USB drive root directories (PteroLNK propagation)
- `.hta` files executing via `mshta.exe` with outbound network calls
- VBScript files in `%TEMP%` with random names and immediate execution
- Word documents with remote template URLs (template injection delivery)

---

## Behavioral Indicators

### High Confidence
1. **`mshta.exe` making outbound HTTP/HTTPS connections** — Gamaredon's primary execution/download chain
2. **`wscript.exe` launching VBS with outbound network calls** — core execution pattern for PteroStew/PteroSand
3. **PowerShell downloading from `*.workers.dev`, `*.devtunnels.ms`, `*.trycloudflare.com`** — current C2 infrastructure hosting
4. **LNK files created in USB root directories** by `wscript.exe` or `powershell.exe` — PteroLNK spreading
5. **PowerShell downloading base64-encoded content and executing in-memory** — Kazuar delivery chain (Gamaredon–Turla collaboration pattern)

### Medium Confidence
1. **Outbound connections to Telegram API (`api.telegram.org`)** from non-browser, non-messaging processes
2. **Outbound connections to Telegraph API (`api.telegra.ph`)** from any endpoint process
3. **`rclone.exe` running from user-writable paths** with Dropbox remote destinations
4. **Word documents fetching remote templates** at open time from `.ru` or suspicious domains
5. **Registry entries under `HKCU:\System\`** — unusual path, consistent with Gamaredon persistence

### Telemetry Patterns
- Fast-flux DNS on `.ru` domains — IPs rotating hourly/daily
- Multiple simultaneous lightweight backdoors on a single host
- Sequential encoding of IP addresses in Telegram post text (format: `AAA@BBB@CCC@DDD`)

---

## MITRE ATT&CK Techniques

| Tactic | Technique | ID |
|--------|-----------|-----|
| Initial Access | Spearphishing Attachment | T1566.001 |
| Initial Access | Spearphishing Link | T1566.002 |
| Execution | User Execution: Malicious File | T1204.002 |
| Execution | Mshta | T1218.005 |
| Execution | Windows Script Host (wscript) | T1059.005 |
| Execution | PowerShell | T1059.001 |
| Persistence | Registry Run Keys | T1547.001 |
| Persistence | Office Application Startup: Office Template Macros | T1137.001 |
| Defense Evasion | Masquerading | T1036 |
| Defense Evasion | Obfuscated Files | T1027 |
| Defense Evasion | Template Injection | T1221 |
| Defense Evasion | Trusted Developer Utilities Proxy Execution | T1127 |
| Defense Evasion | Abuse Elevation Control Mechanism | T1548 |
| Credential Access | Credentials from Web Browsers | T1555.003 |
| Credential Access | Email Collection | T1114 |
| Discovery | File and Directory Discovery | T1083 |
| Discovery | System Information Discovery | T1082 |
| Lateral Movement | Replication Through Removable Media | T1091 |
| Collection | Data from Removable Media | T1025 |
| Collection | Email Collection | T1114 |
| Collection | Data Staged: Local Data Staging | T1074.001 |
| Command & Control | Application Layer Protocol: Web Protocols | T1071.001 |
| Command & Control | Dynamic Resolution: Fast Flux DNS | T1568.001 |
| Command & Control | Web Service (Telegram/Telegraph) | T1102 |
| Command & Control | Proxy: External Proxy (Cloudflare/DevTunnels) | T1090.002 |
| Exfiltration | Exfiltration to Cloud Storage (Dropbox) | T1567.002 |
| Impact | Defacement / Propaganda Delivery | T1491 |

---

## CERT-UA Resources

CERT-UA maintains the most current and comprehensive Gamaredon IOC repository:
- **Tag**: UAC-0010 (primary Gamaredon identifier)
- **Portal**: https://cert.gov.ua (Ukrainian CERT)
- **Telegram**: @CERTua (real-time advisory publication)

ESET maintains ongoing Gamaredon tracking at welivesecurity.com — search "Gamaredon" for the full research archive.

---

*TLP: WHITE | Sources: ESET 2024–2025, Unit 42 2022, CERT-UA, 360 Threat Intelligence Center Sep 2025, CyberSec Sentinel Jul 2025, SSU Operation Armageddon Report 2020*
