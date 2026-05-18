# GopherWhisper

## Identity

| Field | Value |
|---|---|
| **Scoundrel #** | 043 |
| **Primary Name** | GopherWhisper |
| **AKA** | None publicly assigned |
| **Type** | State-Sponsored Cyber Espionage |
| **Origin** | People's Republic of China (PRC) |
| **Sponsor** | Unknown (China-aligned; UTC+8 working hours, Slack locale set to China Standard Time) |
| **Base** | Unknown |
| **Active Since** | ~November 2023 |
| **Status** | 🔴 ACTIVE |
| **Threat Level** | 🟠 HIGH |
| **MITRE ATT&CK ID** | Pending |

---

## Discovery

ESET Research discovered GopherWhisper in **January 2025** after finding a previously undocumented backdoor (LaxGopher) on a Mongolian government system. ESET publicly disclosed the group on **April 23, 2026** at the Botconf 2026 conference. The group has been assessed as active since at least November 2023 based on tooling timestamps.

Because the group's malware bears no code similarity to any known threat actor and TTPs show no overlap with existing clusters, ESET attributed the tools to a **new, previously undocumented group**.

---

## Mission

GopherWhisper conducts targeted cyberespionage against government institutions, focusing on **intelligence collection, credential theft, and document exfiltration**. The exclusive targeting of Mongolian governmental entities (Mongolia sharing a border with both China and Russia) aligns with PRC strategic interest in monitoring political and diplomatic activity in its northern buffer zone. ESET's telemetry suggests **dozens of additional victims** beyond the confirmed Mongolian institution, but their geolocations and sectors are not yet publicly known.

---

## Targeting

### Primary Sectors
- 🏛️ **Government / Diplomatic** — Mongolian governmental institutions confirmed; likely broader government targeting
- 📄 **Document-focused** — exfil targets: .doc, .docx, .xls, .xlsx, .pdf, .ppt, .pptx, .txt, .jpg

### Geographic Focus
- Mongolia (confirmed)
- Other countries unknown (ESET estimates dozens of additional victims based on C2 traffic)

---

## TTPs (MITRE ATT&CK)

### Initial Access
| Technique | ATT&CK ID | Notes |
|---|---|---|
| Unknown — Under Investigation | T1190 / T1566 | ESET has not publicly identified initial access vector |

### Execution & Persistence
| Technique | ATT&CK ID | Notes |
|---|---|---|
| DLL Side-Loading | T1574.002 | JabGopher loads LaxGopher as `whisper.dll` via svchost.exe injection |
| Process Injection (svchost) | T1055 | JabGopher creates new svchost.exe instance and injects LaxGopher |
| Malicious DLL (FriendDelivery) | T1574.001 | DLL loader for BoxOfFriends |

### Command & Control
| Technique | ATT&CK ID | Notes |
|---|---|---|
| Application Layer Protocol — Web Services | T1071.001 | Slack API (LaxGopher), Discord API (RatGopher), Microsoft Graph API (BoxOfFriends) |
| Exfiltration Over Web Service | T1567.002 | file.io used for data exfiltration (CompactGopher) |
| Encrypted Channel | T1573 | SSLORDoor uses OpenSSL BIO on port 443 |

### Collection & Exfiltration
| Technique | ATT&CK ID | Notes |
|---|---|---|
| Data from Local System | T1005 | CompactGopher collects documents by extension |
| Archive Collected Data | T1560 | Files compressed into ZIP before exfil |
| Encrypted Exfil | T1022 | AES-CFB-128 encryption before upload to file.io |

---

## Malware & Tools Arsenal

| Tool | Language | Role | Notes |
|---|---|---|---|
| **JabGopher** | Unknown | Injector | Creates svchost.exe, injects LaxGopher (whisper.dll) |
| **LaxGopher** | Go | Backdoor (primary) | Slack-based C2; executes cmd.exe commands; downloads further payloads |
| **CompactGopher** | Go | Exfiltration | Collects files by extension, ZIP+AES-CFB-128 encrypts, uploads to file.io |
| **RatGopher** | Go | Backdoor | Discord-based C2; executes commands and returns output to Discord channel |
| **SSLORDoor** | C++ | Backdoor | Raw socket comms on port 443 via OpenSSL BIO; drive enumeration, file ops |
| **FriendDelivery** | Unknown | Loader/Injector | DLL loader that injects BoxOfFriends |
| **BoxOfFriends** | Go | Backdoor (secondary) | Microsoft Graph API to Outlook mailbox; uses draft email messages as C2 channel |

---

## Notable Operations

| Year | Target | Description |
|---|---|---|
| Nov 2023 – Present | Mongolian Government | Sustained espionage campaign; 12 confirmed infected systems; ESET confirmed via C2 traffic |
| Unknown (est. active) | Dozens of additional victims | C2 traffic from Discord/Slack suggests broader victim set; geolocations unknown |

---

## Key Indicators

- Slack API tokens used as C2 channels (LaxGopher)
- Discord server used as C2 (RatGopher)
- Microsoft Outlook draft messages as C2 (BoxOfFriends) — `barrantaya.1010@outlook[.]com` (oldest known account, created July 11, 2024)
- file.io uploads from non-browser processes
- `whisper.dll` side-loaded into svchost.exe
- UTC+8 working hours in C2 message timestamps

---

## Threat Intelligence Grading

| Field | Value |
|---|---|
| **Source Reliability** | A (ESET — independently confirmed via extracted C2 messages) |
| **Info Credibility** | 2 (Confirmed by technical analysis; attribution high-confidence but sponsor org unknown) |
| **Admiralty Grade** | **A2** |
| **TLP** | TLP:CLEAR |

---

## Attribution

- **ESET Research**: April 23, 2026 — "GopherWhisper: A burrow full of malware" (WeLiveSecurity white paper)
- **Confidence**: China-aligned (UTC+8 operational hours, Slack locale set to China Standard Time)
- **Sponsor**: Unknown; no confirmed link to specific Chinese intelligence agency

---

## Connection Web

- **APT41 (#019)**: Shares China-aligned espionage mandate; no confirmed tool overlap
- **GopherWhisper** is distinct from all known China-nexus clusters per ESET analysis
- **UAT-8302 (#045)**: Another newly disclosed China-nexus APT using legitimate services for C2; different tooling but similar operational pattern

---

## Defensive Recommendations

1. **Monitor for messaging service API abuse** — Slack, Discord, and Microsoft Graph API connections from non-standard processes are high-confidence indicators
2. **Detect DLL side-loading** — Alert on whisper.dll or unknown DLLs loaded into svchost.exe processes
3. **Monitor file.io uploads** — Block or alert on file.io connections from internal endpoints
4. **Audit Outlook draft folders** — Draft-message C2 channels are detectable in mail audit logs
5. **Hunt for BoxOfFriends** — Microsoft Graph API calls from non-whitelisted applications to Outlook accounts

---

## References

- ESET Research WeLiveSecurity: https://www.welivesecurity.com/en/eset-research/gopherwhisper-burrow-full-malware/
- ESET Press Release: https://www.eset.com/us/about/newsroom/research/eset-research-discovers-new-china-aligned-group-gopherwhisper-it-abuses-messaging-services-discord-slack-and-outlook-to-spy/
- The Hacker News: https://thehackernews.com/2026/04/china-linked-gopherwhisper-infects-12.html

---

*Profile created: 2026-05-18 | Next review: 2026-08-18 | Classification: TLP:CLEAR*
