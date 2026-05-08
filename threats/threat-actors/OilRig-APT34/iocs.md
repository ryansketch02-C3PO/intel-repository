# OilRig (APT34) — Indicators of Compromise

> Last updated: 2026-05-08 | Source grading per Admiralty Scale | TLP: WHITE
> All IPs/domains defanged with [.] notation. Remove brackets before use in tools.

---

## 2025 Campaign Infrastructure (Iraq / Dual-Channel C2)
*Source: ThreatBook / CERTFA — March 2025 | Admiralty: A2*

### IP Addresses

| IP | Notes |
|---|---|
| 130[.]0.233.63 | C2 — HTTP channel (fake 404 infrastructure) |
| 151[.]236.17.231 | C2 — HTTP channel |
| 185[.]76.78.177 | C2 — HTTP channel |
| 192[.]71.166.24 | C2 — HTTP channel |
| 193[.]36.132.224 | C2 — HTTP channel |
| 198[.]44.140.29 | C2 — HTTP channel |
| 38[.]180.31.225 | C2 — HTTP channel |
| 89[.]46.233.239 | C2 — HTTP channel |
| 91[.]132.95.117 | C2 — HTTP channel |
| 95[.]156.204.168 | C2 — HTTP channel |

### Domains

| Domain | Notes |
|---|---|
| asiacall[.]net | C2 domain — Iraq campaign |
| fastasia[.]shop | C2 domain — Iraq campaign |
| iqwebservice[.]com | C2 domain — Iraq campaign |
| livekv[.]com | C2 domain — Iraq campaign |
| mytrustiq[.]com | C2 domain — Iraq campaign |
| sidaop[.]com | C2 domain — Iraq campaign |

### File Hashes

| Hash | Type | Malware Family |
|---|---|---|
| `e7d52ef521b8cd0b405575c185d64033` | MD5 | Iraq C# campaign payload |
| `6bb4414d717a290b80cca32655b7198f` | SHA1 | Iraq C# campaign payload |
| `b607d60d680f1f1335902a666df843ac9cc58299af6731d2ad1a5ea617cf4a99` | SHA256 | Iraq C# campaign payload |

---

## 2024–2025 Pre-Staging Infrastructure (M247 Cluster)
*Source: Intertec Systems passive DNS analysis — April 2025 | Admiralty: A2*

### IP Addresses

| IP | Notes |
|---|---|
| 38[.]180.18.189 | M247-hosted staging server; biam-iraq[.]org resolved here Nov 2024–Mar 2025 |
| 38[.]180.140.30 | M247-hosted staging server; biam-iraq[.]org resolved here Mar 2025; static 404 "Document" on :8080 |

### Domains

| Domain | Notes |
|---|---|
| biam-iraq[.]org | Iraqi academic institution impersonation — phishing/credential harvest staging |
| plenoryvantyx[.]eu | Algorithmically generated; converged on 38.180.18[.]189; likely future phishing |
| zyverantova[.]eu | Algorithmically generated; converged on 38.180.18[.]189 |
| valtryventyx[.]eu | Algorithmically generated; converged on 38.180.18[.]189 |
| valtorynexon[.]eu | Algorithmically generated; converged on 38.180.18[.]189 |
| axoryvexity[.]eu | Algorithmically generated; converged on 38.180.18[.]189 |

### Infrastructure Fingerprint Signatures

| Indicator | Value | Notes |
|---|---|---|
| HTTP response body (port 8080) | Static 404 page titled "Document" | Content-Length: 338 bytes — APT34 server config signature as of March 2025 |
| SSH banner | `SSH-2.0-OpenSSH_8.0` | Reused across 5 M247 servers Mar 21–29, 2025 |
| Registrar | P.D.R. Solutions (US) LLC | Used for .eu domain cluster |
| Nameserver | regway[.]com | Used for .eu domain cluster |

---

## 2024 STEALHOOK Campaign (UAE/Gulf)
*Source: Trend Micro Earth Simnavaz research — October 2024 | Admiralty: A1*

### File Hashes

