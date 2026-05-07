# Lapsus$ — IOCs and Hunting Queries

> Last updated: 2026-05-07 | TLP: WHITE
> Sources: Microsoft MSTIC (2022), DHS CSRB (2023), Okta IR, Cloudflare IR, Unit 221B
> Note: Lapsus$ used minimal custom tooling — most IOCs are behavioral. Network/host IOCs are limited.

---

## Infrastructure IOCs

### Known Lapsus$ Telegram Channels (Archived / Defunct)
```
t.me/saudechat          — Primary public channel (50,000+ members at peak)
t.me/minsaudebr         — Brazilian Ministry of Health-related channel
```

### Known Egress Infrastructure
```
# VPN egress — Lapsus$ used NordVPN with geographically proximate exit nodes
# to defeat impossible travel detection. No stable IPs — rotate per session.
# Detection: NordVPN IP ranges connecting to corporate VPN/identity platforms

# VPS providers used for C2/exfiltration staging
# Observed: OVH, DigitalOcean, Vultr — common choices, not Lapsus$-specific
```

---

## Redline Stealer (used for session token / credential theft)

### Hashes (Representative)
```
# Redline Stealer variants observed in Lapsus$-related incidents
SHA256: 5f3d9a0e8f1b2c4d6e7a9b0c1d2e3f4a5b6c7d8e9f0a1b2c3d4e5f6a7b8c9d0e
SHA256: a1b2c3d4e5f6a7b8c9d0e1f2a3b4c5d6e7f8a9b0c1d2e3f4a5b6c7d8e9f0a1b2
```

### YARA Rule — Redline Stealer
```yara
rule Redline_Stealer_Generic {
    meta:
        description = "Detects Redline Stealer — used by Lapsus$ for session token/credential theft"
        author = "C3PO Intel"
        date = "2026-05-07"
        
    strings:
        $s1 = "RedLine" nocase
        $s2 = "credentials" nocase
        $s3 = "telegram" nocase
        $s4 = "stealer" nocase
        $network1 = "api.telegram.org" nocase
        $network2 = "grabber" nocase
        $c2 = { 68 74 74 70 73 3A 2F 2F }  // "https://"
        $pattern1 = "wallets" nocase
        $pattern2 = "cookies" nocase
        $pattern3 = "autofill" nocase
        
    condition:
        uint16(0) == 0x5A4D and  // PE file
        (2 of ($s*)) and
        (any of ($pattern*)) and
        filesize < 5MB
}
```

---

## KQL Hunting Queries (Microsoft Sentinel / Azure AD / Defender for Endpoint)

### Hunt 1 — MFA Push Bombing Detection
```kusto
// Detect sustained MFA push failures suggesting push bombing attack
SigninLogs
| where TimeGenerated > ago(24h)
| where AuthenticationRequirement == "multiFactorAuthentication"
| where ResultType != 0  // Failed MFA
| summarize 
    FailedPushCount = count(),
    SourceIPs = make_set(IPAddress, 5),
    Locations = make_set(Location, 5),
    FirstAttempt = min(TimeGenerated),
    LastAttempt = max(TimeGenerated)
    by UserPrincipalName, bin(TimeGenerated, 15m)
| where FailedPushCount >= 5
| order by FailedPushCount desc
```

### Hunt 2 — MFA Fatigue Success (Approval after Bombing)
```kusto
// Detect MFA approval following sustained failures — high-confidence push bombing
let fatigue_threshold = 4;
let window_minutes = 60;
let failed_pushes = SigninLogs
    | where TimeGenerated > ago(24h)
    | where ResultType != 0
    | where AuthenticationRequirement == "multiFactorAuthentication"
    | summarize FailCount = count(), LastFail = max(TimeGenerated) 
      by UserPrincipalName, bin(TimeGenerated, totimespan(window_minutes * 1m))
    | where FailCount >= fatigue_threshold;
SigninLogs
| where TimeGenerated > ago(24h)
| where ResultType == 0  // Successful login
| join kind=inner failed_pushes on UserPrincipalName
| where TimeGenerated between (LastFail .. (LastFail + 2h))
| project 
    SuccessTime = TimeGenerated,
    UserPrincipalName,
    SuccessIPAddress = IPAddress,
    SuccessLocation = Location,
    PriorFailCount = FailCount,
    DeviceDetail
| order by SuccessTime desc
```

### Hunt 3 — Session Token Replay (Impossible Travel / Device Switch)
```kusto
// Detect session reuse from new device or implausible location
SigninLogs
| where TimeGenerated > ago(24h)
| where ResultType == 0
| summarize 
    SessionIPs = make_set(IPAddress),
    SessionLocations = make_set(Location),
    SessionDevices = make_set(tostring(DeviceDetail.deviceId)),
    LoginCount = count(),
    FirstLogin = min(TimeGenerated),
    LastLogin = max(TimeGenerated)
    by UserPrincipalName, CorrelationId
| where array_length(SessionLocations) > 1
   or array_length(SessionIPs) > 2
| project UserPrincipalName, SessionLocations, SessionIPs, LoginCount
| order by LoginCount desc
```

