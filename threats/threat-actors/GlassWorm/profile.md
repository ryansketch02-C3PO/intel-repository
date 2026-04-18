# 🕵️ GlassWorm — Threat Actor Profile

**Scoundrel #005**

---

## Identity

| Field | Details |
|---|---|
| **Primary Name** | GlassWorm |
| **Aliases** | ForceMemo (sub-campaign designation) |
| **MITRE ATT&CK ID** | No formal group ID — campaign-level tracking |
| **Type** | Financially Motivated Cybercriminal |
| **Origin** | Russian-speaking (circumstantial — unconfirmed nation-state nexus) |
| **Sponsor** | Unknown / likely independent criminal operation |
| **Active Since** | October 2025 |
| **Status** | 🔴 Active |
| **Motivation** | Financial — cryptocurrency theft, credential harvesting, developer token theft |
| **Threat Level** | 🟡 MEDIUM (indirect A&D risk via developer supply chain) |

---

## Sector Targeting

| Priority | Sector |
|---|---|
| 🎯 Primary | Software development ecosystem |
| 🎯 Primary | Cryptocurrency / DeFi users |
| Secondary | Any org with internal software development function |
| Secondary | Tech companies, AI tooling users, open-source maintainers |

**Aerospace/Defense Relevance:** Indirect. GlassWorm targets developer workstations and supply chains. Any A&D contractor or supplier with an internal dev team or dependency on open-source Python/JS/VS Code tooling is a potential downstream victim.

---

## Background

GlassWorm is an ongoing software supply chain campaign first observed in late October 2025. It is not attributed to a known APT or nation-state actor — the tradecraft and targeting pattern are consistent with financially motivated Russian-speaking cybercriminals. The campaign's signature is the use of the **Solana blockchain as a command-and-control dead drop resolver**, making C2 infrastructure extremely resilient to takedown.

GlassWorm has evolved through multiple waves, expanding from Visual Studio Code / Open VSX extension abuse to GitHub repository poisoning, npm package compromise, and IDE-agnostic delivery. By March 2026, researchers at Aikido, Socket, StepSecurity, and the OpenSourceMalware community collectively identified **433 compromised components** across four ecosystems in a single coordinated wave.

The campaign exclusively targets non-Russian systems — all variants implement Russian locale/timezone checks and skip execution if detected. Code comments in Russian further support the attribution assessment.

---

## Campaign Timeline

| Date | Event |
|---|---|
| Oct 2025 | First observed — malicious Open VSX extensions using **invisible Unicode characters** to hide malicious logic; targets crypto wallet data and developer credentials |
| Late Oct–Jan 2026 | Expands to macOS; compromises legitimate developer accounts; introduces VNC remote access + SOCKS proxying; trojanizes Trezor and Ledger wallet clients |
| Jan 31, 2026 | Socket publishes first major report; Solana blockchain C2 mechanism confirmed; Russian locale exclusion documented |
| Feb 2026 | Cyware reports 230+ malicious extensions across Open VSX; macOS LaunchAgent persistence confirmed |
| Mar 8, 2026 | **ForceMemo** sub-campaign begins — stolen GitHub tokens used to force-push malicious commits to Python repositories; original commit metadata preserved to evade detection |
| Mar 13, 2026 | Socket identifies 72 new Open VSX extensions using **transitive dependency abuse** (`extensionPack` / `extensionDependencies`); Solana wallet rotated |
| Mar 16, 2026 | Two React Native npm packages compromised via ForceMemo (`react-native-international-phone-number`, `react-native-country-select`) |
| Mar 17, 2026 | **433 total compromised components** documented: 200 GitHub Python repos, 151 GitHub JS/TS repos, 72 VSCode/Open VSX extensions, 10 npm packages |
| Mar 2026 | Bitdefender confirms expansion to **Windsurf IDE** via trojanized R-language extension |

---

## TTPs — MITRE ATT&CK Mapping

### Initial Access / Delivery
| Technique | Details |
|---|---|
| T1195.001 | Supply Chain Compromise: Software Dependencies — malicious extensions, npm packages, Python repos |
| T1195.002 | Supply Chain Compromise: Software Supply Chain — GitHub account takeover + force-push |
| T1078 | Valid Accounts — stolen GitHub PATs used for force-push campaigns |

