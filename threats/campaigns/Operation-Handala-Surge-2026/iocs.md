# Operation Handala Surge 2026 — IOCs

> C3PO Threat Actor Repository | Campaign IOCs  
> TLP: CLEAR | Confidence: A1

---

## Seized / Known Domains

```
handala-hack[.]to          (primary ops/leak site — seized Mar 19, 2026; rebuilt same day)
handala-redwanted[.]to     (doxxing site — seized Mar 19, 2026)
justicehomeland[.]org      (MOIS shell persona)
karmabelow80[.]org         (MOIS shell persona)
```

## Email Infrastructure

```
Handala_Team@outlook[.]com    (used to send death threats to dissidents/journalists)
```

## Mobile Malware (APK)

```
RedAlert weaponized APK — Android surveillance malware disguised as Israeli emergency alert app
MyCity weaponized APK — used in 2024; persistent device access
```

> File hashes not publicly released as of profile date. Monitor VirusTotal for "RedAlert" + "Handala" tags.

## MDM Attack Vector (Stryker, Mar 11, 2026)

- **Vector:** Microsoft Intune MDM
- **Method:** Compromised admin credentials used to push destructive wiper payload to all enrolled devices
- **Scale:** 200,000+ devices across 79 countries
- **Indicator:** Unusual MDM policy push from unexpected admin account; mass device wipe event

## Behavioral Indicators

- Credential stuffing from public breach databases (HaveIBeenPwned-indexed data)
- LinkedIn scraping for employee targeting (defense/government sectors)
- Mass device wipe events following MDM admin compromise
- Sudden appearance of Handala branding/logo on compromised web infrastructure
- Death threat emails from `Handala_Team@outlook.com` or similar Handala-branded accounts

## Hunting Queries (Conceptual)

```
# MDM abuse detection (Intune)
Alert on: new MDM policy push from account not previously used for policy creation
Alert on: mass device compliance state change (>100 devices) within short window
Alert on: wipe command issued to device group from non-standard admin account

# Credential stuffing detection
Alert on: login success from IP associated with known Iranian IP ranges after multiple failures
Alert on: O365/Entra ID login from impossible travel following breach database publication
```

---

*IOCs last updated: 2026-04-17 | Author: C3PO*  
*Sources: DOJ affidavit (Mar 19, 2026), FBI advisory, Unit 42 Threat Brief*
