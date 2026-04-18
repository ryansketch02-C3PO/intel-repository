# 🐱 Charming Kitten (APT35) — Indicators of Compromise

> **Admiralty Grade:** B2 | **TLP:** TLP:WHITE (publicly sourced)
> Last updated: 2026-04-08

---

## Network Indicators

### IP Addresses (Known Operational / Relay)

| IP | ASN / Provider | Role | Confidence | Source |
|---|---|---|---|---|
| 128.199.237.132 | RIPE / DigitalOcean Singapore | C2 relay / egress node | High | DomainTools Jan 2026 |
| 212.175.168.58 | Turk Telekomunikasyon | Target-side / relay | High | DomainTools Jan 2026 |
| 212.12.178.178 | Nour Communication Co. (Saudi Arabia) | Scanning / relay | High | DomainTools Jan 2026 |
| 1.235.222.140 | IRT-KRNIC-KR (South Korea) | Target-side | High | DomainTools Jan 2026 |
| 109.125.132.66 | Pishgaman Tejarat Sayar DSL (Iran) | Operator-side / staging | High | DomainTools Jan 2026 |
| 83.96.77.227 | Fast Communication Co. (Kuwait) | Target-side | High | DomainTools Jan 2026 |

### Domains

| Domain | Purpose | Source |
|---|---|---|
| systemupdate.info | BellaCPP C2 | Rewterz / Hunt & Hackett Dec 2024 |
| sunrapid.com | Phishing infrastructure | CloudSEK Oct 2025 |
| lydston.com (variants) | Phishing infrastructure | CloudSEK Oct 2025 |
| aecars.store | Fake e-commerce phishing site | CloudSEK Oct 2025 |

### Targeted Infrastructure (Victim-Side Indicators)

| Host | Sector | Notes |
|---|---|---|
| mfa.gov.ct.tr | Government (Turkish MFA) | GAL/mailbox exfiltration target |
| alrabie.com | Corporate | Webshell deployment documented |
| customs.gov.lb | Government (Lebanon Customs) | Exchange exploitation target |
| cnthoth.com | Corporate | Post-exploitation confirmed |
| mail.yousifi.com.kw | Corporate (Kuwait) | Webmail gateway targeted |
| webmail.kccec.com.kw | Corporate (Kuwait) | Exchange targeted |
| services.moj.gov.jo | Government (Jordan MoJ) | Telerik exploitation |
| wise.edu.jo | Academia (Jordan) | PHP CGI RCE (CVE-2012-1823) |

---

## File Indicators

### BellaCiao / BellaCPP

#### MD5 Hashes
```
14f6c034af7322156e62a6c961106a8c
44d8b88c539808bb9a479f98393cf3c7
e24b07e2955eb3e98de8b775db00dc68
8ecd457c1ddfbb58afea3e39da2bf17b
103ce1c5e3fdb122351868949a4ebc77
28d02ea14757fe69214a97e5b6386e95
4c6aa8750dc426f2c676b23b39710903
ac6ddd56aa4bf53170807234bc91345a
36b97c500e36d5300821e874452bbcb2
febf2a94bc59011b09568071c52512b5
ac4606a0e10067b00c510fb97b5bd2cc
```

#### SHA-256 Hashes
```
7e761786ff674de0adcbd62ed6adcb5237b2e23d5e6e2a4799f23037463513c5
5f2c954dc7b35c4d31084ce43d7e5f88ffd803f848dbdd77f3112aca01325d06
5ecbfc037a992fa71b6da1229308840692589f019f5ffd77efaefecd322179f1
1bcb35deb900c2c12f2b96071a24023a026ffb09464cfd446f8bf6d928c1365e
c8ac671b0ab1bddabb229a0f28d0e52b2efbcc7415254ceb720452fa4ac7942a
b2b3e8b3da25215ff5dc3965da852a27fefde958c6d06ddffe36087bd0f6d1a5
6a5f8868456ce708c7d6318c10a27bfb576da1a02ff8f8575d2007f5c2f88e54
d967148e4289fd6b831c4bc9ec5bb808a384bd7d334fb2d1ab3a498f680315c7
8dd77dfd8da749741fa9aadbe82e62b273c1c3565debbb753fafedb18a9f50c0
0f696b7505255119b3ca53f57c2f829fc282d227d7a1577e698687a199800ef1
81b6911bec25c314c256e3e04e1826169359bfcbbed76acd14f18a05baf0a28a
918e70e3f5fdafad28effd512b2f2d21c86cb3d3f14ec14f7ff9e7f0760fd760
bf308e5c91bcd04473126de716e3e668cac6cb1ac9c301132d61845a6d4cb362
```

