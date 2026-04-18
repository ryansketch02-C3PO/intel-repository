# 🔍 Contagious Interview — Indicators of Compromise

**Last Updated:** 2026-04-15  
**Campaign:** Contagious Interview (CL-STA-0240)  
**Attribution:** Lazarus Group / DPRK  
**Sources:** Unit 42, NTT Security, Silent Push, SentinelOne, Cisco Talos, Socket Research, Gurucul, ENKI

> ⚠️ IoCs decay. Contagious Interview operators rapidly rotate infrastructure. Cross-reference against live threat feeds before actioning. Treat IP addresses with caution — some may be shared hosting.

---

## 🌐 C2 Domains & Infrastructure

### BeaverTail / InvisibleFerret C2 Domains
| Domain | Notes |
|---|---|
| `lianxinxiao[.]com` | Active BeaverTail C2 (Aug 2024–Mar 2025+); hosted on 37.211.126[.]117 |
| `camdriversupport[.]com` | C2 domain from BlockNovas fake interview infrastructure |
| `easydriver[.]cloud` | ClickFix C2 (gitlab.blocknovas[.]com infrastructure) |
| `api[.]drive-release[.]cloud` | Related to ContagiousDrop distribution |
| `skillcheck[.]pro` | Contagious Interview fake assessment site |
| `talentcheck[.]pro` | Contagious Interview fake assessment site |
| `vidassesspro[.]com` | Contagious Interview fake assessment site |
| `blocknovas[.]com` | DPRK front company domain (FBI seized May 2025) |
| `apply-blocknovas[.]site` | BlockNovas fake interview portal |
| `angeloper[.]com` | DPRK front company domain |
| `angeloperonline[.]online` | Angeloper Agency associated domain |
| `softglide[.]co` | DPRK front company domain |

### OtterCookie C2 Domains (NTT Security — 2025)
| Domain | Notes |
|---|---|
| `alchemy-api-v3[.]cloud` | OtterCookie C2 |
| `chainlink-api-v3[.]cloud` | OtterCookie C2 |
| `moralis-api-v3[.]cloud` | OtterCookie C2 |
| `modilus[.]io` | OtterCookie C2 |
| `zkservice[.]cloud` | OtterCookie v1 C2 |
| `w3capi[.]marketing` | OtterCookie v1 C2 |
| `payloadrpc[.]com` | OtterCookie v1 C2 |

### ClickFake Interview / GolangGhost Infrastructure
| Domain | Notes |
|---|---|
| `tetrismic[.]vercel[.]app` | Vercel-hosted staging server (OtterCookie npm delivery) |

---

## 🖥️ C2 IP Addresses

| IP Address | Port | Notes |
|---|---|---|
| `95.164.17[.]24` | 1224 | BeaverTail/InvisibleFerret C2 (Unit 42, 2024) |
| `185.235.241[.]208` | 1224 | BeaverTail/InvisibleFerret C2 (Unit 42, 2024) |
| `37.211.126[.]117` | 5000 / 8000 | lianxinxiao[.]com host; BeaverTail/InvisibleFerret staging |
| `37.221.126[.]117` | 8000 | nvidiaupdate.go C2 (BlockNovas infrastructure) |
| `86.104.74[.]169` | — | apply-blocknovas[.]site host |
| `45.159.248.55` | — | OtterCookie v1 C2 (NTT Security) |
| `116[.]202.208.125` | — | OtterCookie C2 (NTT Security, 2025) |
| `65[.]108.122.31` | — | OtterCookie C2 (NTT Security, 2025) |
| `194[.]164.234.151` | — | OtterCookie C2 (NTT Security, 2025) |
| `135[.]181.123.177` | — | OtterCookie C2 (NTT Security, 2025) |
| `188[.]116.26.84` | — | OtterCookie C2 (NTT Security, 2025) |
| `65[.]21.23.63` | — | OtterCookie C2 (NTT Security, 2025) |
| `95[.]216.227.188` | — | OtterCookie C2 (NTT Security, 2025) |
| `144[.]172.104[.]117` | 5918 | OtterCookie v4/v5 C2 (Socket Research, Nov 2025) |

---

## 🧬 Malware Hashes (SHA-256)

### OtterCookie v1 (NTT Security / Gurucul)
| SHA-256 | Notes |
|---|---|
| `d19ac8533ab14d97f4150973ffa810e987dea853bb85edffb7c2fcef13ad2106` | OtterCookie |
| `7846a0a0aa90871f0503c430cc03488194ea7840196b3f7c9404e0a536dbb15e` | OtterCookie |
| `4e0034e2bd5a30db795b73991ab659bda6781af2a52297ad61cae8e14bf05f79` | OtterCookie |
| `32257fb11cc33e794fdfd0f952158a84b4475d46f531d4bee06746d15caf8236` | OtterCookie |