| Hash | Type | Malware Family |
|---|---|---|
| `db79c39bc06e55a52741a9170d8007fa93ac712df506632d624a651345d33f91` | SHA256 | STEALHOOK (TrojanSpy.MSIL.STEALHOOK.A) — Update.dll |

---

## Historical / Multi-Campaign IOCs
*Source: ESET, Malpedia, public reporting | Admiralty: B2*

### IP Addresses

| IP | Campaign / Notes |
|---|---|
| 199[.]102.48.42 | Historical C2 — multiple campaigns |

### Domains

| Domain | Campaign / Notes |
|---|---|
| tecforsc-001-site1[.]gtempurl.com | Historical C2 domain |

### File Hashes

| Hash | Type | Malware / Campaign |
|---|---|---|
| `1de4810a10fa2d73cc589ca403a4390b02c6da5e` | SHA1 | Historical payload |
| `2236d4dcf68c65a822ff0a2ad48d4df99761ad07` | SHA1 | Historical payload |
| `3699b67bf4e381847bf98528f8ce2b966231f01a` | SHA1 | Historical payload |

---

## Detection Rules

### DNS Tunneling Pattern (Spearal / Saitama)
Hunt for anomalously long DNS query subdomains from endpoints — Spearal encodes data in Base32 strings embedded as subdomains. Legitimate DNS subdomains are rarely >30 characters; data exfiltration subdomains may reach 50–100+ characters.

```
# Splunk / SIEM pseudo-query
dns.query.name.length > 50
AND dns.query.type IN ("A", "TXT")
AND NOT dns.query.name MATCHES (CDN/legitimate_whitelist)
```

### HTTP C2 Infrastructure Signature (Port 8080 Decoy)
```
http.status_code == 404
AND http.response.body CONTAINS "Document"
AND http.response.headers["Content-Length"] == "338"
AND destination.port == 8080
```

### Password Filter DLL Registration (STEALHOOK / credential capture)
```
# Windows Registry monitoring
registry.key == "HKLM\\SYSTEM\\CurrentControlSet\\Control\\Lsa\\Notification Packages"
AND registry.event == "modify"
AND NOT registry.value IN (known_legitimate_password_filters)
```

### Ngrok Tunneling Detection
```
# Process or DNS-based
process.name IN ("ngrok.exe", "ngrok") AND process.parent NOT IN (known_admin_processes)
OR dns.query.name MATCHES ".*\.ngrok\.io$"
OR dns.query.name MATCHES ".*\.ngrok-free\.app$"
```

### Veaty/PowerExchange Email C2 Hunt (Exchange telemetry)
Monitor Exchange audit logs for mailboxes:
- Accessed by service accounts or processes that are not Outlook clients
- Accessed at unusual hours (OilRig operators are UTC+3:30 / Iran Standard Time)
- Low message volume mailboxes with frequent access (checking for new command emails)

---

## YARA Rules (Community — Reference Only)

```yara
rule APT34_Spearal_DNS_Tunneling {
    meta:
        description = "Detects Spearal .NET backdoor DNS tunneling strings"
        author = "Community"
        reference = "Check Point Research Sep 2024"
    strings:
        $dns_base32 = "ABCDEFGHIJKLMNOPQRSTUVWXYZ234567" ascii
        $net_class = "Spearal" ascii nocase
        $dns_tunnel = { 53 79 73 74 65 6D 2E 4E 65 74 2E 44 6E 73 }  // "System.Net.Dns"
    condition:
        uint16(0) == 0x5A4D and all of them
}

rule APT34_CacheHttp_IIS_Backdoor {
    meta:
        description = "Detects CacheHttp.dll IIS backdoor OnGlobalPreBeginRequest strings"
        author = "Community"
        reference = "Check Point Research Sep 2024"
    strings:
        $iis_event = "OnGlobalPreBeginRequest" ascii
        $cmd_exec = "cmd.exe" ascii
        $upload = "upload" ascii nocase
        $download = "download" ascii nocase
    condition:
        uint16(0) == 0x5A4D and 3 of them
}
```

---

*Created: 2026-05-08 | Author: C3PO | TLP: WHITE*
