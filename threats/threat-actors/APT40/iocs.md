# APT40 — Indicators of Compromise

> Source: FBI/CISA Advisory AA21-200A (July 2021) | ASD ACSC Advisory (July 2024)
> Admiralty Grade: A1 | TLP: TLP:CLEAR
> **Note:** Many IOCs date to 2009–2018 operations. Treat historical domains/IPs as historical context; use for threat hunting baselines. APT40 regularly rotates infrastructure.

---

## Malicious Domains (Historical C2 / Typosquatting)

| Domain | Notes |
|---|---|
| airbusocean[.]com | Impersonates Airbus — aerospace targeting indicator |
| teledynegroup[.]com | Impersonates Teledyne Technologies (defense) |
| teledyneinstrument[.]com | Impersonates Teledyne |
| huntingtomingalls[.]com | Impersonates Huntington Ingalls (defense shipbuilder) |
| thyssenkrupp-marinesystems[.]org | Impersonates ThyssenKrupp Marine Systems |
| thyssemkrupp[.]com | Typosquat of ThyssenKrupp |
| cargillnotice[.]com | Impersonates Cargill |
| chemscalere[.]com | C2 domain |
| ccidmeekparry[.]info | C2 domain |
| ccvzvhjhdf[.]website | C2 domain |
| cdigroups[.]com | C2 domain |
| checkecc[.]com | C2 domain |
| cnnzapmeta[.]com | C2 domain |
| corycs[.]com | C2 domain |
| deltektimes[.]com | C2 domain |
| Engaction[.]com | C2 domain |
| ens-smithjonathan.rhcloud[.]com | C2 infrastructure |
| fishgatesite.wordpress[.]com | Watering hole / staging |
| goo2k88yyh2.chickenkiller[.]com | C2 subdomain |
| gttdoskip[.]com | C2 domain |
| indiadigest[.]in | Lure / staging domain |
| jack-newnb[.]com | C2 domain |
| kAty197.chickenkiller[.]com | C2 subdomain |
| louisdreyfu[.]com | C2 domain |
| mail2.ignorelist[.]com | C2 / exfil |
| masterroot[.]pw | C2 domain |
| microsql-update[.]info | C2 domain — masquerades as Microsoft update |
| mihybb[.]com | C2 domain |
| mlcdailynews[.]com | Lure domain |
| movyaction[.]net | C2 domain |
| msusanode[.]com | C2 domain |
| newbb-news[.]com | Lure domain |
| nfmybb[.]com | C2 domain |
| nobug[.]uk.to | C2 domain |
| notesof992.wordpress[.]com | Staging |
| onlinenewspapers[.]club | Lure domain |
| onlineobl[.]com | C2 domain |
| oyukg43t[.]website | C2 domain |
| pacifichydrologic[.]org | C2 domain |
| philippinenewss[.]com | Lure domain (South China Sea targeting) |
| philstarnotice[.]com | Lure domain |
| santaclarasystem[.]us | C2 domain |
| scsnewstoday[.]com | Lure domain |
| secbkav[.]com | C2 domain |
| Soure7788.chickenkiller[.]com | C2 subdomain |
| tccoll[.]com | C2 domain |
| thestar[.]live | Lure domain |
| thrivedataview[.]com | C2 / exfil domain |
| togetno992.mooo[.]com | C2 subdomain |
| tojenner97.chickenkiller[.]com | C2 subdomain |
| trafficeco[.]com | C2 domain |
| transupdate[.]com | C2 domain |
| troubledate[.]com | C2 domain |
| ultrasocial[.]info | C2 domain |
| usdagroup[.]com | C2 domain |
| vser.mooo[.]com | C2 domain |
| wsmcoff[.]com | C2 domain |
| xbug.uk[.]to | C2 domain |
| yootypes[.]com | C2 domain |
| nmw4xhipveaca7hm[.]onion.link | Tor-based C2 relay |

---

## Malicious URLs / Staging Locations (Historical)

