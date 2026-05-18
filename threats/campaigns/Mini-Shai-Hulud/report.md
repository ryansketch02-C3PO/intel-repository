# Mini Shai-Hulud — TeamPCP Multi-Ecosystem Supply Chain Worm Campaign

> C3PO Threat Actor Repository | Campaign File  
> Status: 🔴 ACTIVE — May 11 TanStack wave ongoing; worm propagation self-sustaining | Last Updated: 2026-05-18

---

## Campaign Summary

| Field | Detail |
|---|---|
| **Campaign Name** | Mini Shai-Hulud (self-assigned by threat actor) |
| **C3PO Designation** | MCSC-001 — Multi-Ecosystem Supply Chain Campaign |
| **Threat Actor** | TeamPCP (@pcpcats) — see Threat Actor profile |
| **Campaign Active** | April 22, 2026 – ongoing (May 11 TanStack wave still propagating) |
| **Status** | 🔴 ACTIVE — May 11 wave spawned 373 malicious package versions; worm propagation continues |
| **Primary Objective** | Developer credential and cloud secret theft → ransomware deployment via Vect RaaS partnership |
| **Ecosystems Targeted** | npm · PyPI (first cross-ecosystem Shai-Hulud wave) |
| **Wave Designation** | Third major Shai-Hulud operational iteration (Sept 2025 → Dec 2025 → Apr 2026) |
| **Related CVE** | CVE-2026-45321 (May 11 TanStack wave) |
| **Ransomware Nexus** | ✅ Confirmed — Vect ransomware-as-a-service (RaaS) operational partnership |
| **Admiralty Grade** | A1 — Attributed by Wiz, Unit 42, Socket, StepSecurity, Aikido, OX Security, SafeDep, Snyk, Sophos, Dataminr |
| **TLP** | TLP:WHITE |

---

## Overview

Between April 22 and May 11, 2026, threat actor **TeamPCP** executed the most technically sophisticated multi-ecosystem supply chain campaign of 2026, compromising high-trust developer packages across npm and PyPI in coordinated waves. The operation, self-branded **"Mini Shai-Hulud"** (a *Dune* reference to the massive sandworm deity), deployed an 11-megabyte obfuscated JavaScript payload that harvested developer credentials, cloud secrets, cryptocurrency wallets, and AI coding agent configuration files — then used stolen tokens to self-propagate across the npm ecosystem without further attacker interaction.

The campaign is the third major operational iteration of a worm framework TeamPCP has been developing since September 2025, and the first to jump from npm into PyPI within a single campaign cycle. The May 11 TanStack wave introduced an unprecedented capability: publishing malicious packages with **valid SLSA Build Level 3 provenance attestations** — making compromised packages indistinguishable from legitimate ones by standard supply chain verification tools.