#### SHA1 Hashes
```
2d22f3744a9dada4638486fb26b3404364c1418b
1dd27a926e98c9305bd1689a9f7c33e0e3070d4b
2873d5c7b215a68bf02697ba169078919aeee474
0431fe37bf744d5416e2f2a3220c5dc696e0fda3
9f6c7d3f02f6e214f56c3a3fc218a564fb8cb3c0
d0eb6cb3ca82d2d2e1d30af4dfe7ed744111c6d7
7697329bd6afaa0874e8ef1ae81ad27baa1863ec
ac5b30aea5f5c36ff6918a7545740f7fb4301650
7995fc3ee9a9a97561774d1652a768d9586c259a
74d53b909c75c5850c0c0eaad71ad98ca3546064
d3d67296c8fe2b10a3626eeee0b9bcc26157de99
```

#### BellaCPP DLL
- Filename: `adhapl.dll`
- Loads: `D3D12_1core.dll` (SSH tunnel loader)

---

## Behavioral / HTTP Indicators

| Indicator | Type | Notes |
|---|---|---|
| `Cookie: mstshash=` | HTTP header | RDP-style probe in web access logs |
| User-Agent: `Pandalytics/2.0` | HTTP UA | Domain discovery / mass reconnaissance tool |
| Path scans: `/.env`, `/sendgrid` config paths | HTTP | Config file harvesting |
| `/?author=`, `/wp-json/wp/v2/users` | HTTP | WordPress enumeration |
| ASPX webshells in Exchange virtual dirs | Filesystem | Post-ProxyShell persistence |
| Stager paths under `ProgramData\` | Filesystem | Check for unusual executables/services |
| Local operator web UI on port 8000 | Network | Seen in toolkit artifacts |
| Outbound to Zitel (AS50810) | Network | Iranian ISP used for C2 egress |

---

## CVEs Actively Exploited

| CVE | Product | Notes |
|---|---|---|
| CVE-2024-1709 | ConnectWise ScreenConnect | Day-1 exploitation observed Feb–Mar 2024 |
| CVE-2021-34473 / 34523 / 31207 | Microsoft Exchange (ProxyShell) | Core Exchange exploitation chain |
| CVE-2021-26855 | Microsoft Exchange (ProxyLogon) | Widely used |
| CVE-2021-44228 | Apache Log4j (Log4Shell) | Initial access vs. exposed servers |
| CVE-2022-47966 | Zoho ManageEngine | Initial access; BellaCiao delivery vector |
| CVE-2019-18935 | Telerik UI for ASP.NET | Deserialization RCE |
| CVE-2017-11317 | Telerik | File upload exploitation |
| CVE-2012-1823 | PHP CGI | Legacy system exploitation |
| CVE-2017-3506 | Oracle WebLogic | Web service component exploitation |

---

## MITRE ATT&CK Mappings

| Technique | ID | Notes |
|---|---|---|
| Phishing: Spearphishing Link | T1566.001 | Primary initial access vector |
| Phishing: Spearphishing Attachment | T1566.002 | Secondary |
| Phishing for Information | T1598 | Credential harvesting portals |
| PowerShell | T1059.001 | POWERSTAR, PowerLess, ProxyShell automation |
| Application Layer Protocol: Web | T1071.001 | C2 over HTTPS |
| Application Layer Protocol: DNS | T1071.004 | DNS tunneling |
| Web Service | T1102 | Cloud services (Azure, OneDrive, Google Drive) for C2 |
| Email Collection | T1114 | Exchange/Gmail exfiltration; Hyperscrape |
| Acquire Infrastructure: Domains | T1583.001 | Phishing domain registration |
| Compromise Infrastructure: Domains | T1584.001 | Hijacked/compromised domains |
| OS Credential Dumping: LSASS | T1003.001 | Mimikatz-style LSASS dumps |
| Steal Web Session Cookie | T1539 | MFA bypass / session hijacking |

---

## Sources

- Rewterz BellaCPP Advisory (Dec 2024): https://rewterz.com/threat-advisory/iran-linked-apt35-spreads-new-bellaciao-malware-variant-active-iocs
- DomainTools APT35 Leak Report (Jan 2026): https://dti.domaintools.com/research/threat-intelligence-report-apt35-internal-leak-of-hacking-campaigns-against-lebanon-kuwait-turkey-saudi-arabia-korea-and-domestic-iranian-targets
- CloudSEK TRIAD APT35 Analysis (Oct 2025): https://www.cloudsek.com/blog/an-insider-look-at-the-irgc-linked-apt35-operations
- IOC.one Charming Kitten Part 7 (Dec 2024): https://ioc.one/auth/website/bfa9bf19-0cdd-47c0-8846-71016ba8cdba

---

*Last updated: 2026-04-08*
