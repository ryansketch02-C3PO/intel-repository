# 🔍 Stardust Chollima — Indicators of Compromise (IOCs)

**TLP:** WHITE  
**Last Updated:** 2026-03-31  

---

## Network IOCs

### Domains
| Domain | Campaign | Notes |
|---|---|---|
| zoom-tech[.]us | Deepfake Zoom (2025) | Registered April 14, 2025; fake Zoom audio repair lure |

> ⚠️ Stardust Chollima rotates infrastructure frequently. Treat domain indicators as campaign-specific, not persistent. Monitor for newly registered Zoom/VC/fintech impersonation domains.

---

## File / Malware Hashes

| Hash (SHA256) | Family | Source |
|---|---|---|
| 6f3e849ee0fe7a6453bd0408f0537fa894b17fc55bc9d1729ae035596f5c9220 | Stardust Chollima malware (2024) | PolySwarm |

---

## Malware Families

| Malware | Platform | Description |
|---|---|---|
| **RustBucket** | macOS | Rust-based backdoor; PDF viewer lure delivery |
| **ObjCShellz** | macOS | Objective-C shell command execution backdoor |
| **SpectralBlur** | macOS | Multi-capability backdoor; active late 2023–early 2024 |
| **NimDoor RAT** | macOS | RAT delivered via fake Zoom SDK updates |
| **TwoPence** | Windows | Core implant framework — Enigma Protector hardened |
| **Hawup** | Windows | Shared framework with Lazarus/LABYRINTH lineage |
| **Hermes** | Windows | Ransomware used in SWIFT heist operations |
| **Custom MetaMask** | Chrome | Malicious extension; intercepts/redirects crypto transactions |

---

## Behavioral IOCs

- AppleScript execution from unexpected sources (especially during/after video calls)
- Zoom-related domains **not** ending in `zoom.us` or `zoom.com`
- curl/zsh executing downloaded payloads from recently registered domains
- Chrome/browser in Developer Mode with MetaMask installed from local directory (not Chrome Web Store)
- Credential prompt dialogs during or immediately after a video call
- Keylogger artifacts on macOS systems of finance/crypto employees
- Screen capture utilities not installed by IT running silently
- Clipboard access from non-standard processes
- Compiled binaries built with Microsoft Visual Studio 6 (extreme rarity — strong indicator)

---

## Social Engineering Indicators

- Unexpected Calendly/scheduling links from "known contacts" leading to video meetings
- Video call participants whose audio/video quality is unusually perfect or robotic
- Call participants who cannot hear you but ask you to download tools/extensions
- "Audio repair" scripts or Zoom extensions not from the official Zoom website
- Documents titled as contracts, NDAs, or investment proposals from VC-adjacent contacts
- ISO or VHD file attachments from external senders in finance/crypto contexts

---

## Infrastructure Patterns

- Newly registered domains mimicking: Zoom, legitimate VC firms, crypto exchanges, financial institutions
- Domain registration ~2–8 weeks before campaign activity begins
- Multi-stage payload delivery: initial script → curl download → credential harvest → cleanup

---

## CVEs Associated

No specific CVEs currently attributed to Stardust Chollima in open-source reporting. Group primarily relies on social engineering over vulnerability exploitation for initial access, though zero-days have been referenced in related DPRK actor reporting.