- `http://gkimertds.wordpress[.]com/feed/`
- `https://pastebin[.]com/p1mktQpD`
- `https://pastebin[.]com/vfb5mbbu`
- `http://stackoverflow[.]com/users/3627469/angle-swift` (compromised/fake account)
- `http://stackoverflow[.]com/users/3804206/swiftr-angle` (compromised/fake account)
- `https://github[.]com/slotz/sharp-loader/commit/f9de338fb474fd970a7375030642d04179b9245d`
- `www.yorkshire-espana-sa[.]com/english/servicios/`

---

## MD5 Malware Hashes (Historical — from CISA AA21-200A)

```
01234c0e41fc23bb5e1946f69e6c6221
018d3c34a296edd32e1b39b7276dcf7f
019b68e26df8750e2f9f580b150b7293
01fa52a4f9268948b6c508fef0377299
022bd2040ec0476d8eb80d1d9dc5cc92
039d9ca446e79f2f4310dc7dcc60ec55
043f6cdca33ce68b1ebe0fd79e4685af
04918772a2a6ccd049e42be16bcbee39
04dc4ca70f788b10f496a404c4903ac6
060067666435370e0289d4add7a07c3b
062c759d04106e46e027bbe3b93f33ef
07083008885d2d0b31b137e896c7266c
079068181a728d0d603fe72ebfc7e910
0803f8c5ee4a152f2108e64c1e7f0233
09143a14272a29c56ff32df160dfdb30
0985f757b1b51533b6c5cf9b1467f388
09aab083fb399527f8ff3065f779644
30b7bb3e23a1be2f26b9adf7004fc6b5
20b9a614a2bbc64c1f32b95988e5a335
90bbe092a2120b1be699387be16b5f8f
b0bbe769505ca3db6016da400539f77a
a0c3c00c01f4c4bad92b5ba56bd5a959
80c4fa4dfbe0b07d3425fea3efe60be1
c0ca936a564508a1f9c91cb7943e07c3
00d69eefede612493afd16a7541415b9
50da08b4bfe84eacc9a1d9642046c3b3
c0dd7f10fdf60fc36d81558e0c493098
40e01ec14c25f9732cc47cf634410767
210191b6ce29b4e2bddb9e57d99e6c47
1105757d1499f3790e69fb1a41e372fd
911166f8319c08c70fc886433a7dac92
d1223302912ec70c7c8350268a13ad22
6139e071dd83304cdcfd5280022a0f95
813c93dc9186258d6c335b16dc7bb3c8
c14e2b0e47887c3bfbddb3b66012cb6e
```

---

## Key CVEs (Priority Patch List)

| CVE | Product | CVSS | Notes |
|---|---|---|---|
| CVE-2021-44228 | Apache Log4j | 10.0 CRITICAL | Log4Shell — weaponized within hours of disclosure |
| CVE-2021-26084 | Atlassian Confluence | 9.8 CRITICAL | RCE in Server/Data Center |
| CVE-2021-31207 | Microsoft Exchange | 6.6 MEDIUM | ProxyShell chain component |
| CVE-2021-34523 | Microsoft Exchange | 9.0 CRITICAL | ProxyShell — SSRF |
| CVE-2021-34473 | Microsoft Exchange | 9.1 CRITICAL | ProxyShell — pre-auth RCE |

---

## Malware File Indicators (Known Filenames/Paths)

APT40 has placed legitimate tools in non-standard locations as part of intrusion activity:
- `mt.exe` (MurkTop — recon tool) — flagged when found outside standard Windows paths
- Web shells placed on internet-facing servers post-exploitation
- `tcpdump` binaries placed on compromised appliances for credential sniffing

---

## Hunting Notes

- **DNS:** Monitor for outbound queries to typosquatted domains mimicking aerospace/defense firms (Airbus, Huntington Ingalls, Teledyne, ThyssenKrupp)
- **Web servers:** Hunt for unexpected web shells post-exposure to known CVEs
- **SOHO devices:** Compromised routers/home devices as last-hop C2 relays — treat anomalous outbound from routers as high-priority
- **Dropbox API:** Monitor for unusual Dropbox API activity from servers (T1567.002)
- **GitHub/Pastebin:** Historical staging of payloads; monitor C2 callbacks to public code repositories

---

*Last updated: 2026-04-13 | Source: FBI/CISA AA21-200A, ASD/ACSC July 2024 Advisory | TLP:CLEAR*