### BeaverTail Qt Variant (Unit 42, 2024)
| SHA-256 | Notes |
|---|---|
| `d5c0b89e1dfbe9f5e5b2c3f745af895a36adf772f0b72a22052ae6dfa045cea6` | BeaverTail Qt (macOS) |

---

## 📦 Malicious npm Packages

### High-Confidence Confirmed Malicious (Contagious Interview)
| Package Name | Notes |
|---|---|
| `tailwind-magic` | Typosquats tailwind-merge; OtterCookie loader via tetrismic.vercel.app |
| `tailwind-node` | OtterCookie delivery |
| `node-tailwind` | OtterCookie delivery; embedded in dexproject lure repo |
| `node-tailwind-magic` | OtterCookie delivery |
| `react-modal-select` | OtterCookie delivery |
| `bcryptjs-node` | OtterCookie delivery (Nov 2025 wave) |
| `cross-sessions` | OtterCookie delivery |
| `json-oauth` | OtterCookie delivery |
| `react-adparser` | OtterCookie delivery |
| `session-keeper` | OtterCookie delivery |
| `tailwindcss-forms` | OtterCookie delivery (typosquats legitimate package) |
| `webpack-loadcss` | OtterCookie delivery |
| `module-listener` | Keylogging dependency in OtterCookie v5 |
| `node-nvm-ssh` | Contagious Interview dependency (removed by npm, Aug 2025) |

### Broader Nov 2025 Wave (Partial — Socket Research)
`assert-json-not`, `auth-handler`, `bcrypt-js-edge`, `bcryptjs-nodejs`, `bootstrap-flexgrid`, `bootstrap-setcolor`, `bootstrap-setcolors`, `bootstrap-setflexcolor`, `chai-as-deploy`, `chai-as-deployed`, `chai-as-sorted`, `chai-as-tested`, `chai-async`, `chai-async-chain`, `chai-async-flow`, `chai-auth`, `chai-status`, `chai-sync`, `chai-test-await`, `chai-type`, `cookie-breaker`, `cookie-mapper`, `cookie-validate`, `cwanner`, `custom-log-viewer`, `gridmancer`, `init-router`, `initial-path`, `js-coauth`, `js-copack`, `js-cotype`, `js-repack`, `js-uponcaps`, `json-getin`, `jstoauto`, `kyjnzu`, `lintcolor`, `log-pino`, `logify-pino`, `muleforge`, `multi-provider-settings`, `pgforce`, `pino-logging`, `pixel-bloom`, `pixelblm`, `pretty-text-formatter`, `radix-ui-react-modal`, `react-alerts-template-basic`, `react-bindify-decorators`

---

## 🐙 GitHub Infrastructure

| GitHub Account / Repo | Notes |
|---|---|
| `stardev0914` | Threat actor GitHub account (removed); hosted OtterCookie payloads and crypto-themed lure repos |
| `coinbase2024` | Fake repo used in BeaverTail campaign (Bitbucket + GitHub) |
| `gitlab.blocknovas[.]com` | Self-hosted GitLab on DPRK front company infrastructure |

---

## ✉️ Threat Actor Email Infrastructure

| Email | Notes |
|---|---|
| `designedcuratedamy58[@]gmail.com` | ContagiousDrop notification sender (victim engagement alerts to operators) |

---

## 🪟 Windows Registry Persistence

- **Key:** `HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Run`
- **Value Name:** `pythonws`
- **Value Data:** `<profile>\.pyp\pythonw.exe <profile>/.npl`
- (InvisibleFerret / OtterCookie persistence pattern)

---

## 🍎 macOS Persistence

- **LaunchAgent path:** `~/Library/LaunchAgents/com.yourscript.plist`
- Launches Python or Go backdoor on user login
- Also observed: shell scripts in startup items

---

## 🔑 Cryptocurrency Wallet Browser Extensions Targeted

| Extension ID | Wallet |
|---|---|
| `nkbihfbeogaeaoehlefnkodbefgpgknn` | MetaMask |
| `ejbalbakoplchlghecdalmeeeajnimhm` | MetaMask (Edge) |
| `fhbohimaelbohpjbbldcngcnapndodjp` | BNB Chain Wallet |
| `ibnejdfjmmkpcnlpebklmnkoeoihofec` | TronLink |
| `bfnaelmomeimhlpmgjnjophhpkkoljpa` | Phantom |
| `aeachknmefphepccionboohckonoeemg` | Coin98 Wallet |
| `hifafgmccdpekplomjjkcfgodnhcellj` | Crypto.com Wallet |
| `jblndlipeogpafnldhgmapagcccfchpi` | Kaia Wallet |
| `acmacodkjbdgmoleebolmdjonilkdbch` | Rabby Wallet |
| `dlcobpjiigpikoobohmabehhmhfoodbb` | Argent X (Starknet) |
| `mcohilncbfahbmgdjkbpemcciiolgcge` | OKX Wallet |
| `agoakfejjabomempkjlepdflaleeobhb` | Core Wallet |
| `omaabbefbmiijedngplfjmnooppbclkk` | Tonkeeper |
| `aholpfdialjgjfhomihkjbmgjidlcdno` | Exodus Web3 |
| `nphplpgoakhhjchkkhmiggakijnkhfnd` | TON Wallet |
| `penjlddjkjgpnkllboccdgccekpkcbin` | OpenMask |
| `lgmpcpglpngdoalbgeoldeajfclnhafa` | SafePal |
| `fldfpgipfncgndfolcbkdeeknbbbnhcc` | MyTonWallet |
| `bhhhlbepdkbapadjdnnojkbgioiodbic` | Solflare |
| `gjnckgkfmgmibbkoficdidcljeaaaheg` | Atomic Wallet |
| `afbcbjpbpfadlkmhmclhkeeodmamcflc` | MathWallet |