### Execution
| Technique | Details |
|---|---|
| T1204.002 | User Execution: Malicious File — developer installs/runs compromised package |
| T1059.007 | JavaScript — in-memory info-stealer payload |
| T1059.006 | Python — malicious code appended to `setup.py`, `main.py`, `app.py` |

### Persistence
| Technique | Details |
|---|---|
| T1547.001 | Boot or Logon Autostart Execution: Registry Run Keys — Windows scheduled task / Registry Run key |
| T1543.001 | macOS LaunchAgent — establishes persistence on macOS developer systems |

### Defense Evasion
| Technique | Details |
|---|---|
| T1027.013 | Obfuscated Files: Encrypted/Encoded File — AES + RC4 + Base64 + string-array obfuscation |
| T1027 | Unicode steganography — invisible Unicode characters to hide malicious logic from code review |
| T1480 | Execution Guardrails — Russian locale + timezone check; 48-hour reinfection lock (`~/init.json`) |
| T1036 | Masquerading — impersonates ESLint, Prettier, WakaTime, vscode-icons, Flutter, Angular, Claude Code, Codex |
| T1070.004 | Indicator Removal: File Deletion — payloads executed in memory, never written to disk |
| T1553.002 | Subvert Trust Controls: Code Signing — abuses legitimate extension trust (via transitive deps) |

### Command & Control
| Technique | Details |
|---|---|
| T1102 | Web Service — Solana blockchain memo fields as dead drop C2 resolver |
| T1071.001 | Web Protocols — HTTPS payload delivery and exfiltration |
| T1008 | Fallback Channels — multiple redundant Solana RPC endpoints; wallet rotation |

### Collection & Exfiltration
| Technique | Details |
|---|---|
| T1555.003 | Credentials from Web Browsers — Chromium-based browser credential theft |
| T1552.001 | Unsecured Credentials: Credentials in Files — SSH keys, `.env` files, API keys, config files |
| T1552.004 | Private Keys — cryptocurrency wallet data, developer signing keys |
| T1005 | Data from Local System — dev environment secrets, access tokens |
| T1041 | Exfiltration Over C2 Channel — HTTPS to attacker infrastructure |

---

## Technical Tradecraft

### Solana Blockchain Dead Drop (Key Innovation)
The defining GlassWorm signature. Instead of hard-coding C2 IPs, the malware queries a **Solana wallet's transaction memo field** every 5 seconds for the current payload URL. The actor posts a new Solana transaction (often multiple times daily) to rotate the payload endpoint. This makes traditional IP/domain-based C2 blocking ineffective — the blockchain is the C2, and it can't be taken down.

- Solana Memo Program: `MemoSq4gqABAXKb96qnH8TysNcWxMyWCqXgDLGmfcHr`
- C2 wallet 1 (early): `BjVeAjPrSKFiingBn4vZvghsGj9KCE8AJVtbc9S8o8SC`
- C2 wallet 2 (rotated ~Mar 2026): `6YGcuyFRJKZtcaYCCFba9fScNUvPkGXodXE1mJiSzqDJ`
- Transaction count (Nov 2025–Mar 2026): 50 observed transactions

### ForceMemo — GitHub Repo Poisoning
- Steals GitHub Personal Access Tokens (PATs) from developer systems
- Uses stolen tokens to **force-push malicious commits** to every accessible repository
- Preserves original commit message, author, and author date — rewrites only committer timestamp
- **No pull request created, no UI-visible commit trail** in GitHub
- First documented supply chain campaign to use this injection technique
- Detection: compare `author date` vs. `committer date` in git log — significant gap = potential force-push

### Transitive Dependency Abuse (Open VSX)
- Publishes a clean, benign-looking extension → builds download count
- Later updates manifest to add `extensionPack` or `extensionDependencies` pointing to a separate malicious payload extension
- Editor auto-installs the payload extension on update — user never explicitly installs it
- Initial review of the original release is insufficient; **version history diffs required**