As of this writing, **over 1,100 public GitHub repositories** carry the telltale description "A Mini Shai-Hulud has Appeared," each a covert exfiltration sink containing encrypted developer secrets. Stolen data is encrypted with AES-256-GCM and RSA-4096 (attacker's embedded public key) — making the exfiltrated data **unrecoverable without the attacker's private key**.

TeamPCP has a confirmed operational partnership with the **Vect ransomware-as-a-service** operation, meaning credential theft is the precursor and ransomware deployment is the monetization endpoint.

---

## Shai-Hulud Lineage

| Wave | Date | Scope | Notable |
|---|---|---|---|
| Shai-Hulud 1.0 | Sept 2025 | Hundreds of npm packages; thousands of developer credentials | Original worm framework established |
| Shai-Hulud 2.0 | Dec 2025 | Refined propagation, wider cloud targeting | Added cloud provider credential sweep |
| Checkmarx / OpenVSX | Mar 2026 | Checkmarx GitHub Actions, OpenVSX plugins, Aqua Trivy | First security tooling focus |
| **Bitwarden / Mini Shai-Hulud** | **Apr 22–30, 2026** | **npm + PyPI; 5 packages, 2 ecosystems** | **AI agent config persistence; cross-ecosystem; encrypted exfil** |
| **Mini Shai-Hulud Is Back** | **May 11, 2026** | **TanStack; 42 packages, 373 malicious versions, 169 packages downstream** | **First npm worm with valid SLSA BL3 provenance; P2P C2; CVE-2026-45321** |

Technical fingerprint linking all waves: shared `ctf-scramble-v2` Fisher-Yates PRNG (seeded with `0x3039` / 12345), shared RSA public key, identical Russian locale exit guard. Unit 42 confirmed these as shared-authorship indicators.

---

## Wave 1: Bitwarden + SAP (April 22–30, 2026)

### Compromised Packages

| Date (UTC) | Package | Ecosystem | Malicious Version | Exposure Window |
|---|---|---|---|---|
| Apr 22 | `@bitwarden/cli` | npm | `2026.4.0` | ~90 minutes (~334 developers exposed) |
| Apr 29 | `@cap-js/sqlite` | npm | `2.2.2` | 09:55–12:14 UTC (~2h19m) |
| Apr 29 | `@cap-js/postgres` | npm | `2.2.2` | 09:55–12:14 UTC (~2h19m) |
| Apr 29 | `@cap-js/db-service` | npm | `2.10.1` | 09:55–12:14 UTC (~2h19m) |
| Apr 29 | `mbt` | npm | `1.2.48` | 09:55–12:14 UTC (~2h19m) |
| Apr 30 | `lightning` (PyTorch Lightning) | PyPI | `2.6.2`, `2.6.3` | Hours |
| Apr 30 | `intercom-client` | npm | `7.0.5` | Hours |

### How the Bitwarden Compromise Worked

Bitwarden's CI/CD pipeline used `checkmarx/ast-github-action`, which had been compromised in the March 2026 Checkmarx operation. Attackers leveraged GitHub's fork object store and OIDC-based trusted publishing to route a malicious tarball through Bitwarden's own release infrastructure without leaving a standard branch history trail. The malicious version (`@bitwarden/[email protected]`) was live from 17:57–19:30 ET on April 22 before detection.

Bitwarden end-user password vaults were **not affected**. The attack targeted developer tooling and CI/CD token exposure.

### How the SAP Compromise Worked

The four `@cap-js` packages were compromised by account takeover of `RoshniNaveenaS`'s GitHub account, then pushing a modified workflow to a non-main branch. The attacker exploited a **critical configuration gap in npm's OIDC trusted publisher setup**: `@cap-js/sqlite` was configured to trust *any workflow* in the `cap-js/cds-dbs` repo with `id-token: write` permission and `environment: npm` reference — not just the canonical `release-please.yml` on `main`. A branch push was sufficient to exchange an OIDC token and publish to npm without long-lived credentials. For `mbt`, a static npm token (`cloudmtabot`) was compromised via an undetermined channel.

### Safe Versions (Wave 1)

| Package | Safe Version |
|---|---|
| `@cap-js/sqlite` | v2.4.0, v2.3.0 |
| `@cap-js/postgres` | v2.3.0, v2.2.2 (re-released clean) |
| `@cap-js/hana` | v2.8.0, v2.7.2 |
| `@cap-js/db-service` | v2.10.1 (re-released clean) |
| `mbt` | v1.2.49 |
| `@bitwarden/cli` | All versions other than `2026.4.0` |

---

## Wave 2: TanStack (May 11, 2026)

The May 11 wave targeted the **TanStack router** ecosystem, exploiting a `pull_request_target` misconfiguration in TanStack's `bundle-size.yml` GitHub Actions workflow that checked out and executed fork-controlled code. The attacker opened PR #7378 titled "WIP: simplify history build" to trigger the vulnerability.

From there, the worm used the same memory-extraction technique (and verbatim Python script with attribution comment) used in the `tj-actions/changed-files` compromise of March 2025 to steal runner-memory secrets, then republished packages using stolen OIDC tokens — **forging Sigstore-compatible in-toto provenance attestations for every re-published package.**

**This is the first documented case of an npm worm producing validly-attested SLSA Build Level 3 provenance for malicious packages.** The attestations correctly attest that packages were built and published by `release.yml` running on `refs/heads/main` in the `TanStack/router` repository — because they were, by the attacker-controlled workflow.

By end of day: **373 malicious versions**, **169 downstream packages**, across `@tanstack`, `@uipath`, `@mistralai`, `@squawk`, and `@draftlab` namespaces. Socket detected compromised artifacts within **six minutes** of publication.

CVE assigned: **CVE-2026-45321**

---

## Technical Analysis — Payload

### Infection Chain

```
npm install <package>
    ↓
preinstall hook → setup.mjs executes automatically
    ↓
Downloads Bun JavaScript runtime from GitHub Releases
(follows HTTP redirects without destination validation)
(Windows: PowerShell with -ExecutionPolicy Bypass)
    ↓
execution.js (11MB obfuscated payload) runs
    ↓
System locale check → exit immediately if Russian locale
    ↓
Credential harvest + cloud secret sweep
    ↓
AES-256-GCM encrypt → RSA-4096 wrap (attacker's embedded public key)
    ↓
Exfiltrate to public GitHub repos (victim's own account)
    ↓
Self-propagation using stolen tokens
    ↓
Inject persistence into victim repos (.claude/settings.json, .vscode/tasks.json)
```

### Credential Harvesting Scope

- GitHub personal access tokens (PATs) and OAuth tokens
- npm publish tokens
- AWS, Azure, GCP, Kubernetes secrets and service account tokens
- GitHub Actions secrets (including extraction from runner memory)
- Browser passwords: Chrome, Safari, Edge, Brave, Chromium
- Cryptocurrency wallets
- Claude / MCP configuration files
- SSH keys, VPN configurations
- HashiCorp Vault credentials, Kubernetes service endpoints
- Stripe, Slack, Twilio API keys (via regex-based secret matching)
- Database connection strings

### Exfiltration

- **Primary:** Public GitHub repositories created under victim accounts, description: `"A Mini Shai-Hulud has Appeared"` — **1,100+ repositories** created as of late April 2026
- **Secondary (post-Bitwarden/SAP):** Dedicated C2 infrastructure `zero.masscan.cloud` / `94.154.172[.]43/v1/telemetry`; also fronted via `audit.checkmarx[.]cx`
- **Dynamic fallback:** GitHub commit search for keyword `"beautifulcastle"` to retrieve base64-encoded exfil endpoint config — allows attacker to update infrastructure without new package releases
- **Encryption:** AES-256-GCM + RSA-4096; only the attacker can decrypt exfiltrated data

### Self-Propagation (The Worm)

If a GitHub PAT is discovered on the victim machine, the malware:
1. Enumerates all GitHub repos accessible to the token
2. Clones each repo, injects a modified `package.json` with the preinstall hook + Bun binary
3. Bumps patch version (e.g. 1.2.3 → 1.2.4)
4. Re-packs and publishes to npm using stolen npm token under the victim's identity
5. Commits the worm payload to victim repos under attacker-controlled branch names

This results in secondary victims installing malicious packages that carry **the legitimate maintainer's identity**, requiring no further attacker action.

### Novel TTPs

| TTP | First Seen | Detail |
|---|---|---|
| AI coding agent config persistence | Apr 29, 2026 | `.claude/settings.json` abuses Claude Code `SessionStart` hook; `.vscode/tasks.json` with `"runOn": "folderOpen"` triggers on repo open in VS Code |
| Cross-ecosystem propagation | Apr 30, 2026 | npm worm spreading to PyPI within same campaign cycle |
| Valid SLSA BL3 provenance on malicious packages | May 11, 2026 | Forged Sigstore in-toto attestations; provenance verification alone insufficient as defense |
| GitHub commit search as dead-drop C2 | Apr 30, 2026 | Dynamic C2 endpoint retrieval via `"beautifulcastle"` commit search; survives domain takedowns |
| Session P2P C2 | May 11, 2026 | Peer-to-peer command channel established between compromised developer sessions |

---

## Attribution — TeamPCP

| Field | Detail |
|---|---|
| **Designation** | TeamPCP |
| **Social** | @pcpcats — publicly claimed credit |
| **Nexus** | Russia-assessed |
| **Confidence** | High — shared RSA public key, `ctf-scramble-v2` PRNG (seeded `0x3039`), Russian locale exit guard consistent across all waves |
| **Motive** | Financial — credential harvest → ransomware deployment via **Vect RaaS** partnership |
| **Targeting preference** | Security/developer tools with privileged CI/CD access: Trivy, Checkmarx, Bitwarden, then developer ecosystem packages |
| **Prior operations** | Sept 2025: Shai-Hulud 1.0 npm worm · Dec 2025: Shai-Hulud 2.0 · Mar 2026: Checkmarx GitHub Actions + OpenVSX + Trivy |
| **Vect RaaS link** | Confirmed operational partnership; stolen credentials converted to ransomware deployment events; treat any confirmed exposure as **ransomware precursor** |

---

## IOCs

### Network

| Type | Indicator | Notes |
|---|---|---|
| Domain | `zero.masscan.cloud` | Primary C2 (Wave 1 update/Wave 2) |
| Domain | `audit.checkmarx[.]cx` | C2 alias / front domain |
| Domain | `git-tanstack[.]com` | Wave 2 (TanStack) C2 |
| Domain | `seed1[.]getsession[.]org` | Wave 2 C2 |
| IP | `94.154.172[.]43` | C2 server (`/v1/telemetry`) |

### GitHub Indicators

| Type | Indicator |
|---|---|
| Repo description | `"A Mini Shai-Hulud has Appeared"` |
| Repo description | `"Shai-Hulud Repository"` / `"Shai-Hulud Migration"` |
| Commit message prefix | `OhNoWhatsGoingOnWithGitHub:[base64]` |
| Branch name | `shai-hulud` |
| Workflow file | `.github/workflows/discussion.yaml` |
| Workflow file | `shai-hulud-workflow.yml` |
| Runner name | `SHA1HULUD` |

### Filesystem / Package Artifacts

| Type | Indicator |
|---|---|
| Files | `setup.mjs`, `execution.js` |
| Files | `cloud.json`, `environment.json`, `truffleSecrets.json` |
| Persistence | `.claude/settings.json` (injected into repos) |
| Persistence | `.vscode/tasks.json` (injected into repos) |
| Injected file | `router_init.js` (Wave 2 TanStack) |
| Package dependency | `optionalDependencies` entry pointing to malicious package (Wave 2) |

### Malicious Package Versions (Wave 1)

| Package | Malicious Version |
|---|---|
| `@bitwarden/cli` | `2026.4.0` |
| `@cap-js/sqlite` | `2.2.2` |
| `@cap-js/postgres` | `2.2.2` |
| `@cap-js/db-service` | `2.10.1` |
| `mbt` | `1.2.48` |
| `lightning` | `2.6.2`, `2.6.3` |
| `intercom-client` | `7.0.5` |

---

## MITRE ATT&CK Mapping

| Tactic | Technique | ID | Notes |
|---|---|---|---|
| Initial Access | Supply Chain Compromise | T1195.001 | Poisoned npm/PyPI packages via CI/CD and account compromise |
| Execution | Command and Scripting Interpreter: JavaScript | T1059.007 | Bun runtime + execution.js payload |
| Persistence | Event Triggered Execution | T1546 | `.claude/settings.json` SessionStart hook; `.vscode/tasks.json` folderOpen trigger |
| Credential Access | Credentials from Password Stores | T1555.003 | Browser password extraction (Chrome, Safari, Edge, Brave, Chromium) |
| Credential Access | Credentials In Files | T1552.001 | Sweep for AWS/Azure/GCP/K8s/Vault secrets in environment and config files |
| Collection | Data from Local System | T1005 | Developer credential and wallet harvesting |
| Collection | Data from Information Repositories | T1213 | GitHub Actions secrets, runner memory extraction |
| C2 | Web Service: Dead Drop Resolver | T1102.001 | GitHub commit search (`"beautifulcastle"`) used as dynamic C2 endpoint resolver |
| Exfiltration | Exfiltration Over Web Service | T1567.001 | Encrypted data posted to public GitHub repos under victim accounts |
| Impact | Resource Hijacking | T1496 | Stolen tokens used to republish poisoned packages under victim identities |
| Lateral Movement | Taint Shared Content | T1080 | Worm injects malicious payload into victim's own npm packages for downstream propagation |

---

## Remediation Checklist

**If any malicious package version was installed in your environment:**

1. **Identify exposure** — check `package-lock.json`, `yarn.lock`, or `pnpm-lock.yaml` for any malicious versions listed above; search CI/CD logs for install timestamps
2. **Search for artifacts** — scan developer machines and CI runners for `setup.mjs`, `execution.js`, `cloud.json`, `environment.json`, `truffleSecrets.json`, Bun binaries in unexpected paths
3. **Hunt GitHub exfil repos** — search your GitHub org/account for public repos with `"A Mini Shai-Hulud has Appeared"` or `"Shai-Hulud"` in description; search commit history for `OhNoWhatsGoingOnWithGitHub:` prefix
4. **Rotate all secrets** — GitHub PATs, npm tokens, AWS/Azure/GCP credentials, Kubernetes tokens, CI/CD secrets, SSH keys, browser-stored passwords, Vault credentials
5. **Audit GitHub Actions** — look for unexpected workflows (`discussion.yaml`, `shai-hulud-workflow.yml`), self-hosted runners named `SHA1HULUD`, any PRs from unknown forks triggering `pull_request_target` workflows
6. **Check npm publish history** — verify no unexpected versions of your maintained packages were published in the April–May 2026 window
7. **Inspect repo for persistence files** — search all repos for injected `.claude/settings.json` and `.vscode/tasks.json` files
8. **Block C2** — `zero.masscan.cloud`, `audit.checkmarx[.]cx`, `94.154.172[.]43`, `git-tanstack[.]com` at perimeter
9. **Initiate ransomware-level IR** — given Vect RaaS link, treat any confirmed exposure as a ransomware precursor event; do not limit response to credential rotation alone

---

## Hardening Recommendations

- **Pin all third-party GitHub Actions to full commit SHAs** (not tags or branch names)
- **Restrict OIDC trusted publishing to specific branches AND workflow files** — not repo-wide
- **Add `npm install --ignore-scripts`** to CI pipelines; evaluate all `preinstall`/`postinstall` hooks
- **Implement 7-day release cooldown** for dependency upgrades in production CI (malicious versions were live for ~3h; a 7-day cooldown would have fully protected against Wave 1)
- **Do not rely on SLSA provenance alone** — Wave 2 produced valid BL3 attestations for malicious packages; combine with behavioral analysis and known-signature checking
- **Restrict `pull_request_target` workflows** from checking out or executing fork-controlled code

---

## Campaign Timeline

| Date | Event |
|---|---|
| Sept 2025 | Original Shai-Hulud npm worm launched by TeamPCP |
| Dec 2025 | Shai-Hulud 2.0 — refined payload, wider cloud provider targeting |
| Mar 2026 | Checkmarx GitHub Actions + OpenVSX + Aqua Trivy compromised by TeamPCP |
| Apr 22, 2026 | `@bitwarden/[email protected]` published; live 17:57–19:30 ET (~90 min); ~334 developers exposed |
| Apr 22, 2026 | Dataminr, Wiz detect and flag; Bitwarden deprecates malicious version |
| Apr 29, 2026 | Four SAP `@cap-js` packages + `mbt` compromised; published 09:55–12:14 UTC |
| Apr 29, 2026 | Aikido, OX Security, Socket, StepSecurity, SafeDep, Wiz, Google Wiz publish simultaneous alerts |
| Apr 29, 2026 | Wiz confirms TeamPCP attribution; "Shai-Hulud: The Third Coming" string found in payload |
| Apr 30, 2026 | `lightning` (PyPI) versions 2.6.2 + 2.6.3 published; PyPI quarantines within hours |
| Apr 30, 2026 | `intercom-client@7.0.5` (npm) compromised; expanded C2 (`zero.masscan.cloud`) discovered |
| Apr 30, 2026 | 1,100+ GitHub exfil repositories observed; campaign crosses npm → PyPI boundary |
| May 11, 2026 | TeamPCP opens PR #7378 against TanStack/router triggering `pull_request_target` misconfiguration |
| May 11, 2026 | 42 `@tanstack` packages compromised; 373 malicious versions; 169 downstream packages |
| May 11, 2026 | Socket detects compromised artifacts within **6 minutes**; CVE-2026-45321 assigned |
| May 11, 2026 | Snyk, SlowMist publish alerts; SLSA BL3 provenance forgery confirmed |
| May 18, 2026 | Campaign profile created; worm self-propagation ongoing |

---

## References

- [The Hacker News — SAP npm Packages Compromised by Mini Shai-Hulud](https://thehackernews.com/2026/04/sap-npm-packages-compromised-by-mini.html)
- [Wiz — Mini Shai-Hulud Supply Chain SAP npm](https://www.wiz.io/blog/mini-shai-hulud-supply-chain-sap-npm)
- [Lyrie.ai — Mini Shai-Hulud, TeamPCP, and the Supply Chain Attack Post-Mortem](https://lyrie.ai/research/research/mini-shai-hulud-teampcp-multi-ecosystem-supply-chain-post-mortem)
- [Lyrie.ai — The Worm That Won't Stop: TeamPCP's Mini Shai-Hulud](https://lyrie.ai/research/research/mini-shai-hulud-teampcp-supply-chain-worm-pytorch-lightning-sap-npm-pypi)
- [Sophos — Mini Shai-Hulud supply chain attack targets SAP npm packages](https://www.sophos.com/en-us/blog/-mini-shai-hulud-supply-chain-attack-targets-sap-npm-packages)
- [Dataminr — Bitwarden CLI Backdoored via Compromised CI/CD Pipeline](https://www.dataminr.com/resources/intel-brief/cyber-intel-brief-bitwarden-cli-backdoored-via-compromised-ci-cd-pipeline-active-npm-supply-chain-campaign-classification/)
- [Snyk — Mini Shai-Hulud Is Back: TanStack Wave](https://snyk.io)
- [Unit 42 — Palo Alto Networks Supply Chain Campaign Post-Mortem](https://unit42.paloaltonetworks.com)
- [CryptoTimes — Mini Shai-Hulud Malware Targets Crypto Wallets via npm Packages](https://www.cryptotimes.io/2026/05/12/mini-shai-hulud-malware-targets-crypto-wallets-via-npm-packages/)

---

*Profile created: 2026-05-18 | Author: C3PO | Admiralty Grade: A1 | TLP: WHITE*