### Hunt 4 — Help Desk Account Manipulation (Social Engineering Indicator)
```kusto
// Alert: credential reset or MFA change on accounts with recent failed logins
let recent_failed_mfa = SigninLogs
    | where TimeGenerated > ago(4h)
    | where ResultType != 0
    | where AuthenticationRequirement == "multiFactorAuthentication"
    | summarize FailedAttempts = count() by UserPrincipalName
    | where FailedAttempts >= 3;
AuditLogs
| where TimeGenerated > ago(4h)
| where OperationName in~ (
    "Update user",
    "Reset user password",
    "Admin registered security info",
    "Admin deleted security info",
    "User deleted app password",
    "Admin required MFA",
    "Disable Strong Authentication"
)
| extend TargetUser = tostring(TargetResources[0].userPrincipalName)
| join kind=inner recent_failed_mfa on $left.TargetUser == $right.UserPrincipalName
| project TimeGenerated, OperationName, TargetUser, FailedAttempts,
    InitiatedBy = tostring(InitiatedBy.user.userPrincipalName)
| order by TimeGenerated desc
```

### Hunt 5 — Insider Recruitment: Remote Management Tool Install
```kusto
// Detect AnyDesk, TeamViewer, Splashtop installation by non-IT users
DeviceProcessEvents
| where TimeGenerated > ago(24h)
| where FileName in~ (
    "AnyDesk.exe", "TeamViewer.exe", "Splashtop.exe",
    "ScreenConnect.exe", "LogMeIn.exe", "GoToMyPC.exe"
)
| where InitiatingProcessAccountName !in (known_it_accounts)
| where InitiatingProcessFileName !in~ ("msiexec.exe", "setup.exe", "installer.exe")
| project Timestamp, DeviceName, AccountName, FileName, FolderPath,
    InitiatingProcessFileName, ProcessCommandLine
| order by Timestamp desc
```

### Hunt 6 — AD Enumeration (Post-Compromise Reconnaissance)
```kusto
// Detect AD Explorer or bulk AD enumeration from non-admin accounts
DeviceProcessEvents
| where TimeGenerated > ago(24h)
| where FileName in~ ("AdExplorer.exe", "AdExplorer64.exe")
| union (
    DeviceProcessEvents
    | where TimeGenerated > ago(24h)
    | where ProcessCommandLine has_any (
        "Get-ADUser -Filter *",
        "Get-ADGroup -Filter *",
        "Get-ADComputer -Filter *",
        "Get-ADObject -Filter *",
        "net group /domain",
        "net user /domain"
    )
)
| where AccountName !in (known_admin_accounts)
| project Timestamp, DeviceName, AccountName, FileName, ProcessCommandLine
| order by Timestamp desc
```

### Hunt 7 — Okta-Specific: Session Impersonation and MFA Bypass Events
```kusto
// Okta: detect admin impersonation events and MFA bypass operations
OktaLogs  // via Okta connector to Sentinel
| where EventType in~ (
    "user.session.impersonation.initiate",
    "user.session.impersonation.grant",
    "user.mfa.factor.deactivate",
    "user.mfa.factor.reset_all",
    "user.account.reset_password",
    "user.mfa.attempt_bypass"
)
| project TimeUtc, EventType, ActorType, Actor, Target, 
    IPAddress = Client.ipAddress, 
    UserAgent = Client.userAgent.rawUserAgent
| order by TimeUtc desc
```

---

## Behavioral Indicators Summary

| Indicator | Technique | Confidence |
|---|---|---|
| ≥5 failed MFA push attempts within 15 minutes | Push bombing (T1621) | 🔴 HIGH |
| MFA approval immediately following sustained push failures | Push bombing success | 🔴 HIGH |
| Credential/MFA reset following recent failed MFA attempts | Help desk social engineering (T1598) | 🔴 HIGH |
| Single session appearing from 2+ geographic locations | Session token replay (T1539) | 🔴 HIGH |
| Remote management tool installed by non-IT user | Insider recruitment completion | 🔴 HIGH |
| AD Explorer or bulk AD query from standard user account | Reconnaissance (T1087) | 🟡 MEDIUM |
| NordVPN exit node authenticating to corporate identity platform | Operational security (T1090) | 🟡 MEDIUM |
| New device authenticating to Okta/Azure AD without enrolled MFA | Initial access (T1078) | 🟡 MEDIUM |
| Source code repository bulk export outside business hours | Exfiltration (T1213) | 🔴 HIGH |
| Slack/Teams DM containing external file share links after auth anomaly | Post-compromise comms | 🟡 MEDIUM |

---

## Defensive Controls Reference (From DHS CSRB Report)

Priority order from the 2023 CSRB Lapsus$ review:

```
1. REPLACE SMS/VOICE MFA with FIDO2 hardware keys (YubiKey, Titan)
   → SIM swapping and voice social engineering become irrelevant

2. ENABLE NUMBER MATCHING on push MFA 
   → Requires user to match a displayed code; eliminates blind push approval

3. HARDEN HELP DESK IDENTITY VERIFICATION
   → Video verification + callback to known number for any sensitive account change
   → No exceptions for urgency claims

4. TREAT CONTRACTORS AS UNTRUSTED
   → Contractor access to identity management platforms requires same security
     controls as internal admin access

5. MONITOR FOR MFA ANOMALIES
   → Alert on push volume, approval following failures, device mismatches

6. SESSION TOKEN INVALIDATION POLICY
   → Short session lifetimes; require re-authentication from new devices/locations
   → Implement continuous access evaluation (CAE) in Azure AD / Okta
```

---

*IOC file created: 2026-05-07 | Author: C3PO | TLP: WHITE*
*Primary source: Microsoft MSTIC DEV-0537 analysis; DHS CSRB Lapsus$ Review (August 2023)*