### Evasion Upgrades (Mar 2026 variants)
- Payload decryption material moved **out of the extension and into HTTP response headers** (`ivbase64`, `secretkey`) — static analysis of the extension alone no longer reveals the payload
- Shifted from static AES loader to RC4 + Base64 + string-array obfuscation
- Payload delivery from GitHub infrastructure (bypasses Open VSX takedown process)

---

## Malware Capabilities

The final payload is a **JavaScript-based information stealer** that:
- Harvests credentials from Chromium-based browsers (Chrome, Edge)
- Steals cryptocurrency wallet data (hardware wallet software targeted: Trezor, Ledger)
- Extracts SSH keys, API keys, `.env` files, developer tokens
- Captures environment secrets and configuration data
- Supports VNC-based remote access (earlier variants)
- Supports SOCKS proxying (earlier variants)
- Drops compiled Node.js add-ons for Chromium data extraction
- Establishes persistence (LaunchAgent on macOS; Scheduled Task + Registry Run on Windows)
- Executes entirely in memory via `eval()` (macOS/Linux) or `vm.Script` (other)
- 48-hour reinfection lock via `~/init.json`

---

## Defensive Recommendations

1. **Audit VS Code / Open VSX extension history** — diff manifests across version updates; flag any newly added `extensionPack` or `extensionDependencies` relationships
2. **Block Solana C2 IPs** at perimeter: `45.32.150.251`, `45.32.151.157`, `70.34.242.255`
3. **Monitor outbound Solana RPC traffic** from developer workstations — non-blockchain apps querying Solana is a red flag
4. **Search codebases** for GlassWorm marker: variable `lzcdrtfxyqiplpd`
5. **Check developer systems** for `~/init.json` (persistence lock) and unexpected `~/node-v22*` installs
6. **Audit git history** — commits where committer date is significantly newer than author date = force-push indicator
7. **Rotate GitHub PATs** on any endpoint that has run compromised VS Code extensions
8. **Restrict extension installs** to vetted publishers via policy; treat extension updates as supply chain events
9. **EDR rules** for `ssh.exe` spawning from extension processes, in-memory `eval()` of large JS blobs

---

## Attribution Assessment

| Assessment | Detail |
|---|---|
| **Confidence** | Low-moderate (C3 — Admiralty) |
| **Key Indicators** | Russian-language code comments; Russian locale/timezone exclusion on all variants; shared C2 infrastructure patterns |
| **Against State Attribution** | No targeting of government/military entities; financially motivated; no nation-state indicators |
| **Assessment** | Likely independent financially motivated Russian-speaking cybercriminals. Not assessed as IRGC, FSB, GRU, or SVR-linked at this time. |

---

## Connection Web

- **Shai-Hulud campaign** (Nov 2025) — separate self-propagating dependency abuse campaign targeting 800+ npm packages; similar tradecraft (not the same actor, but same attack surface and technique space)
- No confirmed overlap with tracked APT groups at this time
- Indirect ecosystem overlap with **TeamPCP (#001)** — both target developer credentials and CI/CD supply chains, though via distinct tradecraft and different sectors

---

## Sources & References

- [The Hacker News — GlassWorm/ForceMemo (Mar 2026)](https://thehackernews.com/2026/03/glassworm-attack-uses-stolen-github.html)
- [Socket — 72 Malicious Open VSX Extensions, Transitive Delivery (Mar 2026)](https://socket.dev/blog/open-vsx-transitive-glassworm-campaign)
- [BleepingComputer — 433 Compromised Components (Mar 2026)](https://www.bleepingcomputer.com/news/security/glassworm-malware-hits-400-plus-code-repos-on-github-npm-vscode-openvsx/)
- [CSO Online — Transitive Dependency Abuse (Mar 2026)](https://www.csoonline.com/article/4145579/open-vsx-extensions-hijacked-glassworm-malware-spreads-via-dependency-abuse.html)
- [Cyware Daily Threat Intel — Feb 3, 2026](https://www.cyware.com/resources/threat-briefings/daily-threat-briefing/cyware-daily-threat-intelligence-february-03-2026)

---

*Admiralty Grade: C3 | TLP:WHITE | Last updated: 2026-04-02*
