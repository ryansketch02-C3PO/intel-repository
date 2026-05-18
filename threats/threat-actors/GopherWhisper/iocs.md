# GopherWhisper — Indicators of Compromise (IOCs)

> **Last Updated:** 2026-05-18
> **Admiralty Grade:** A2 | TLP:CLEAR
> ⚠️ **C2 channels abuse legitimate services (Slack, Discord, Outlook). Static IP/domain IOCs have limited value. Focus on behavioral detection of API abuse and file.io connections.**

---

## C2 Infrastructure (Known Accounts)

| Indicator | Type | Notes |
|---|---|---|
| `barrantaya.1010@outlook[.]com` | Outlook C2 Account | Earliest known BoxOfFriends C2 account; created July 11, 2024 |
| Private Slack server(s) | Slack C2 | Used by LaxGopher; API tokens extracted by ESET |
| Private Discord server(s) | Discord C2 | Used by RatGopher; specific server IDs not publicly disclosed |

---

## File Indicators

| Indicator | Type | Notes |
|---|---|---|
| `whisper.dll` | DLL | LaxGopher backdoor loaded via JabGopher (side-loaded into svchost.exe) |
| `JabGopher` (various filenames) | EXE/DLL | Injector that deploys LaxGopher |
| `LaxGopher` | Go binary | Slack-based C2 backdoor |
| `RatGopher` | Go binary | Discord-based C2 backdoor |
| `CompactGopher` | Go binary | File collection and AES-CFB-128 encrypted exfiltration to file.io |
| `SSLORDoor` | C++ binary | OpenSSL BIO C2 backdoor on port 443 |
| `FriendDelivery` | DLL | Loader for BoxOfFriends |
| `BoxOfFriends` | Go binary | Outlook Graph API draft-message C2 |

---

## Network Indicators

| Indicator | Type | Notes |
|---|---|---|
| `file[.]io` | Exfiltration destination | CompactGopher exfiltration via AES-CFB-128 encrypted ZIP |
| TCP/443 | Port | SSLORDoor raw socket comms |
| Slack API endpoints | C2 channel | `api.slack.com` connections from non-Slack processes |
| Discord API endpoints | C2 channel | `discord.com/api/` connections from non-standard processes |
| Microsoft Graph API | C2 channel | `graph.microsoft.com` Outlook draft mailbox C2 |

---

## YARA / Behavioral Rules

```
// Hunt for whisper.dll side-load into svchost.exe
rule GopherWhisper_SideLoad {
    strings:
        $dll = "whisper.dll" nocase
    condition:
        $dll
}

// Detect file.io exfiltration from non-browser processes
// Alert on: POST to file.io from processes other than browsers/curl
// Especially CompactGopher pattern: ZIP archive + AES encryption before upload
```

---

## Detection Priorities

1. **Slack/Discord API access from non-whitelisted processes** — LaxGopher and RatGopher C2
2. **Microsoft Graph API Outlook draft access from unexpected applications** — BoxOfFriends C2
3. **file.io uploads from internal hosts** — CompactGopher exfiltration
4. **whisper.dll loaded into svchost.exe** — JabGopher injector
5. **Outbound port 443 raw socket from non-HTTPS processes** — SSLORDoor

---

## File Extension Targets (Collection Profile)

`.doc`, `.docx`, `.jpg`, `.xls`, `.xlsx`, `.txt`, `.pdf`, `.ppt`, `.pptx`

---

*IOCs compiled: 2026-05-18 | Source: ESET Research WeLiveSecurity | Admiralty Grade: A2 | TLP:CLEAR*
