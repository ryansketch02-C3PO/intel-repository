# Kimsuky — Indicators of Compromise

> Last updated: 2026-04-16
> Sources: Gurucul DPRK Infrastructure Analysis (Dec 2025), AhnLab ASEC, CISA/FBI advisories, ThreatFox
> TLP: WHITE

---

## Malware Families
| Malware | Type | Notes |
|---------|------|-------|
| BabyShark | VBS backdoor | Primary tool; executes via mshta.exe |
| HappyDoor | VMP-protected backdoor | Keylogging, screenshots, audio, file theft |
| GREASE | Admin persistence tool | Adds Windows admin account, enables RDP |
| MECHANICAL | PowerShell keylogger | Logs to %userprofile%\appdata\roaming\apach.{txt,log} |
| QuasarRAT | Remote access trojan | Used as final ClickFix payload |
| RftRAT | Remote access trojan | Newer payload delivery |
| Amadey | Loader/stealer | Used with RftRAT campaigns |
| DocSwap | Android malware | QR/quishing campaigns targeting mobile |

---

## File Hashes (SHA256) — 2025 Campaigns
```
cc307cfb401d1ae616445e78b610ab72e1c7fb49b298ea003dd26ea80372089a
a5350b1735190a9a275208193836432ed99c54c12c75ba6d7d4cb9838d2e2106
a3876a2492f3c069c0c2b2f155b4c420d8722aa7781040b17ca27fdd4f2ce6a9
ff32bc1c756d560d8a9815db458f438d63b1dcb7e9930ef5b8639a55fa7762c9
85045d9898d28c9cdc4ed0ca5d76eceb457d741c5ca84bb753dde1bea980b516
24d5dd3006c63d0f46fb33cbc1f576325d4e7e03e3201ff4a3c1ffa604f1b74a
bc7bd27e94e24a301edb3d3e7fad982225ac59430fc476bda4e1459faa1c1647
36541fad68e79cdedb965b1afcdc45385646611aa72903ddbe9d4d064d7bffb9
```

---

## IP Addresses (2025 Infrastructure — Lazarus/Kimsuky shared)
```
23.27.140.49
23.27.177.183
23.254.211.230
207.254.22.248
149.28.139.62
154.216.177.215
182.136.123.102
119.6.56.194
182.136.120.52
118.123.54.71
61.139.89.11
125.67.171.158
125.65.88.195
119.6.121.143
23.254.128.114
104.168.198.145
23.254.164.50
192.236.146.20
142.11.209.109
192.236.233.162
192.236.176.164
192.236.236.100
192.236.146.22
192.236.233.165
192.119.116.231
104.168.151.116
```

---

## Domains (Known C2 / Infrastructure)
```
secondshop[.]store
brhosting[.]net
prohomepage[.]net
splitbusiness[.]com
thesisterize[.]gb[.]net
```

---

## MITRE ATT&CK Techniques
| Technique ID | Name |
|-------------|------|
| T1566.001 | Phishing: Spearphishing Attachment |
| T1566.002 | Phishing: Spearphishing Link |
| T1660 | Quishing (QR Code Phishing) |
| T1598 | Phishing for Information |
| T1589 | Gather Victim Identity Information |
| T1056.003 | Input Capture: Web Portal Capture |
| T1550.004 | Use Alternate Authentication Material: Web Session Cookie |
| T1098 | Account Manipulation |
| T1059.001 | Command and Scripting Interpreter: PowerShell |
| T1218.005 | Signed Binary Proxy Execution: Mshta |
| T1189 | Drive-by Compromise |
| T1185 | Browser Session Hijacking |
| T1547 | Boot or Logon Autostart Execution |
| T1546.001 | Event Triggered Execution: Change Default File Association |
| T1021.001 | Remote Services: Remote Desktop Protocol |
| T1505.003 | Server Software Component: Web Shell |
| T1053.005 | Scheduled Task/Job: Scheduled Task |
| T1055 | Process Injection |
| T1560 | Archive Collected Data |
| T1573.001 | Encrypted Channel: Symmetric Cryptography |
| T1114.003 | Email Collection: Email Forwarding Rule |
| T1040 | Network Sniffing |
| T1083 | File and Directory Discovery |
| T1074.001 | Data Staged: Local Data Staging |

---

## Detection Queries (Splunk/SIEM)
```spl
| index=* (dest IN ("23.27.140.49","23.27.177.183","23.254.211.230","207.254.22.248","149.28.139.62","154.216.177.215","182.136.123.102","192.236.146.20","104.168.151.116") OR src IN ("23.27.140.49","23.27.177.183","23.254.211.230","207.254.22.248","149.28.139.62","154.216.177.215","182.136.123.102","192.236.146.20","104.168.151.116"))
| table _time, src, dest, action
```

```spl
| index=* (url="*secondshop.store*" OR url="*brhosting.net*" OR url="*prohomepage.net*" OR url="*splitbusiness.com*")
| table _time, src_ip, url, user
```

---

*IOC data sourced from public threat intelligence. Defang notation ([.]) used for domains. Always verify before blocking in production.*