---

## 🧭 MITRE ATT&CK Techniques

| Tactic | ID | Technique |
|---|---|---|
| Initial Access | T1566.002 | Phishing: Spearphishing Link |
| Initial Access | T1195.002 | Supply Chain Compromise: Software Supply Chain (npm packages) |
| Execution | T1059.004 | Command and Scripting Interpreter: Unix Shell |
| Execution | T1059.006 | Command and Scripting Interpreter: Python |
| Execution | T1059.007 | Command and Scripting Interpreter: JavaScript |
| Execution | T1204.002 | User Execution: Malicious File |
| Execution | T1546.016 | Event Triggered Execution: Installer Packages (postinstall hooks) |
| Persistence | T1543.001 | Create/Modify System Process: Launch Agent (macOS) |
| Persistence | T1547.001 | Boot/Logon Autostart: Registry Run Keys (Windows) |
| Defense Evasion | T1036 | Masquerading (fake video apps, typosquatted npm packages) |
| Defense Evasion | T1497 | Virtualization/Sandbox Evasion |
| Defense Evasion | T1027 | Obfuscated Files or Information |
| Credential Access | T1555.003 | Credentials from Password Stores: Credentials from Web Browsers |
| Credential Access | T1539 | Steal Web Session Cookie |
| Discovery | T1082 | System Information Discovery |
| Discovery | T1083 | File and Directory Discovery |
| Collection | T1056.001 | Input Capture: Keylogging |
| Collection | T1113 | Screen Capture |
| Collection | T1115 | Clipboard Data |
| Collection | T1005 | Data from Local System |
| Collection | T1560.001 | Archive Collected Data: Archive via Utility |
| C2 | T1071.001 | Application Layer Protocol: Web Protocols |
| C2 | T1102 | Web Service (Vercel, GitHub, Dropbox for staging/exfil) |
| Exfiltration | T1041 | Exfiltration Over C2 Channel |
| Exfiltration | T1567 | Exfiltration Over Web Service (Dropbox, OneDrive) |
| Impact | T1657 | Financial Theft (cryptocurrency) |

---

## 🔍 Detection Queries (Splunk SPL Concepts)

```spl
| search process_name="node" AND (cmdline="*eval*" OR cmdline="*tetrismic*" OR cmdline="*lianxinxiao*")

| search process_name="curl" AND cmdline="*1224*"

| search process_name="python*" AND file_path="*/.n2/*"

| search registry_path="*Run*" AND registry_value_name="pythonws"

| search file_path="*/LaunchAgents/com.yourscript.plist"

| search network_destination_domain IN ("lianxinxiao.com","zkservice.cloud","payloadrpc.com","tetrismic.vercel.app","camdriversupport.com","easydriver.cloud") 

| search network_destination_ip IN ("95.164.17.24","185.235.241.208","37.211.126.117","45.159.248.55","144.172.104.117")

| search process_name="node" AND (cmdline="*socket.io*" OR cmdline="*socket.io-client*") AND NOT file_path IN ("/usr/local/lib/*","/usr/lib/*")
```

---

## 📚 IOC Sources

- [Unit 42 — Oct 2024](https://unit42.paloaltonetworks.com/north-korean-threat-actors-lure-tech-job-seekers-as-fake-recruiters/)
- [NTT Security — OtterCookie (Jan 2025)](https://jp.security.ntt/insights_resources/tech_blog/en-contagious-interview-ottercookie/)
- [NTT Security — OtterCookie v2-v4 (May 2025)](https://jp.security.ntt/insights_resources/tech_blog/en-waterplum-ottercookie/)
- [Silent Push — Front Companies (Apr 2025)](https://www.silentpush.com/blog/contagious-interview-front-companies/)
- [Socket Research — npm Infrastructure (Nov 2025)](https://socket.dev/blog/north-korea-contagious-interview-npm-attacks)
- [Gurucul — OtterCookie IOCs](https://partner.gurucul.com/articles/ThreatResearch/OtterCookie-a-new-malware-used-by-1-1-2025)
