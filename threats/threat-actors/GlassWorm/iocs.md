# 🔍 GlassWorm — Indicators of Compromise

*Admiralty Grade: C3 | TLP:WHITE | Last updated: 2026-04-02*
*Sources: Socket, StepSecurity, Aikido, Bitdefender, BleepingComputer, The Hacker News, Cyware*

---

## ⚠️ Usage Notes

- GlassWorm's primary C2 mechanism is the **Solana blockchain** — IP/domain blocklists alone are insufficient
- Payload URLs rotate frequently (multiple times per day via new Solana transactions)
- Behavioral and filesystem indicators are more reliable than network IOCs for this campaign
- Extension-level IOCs should be treated as **point-in-time** — new extensions are continuously added/removed

---

## IP Addresses (C2 Infrastructure)

```
45.32.150.251    — Primary C2 (confirmed, earlier campaign)
45.32.151.157    — Additional C2 (Mar 2026 variants)
70.34.242.255    — Additional C2 (Mar 2026 variants)
```

---

## Blockchain Infrastructure

### Solana Wallets (Dead Drop C2 Resolver)
```
BjVeAjPrSKFiingBn4vZvghsGj9KCE8AJVtbc9S8o8SC   — Original wallet (active ~Nov 2025 – early Mar 2026)
6YGcuyFRJKZtcaYCCFba9fScNUvPkGXodXE1mJiSzqDJ   — Rotated wallet (Mar 2026 variants)
```

### Solana Program
```
MemoSq4gqABAXKb96qnH8TysNcWxMyWCqXgDLGmfcHr    — Solana Memo Program (used for C2 instruction embedding)
```

**Hunting note:** Any non-blockchain application making regular HTTP requests to Solana RPC endpoints (e.g., `api.mainnet-beta.solana.com`) — especially at 5-second intervals — should be treated as suspect.

---

## Embedded Crypto Material

```
AES key:  wDO6YyTm6DL0T0zJ0SXhUql5Mo0pdlSz
AES IV:   c4b9a3773e9dced6015a670855fd32b
```

*(Used in earlier variants; later variants move decryption material to HTTP response headers `ivbase64` and `secretkey`)*

---

## Filesystem Artifacts

```
~/init.json                — Persistence lock file (written after first execution; prevents re-execution within 48h)
~/node-v22*                — Unexpected Node.js installations in home directory (malware-dropped runtime)
*/i.js                     — Suspicious file in recently cloned repos or extension dirs
```

**Python repo infection marker (ForceMemo):**
Search for this variable name in Python codebases — indicates GlassWorm injection:
```
lzcdrtfxyqiplpd
```

**Git history anomaly (ForceMemo):**
In git log, check for commits where `committer date` is significantly newer than `author date` — indicates force-pushed history rewrite:
```bash
git log --format="%H %ai %ci %s" | awk '{if ($2 != $5) print}'
# Or more simply:
git log --pretty=format:"%h | AuthorDate: %ai | CommitDate: %ci | %s" | head -50
```

---

## Compromised npm Packages (ForceMemo — Mar 2026)

The following versions were confirmed malicious and have been removed/remediated:
```
react-native-international-phone-number  v0.11.8   (publisher: astroonauta)
react-native-country-select              (publisher: astroonauta)
```

---

## Compromised VS Code / Open VSX Extensions (Partial List — Mar 2026)

### Confirmed GlassWorm-Linked Extensions (Socket Research)
Publisher accounts confirmed or suspected malicious:

**Transitive delivery chain examples:**
```
otoboss.autoimport-extension@1.5.7         — extensionPack → oigotm.my-command-palette-extension
otoboss.autoimport-extension@1.5.6         — extensionPack → federicanc.dotenv-syntax-highlighting
twilkbilk.color-highlight-css              — recursive transitive chain confirmed
crotoapp.vscode-xml-extension              — recursive transitive chain confirmed
```

**Sleeper extensions (published clean, updated malicious):**
```
lauracode.wrap-selected-code               — published Mar 12, updated Mar 18 with GlassWorm loader
96-studio.json-formatter                   — published Mar 12, updated Mar 18 with GlassWorm loader
```

**Trojanized clones / typosquats:**
```
daeumer-web.es-linter-for-vs-code          — typosquat of legitimate 'dbaeumer' ESLint publisher
autoimport-smart-tool-2.5.8.vsix           — trojanized clone of legitimate Auto Import extension
aadarkcode.one-dark-material@3.20.1        — confirmed GlassWorm loader (Mar 2026 variant)
```

**Windsurf IDE (Bitdefender — Mar 2026):**
```
reditorsupporter.r-vscode-2.8.8-universal  — disguised as R language support; Node.js stealer + Chromium extraction
dark-code-studio.flutter-extension          — confirmed GlassWorm (Russian locale check code match)
```

**Impersonation targets (extensions masquerade as):**
```
ESLint, Prettier, WakaTime, vscode-icons, Better Comments
Angular, Flutter, Python, Vue language tooling
Claude Code, Codex, Antigravity (AI coding tools)
```

> Full continuously-updated extension list: https://socket.dev/glassworm (Socket campaign tracking page)

---

## Behavioral Detection Patterns

### Solana Polling (High Fidelity)
```
# Network: Regular outbound HTTPS to Solana RPC endpoints from non-blockchain processes
# Interval: ~5 seconds
# Source processes: node.exe, Code.exe (VS Code), or extension host processes
api.mainnet-beta.solana.com
```

### In-Memory Execution (EDR)
```
# eval() called with large (>1KB) dynamically constructed string in JavaScript context
# vm.Script() sandbox execution in Node.js process
# Extension host spawning unexpected child processes
```

### Persistence (Windows)
```
# Scheduled task created by VS Code extension host process
# Registry Run key modification: HKCU\Software\Microsoft\Windows\CurrentVersion\Run
# Source: Code.exe or extension host process — abnormal parent for scheduled task creation
```

### Persistence (macOS)
```
# LaunchAgent plist created in ~/Library/LaunchAgents/ by VS Code extension process
```

### Git History Manipulation (ForceMemo)
```
# Force-push to default branch (--force or --force-with-lease)
# Commit where author date ≠ committer date (author date preserved, committer date = push time)
# Appended Base64 block at end of setup.py / main.py / app.py
# No corresponding PR, no review trail
```

---

## YARA / Code Signatures

### Russian Locale Check (Present in all GlassWorm variants)
```javascript
// Pattern: check system locale before execution
if (locale === 'ru' || timezone.includes('Russia')) { return; }
// or environment variable checks for Russian timezone
```

### Solana Memo Query Pattern
```javascript
// HTTP request to Solana RPC fetching memo field from wallet transaction
// JSON-RPC method: getConfirmedSignaturesForAddress2 or getTransaction
// Wallet addresses: see Blockchain Infrastructure section above
```

### AES Key (Earlier Variants)
```
wDO6YyTm6DL0T0zJ0SXhUql5Mo0pdlSz
```

---

*TLP:WHITE — This information may be freely shared.*
