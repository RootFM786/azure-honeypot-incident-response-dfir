# Azure Honeypot Incident Response & DFIR Investigation

I built and configured a Windows/MySQL honeypot in Microsoft Azure, connected database and endpoint telemetry to Microsoft Sentinel and Defender for Endpoint, and deliberately exposed the lab to internet traffic. I then investigated a real compromise using KQL, compared forensic investigation packages, contained the endpoint, and rebuilt and hardened the environment.

The investigation confirmed privileged MySQL and Windows access, destructive database-extortion activity, post-compromise tooling and attempted RDP lateral movement. The compromised VM was isolated and destroyed; a clean replacement was deployed and hardened.

**Investigation period:** 14–19 September 2026 · **Environment:** controlled Azure lab with synthetic business data  
[Full incident report](<evidence/10-report/Cyber Defence Incident Report.docx>) · [Supporting evidence](evidence/)

## Skills and technologies

| Area | Work demonstrated |
| --- | --- |
| Microsoft Azure | Windows VM deployment, virtual networking and NSG configuration |
| Microsoft Sentinel, Log Analytics and KQL | Custom log ingestion, analytics rules, alert triage and cross-source investigation |
| Microsoft Defender for Endpoint | VM onboarding, endpoint hunting, isolation and investigation-package collection |
| MySQL | Service configuration, authentication and query-log analysis, database recovery and access hardening |
| Incident Response, Threat Hunting and DFIR | Incident scoping, timeline reconstruction, forensic comparison and recovery assessment |
| Network and endpoint analysis | Logons, processes, files, registry changes and process-associated connections |

## Environment and architecture

The honeypot used a Windows VM running MySQL with a synthetic corporate database. RDP and MySQL provided the exposed services. I onboarded the VM to Defender for Endpoint and configured MySQL general-query logs for collection through Azure Monitor Agent and a data collection rule into Log Analytics.

After establishing monitoring and collecting a baseline investigation package, I deliberately weakened privileged authentication, disabled Windows Firewall and added an allow-all inbound NSG rule. These changes created the exposure conditions for the honeypot.

![Azure honeypot architecture](<evidence/00-honeypot-architecture/Honeypot Architecture.png>)

*Planned host, network and telemetry layout. MySQL logs used Azure Monitor Agent; endpoint telemetry came from Defender onboarding.*

## Logging and detection engineering

I verified the flow from **MySQL audit/query logs → Log Analytics → Sentinel**, with records available in `MySQLAudit_CL`. Defender supplied endpoint logon, process, file, registry and network telemetry for investigation.

![MySQL records available for hunting](<evidence/03-wire-logging-to-log-analytics/P3-04 - MySQL Log Ingestion Verified.png>)

*MySQL records returned from the custom log table.*

I created and enabled custom Sentinel analytics rules for Windows and MySQL authentication activity. The rules provided alert context; I checked the underlying records to establish which events represented successful privileged access.

![Custom Sentinel rules enabled](<evidence/04-write-detections/P4-04 - MySQL Detection Rule Enabled.png>)

*Both custom authentication rules are enabled.*

## Incident summary

External activity progressed from MySQL probing to successful root access, database enumeration and `SELECT` queries against `credentials`, `customers`, `orders` and `payments`. The audit trail then recorded `DROP DATABASE` statements and creation of `RECOVER_YOUR_DATA` with an extortion message.

Windows telemetry separately recorded successful Administrator RemoteInteractive/RDP logons, Masscan and WinPcap execution, and an executable-handler registry change involving `C:\Windows\svchost.com`. Network analysis identified eight failed outbound RDP attempts to private hosts and successful outbound connections from suspicious processes.

I assessed the compromise as **high confidence and high severity** based on privileged access and destructive activity. The original Sentinel alert severity was Medium. The evidence does not establish that one actor performed all observed activity.

![Sentinel incident containing multiple alerts](<evidence/06-detect-the-breach/P6-07 - Sentinel Incident with Multiple Alerts.png>)

*Sentinel incident view grouping 14 alerts for the monitored host.*

## Investigation and threat hunting

### Privileged access and database activity

I used `DeviceLogonEvents` to distinguish RemoteInteractive sessions from other logon types and associate successful Administrator events with source IPs. Connection events on TCP/3389 or TCP/3306 alone were not counted as successful authentication.

![Successful Administrator logon results](<evidence/07-analyse-the-breach/P7-02 - Source IP Confirmed for Successful Administrator Logon.png>)

*KQL results show successful Administrator logons, including RemoteInteractive sessions.*

For MySQL, I checked the underlying `RawData` rather than relying solely on helper-query labels. The report identifies four blank Connect records among 49 rows labelled successful; 45 contained explicit successful root connections.

![Destructive database statements and extortion note](<evidence/07-analyse-the-breach/P7-08 - MySQL Commands - Database Drops and Ransom Note.png>)

*Audit records show DROP DATABASE statements and creation of a ransom-note-style message.*

This is **destructive database-extortion activity**. The general log records statements issued, not returned data or the success of every operation. The `SELECT` activity establishes database read/query activity; it does not prove exfiltration. File-encryption ransomware was not established.

### Endpoint and network activity

I correlated process, file and registry events around privileged sessions. The findings included:

- Masscan execution targeting TCP/3389, WinPcap execution and NPF start commands.
- Modification of `HKLM\SOFTWARE\Classes\exefile\shell\open\command` to route executable launches through `C:\Windows\svchost.com`.
- Eight outbound RDP attempts by `mstsc.exe` under Administrator to private hosts. All eight returned `ConnectionFailed`, supporting **attempted lateral movement**.
- Successful outbound connections associated with `._cache_Massscan_GUI.exe` and `Synaptics.exe`. Their transferred content and purpose were unresolved; confirmed C2 and exfiltration were not established.

![Masscan and WinPcap process results](<evidence/07-analyse-the-breach/P7-15 - Process Results - Masscan and WinPcap Activity.png>)

*Process telemetry records Masscan command lines, WinPcap and NPF activity.*

The network export included both the original host at `10.1.0.10` and replacement-host traffic at `10.1.0.105`. I scoped compromise findings to the original host. The [network-results screenshot](<evidence/07-analyse-the-breach/P7-22 - DeviceNetworkEvents Full Incident Window Results.png>) shows replacement-host rows and documents investigation scope, not malicious activity in those rows. The report records the original-host findings from the CSV review.

Source-IP reputation and geolocation provided infrastructure context, not attacker identity.

### Forensic comparison

I compared the pre-exposure and post-breach Defender investigation packages, collected on **14 September at 22:09:25 UTC** and **18 September at 21:28:13 UTC** respectively.

New Prefetch and execution artifacts for Masscan, WinPcap, `svchost.com` and `Synaptics.exe` corroborated the endpoint timeline. Autoruns and service evidence added NPF/`npf.sys` and WinPcap's `rpcapd`. These components were suspicious in context; their presence alone did not prove packet capture or malicious persistence.

None of the named suspicious processes was running at the later collection point, but historical execution artifacts remained. Suspicious binaries were unavailable for reliable hashing or static analysis, and incomplete event-log and firewall artifacts limited the comparison.

## Incident timeline

All dates are in **September 2026**. MySQL entries use embedded UTC timestamps. Endpoint and alert times retain their recorded values because the report could not establish the endpoint export timezone; they should not be treated as a fully normalised cross-source timeline.

| Recorded time | Confirmed event | Source |
| --- | --- | --- |
| 14 Sep 22:14:02 UTC | Recorded honeypot exposure point | Investigation marker |
| 15 Sep 02:34:29 UTC | First explicit successful MySQL root login in the export | MySQL authentication |
| 15 Sep 02:34:56–02:35:05 UTC | Table queries, database-drop statements, extortion-note creation and shutdown/log-manipulation statements | MySQL query log |
| 15 Sep 12:29:06 | Successful Administrator RemoteInteractive logon | DeviceLogonEvents |
| 15 Sep 12:32:30–12:32:51 | Eight failed outbound RDP attempts to private hosts | DeviceNetworkEvents |
| 16 Sep 22:26:18 / 22:38:10 | Administrator logon followed by the reviewed Sentinel alert; earlier incident activity already existed | Logon and alert evidence |
| 17 Sep 06:41:58–06:42:52 | Masscan GUI, Synaptics and WinPcap execution; executable-handler change at 06:42:33; Masscan scan command | Endpoint telemetry |
| 17 Sep 06:42:07–06:43:06 | Successful outbound connections from Masscan-related tooling and Synaptics.exe | DeviceNetworkEvents |
| 18 Sep | Defender isolation and post-breach package collection | Response evidence |
| 18–19 Sep | Compromised VM destroyed; replacement deployed, hardened and repopulated | Recovery evidence |

## Containment, eradication and recovery

I isolated the compromised endpoint in Defender and collected a post-breach investigation package. The report includes the subsequent isolated state and package availability.

![Defender isolation action](<evidence/08-contain-the-breach/P8-01 - Defender Device Isolation Initiated.png>)

*Defender isolation confirmation dialog for the affected host.*

![Post-breach package collection request](<evidence/08-contain-the-breach/P8-03 - Post-Breach Investigation Package Collection.png>)

*Post-breach investigation-package request; collection is pending in this capture.*

The executable-handler modification left the host untrustworthy. I removed the permissive NSG rule, destroyed the compromised VM and deployed a clean replacement. Recovery included Defender onboarding, MySQL reinstallation with stronger credentials and root restricted to localhost, NSG hardening, Windows Firewall enabled across all profiles, and the Guest account disabled. I repopulated the corporate lab database from the original clean source dataset.

![Replacement VM network rules](<evidence/09-eradication-and-recovery/P9-16 - Replacement VM NSG Hardened.png>)

*Replacement NIC-level NSG shows default rules including DenyAllInBound; subnet rules are collapsed.*

![Completed Defender full scan](<evidence/09-eradication-and-recovery/P9-20 - Full Defender Malware Scan Completed.png>)

*Recovery capture shows a completed full scan with 0 threats detected across 802,385 files.*

The scan is a point-in-time result. Its capture shows neither a hostname nor a timezone, and it does not establish the absence of every possible threat.

## Key findings and impact assessment

| Area | Assessment |
| --- | --- |
| Initial access | Publicly exposed privileged services and deliberately weak authentication enabled compromise. No specific Windows or MySQL software exploit was established. |
| Confidentiality | Privileged access and database read/query activity were observed. Returned data and exfiltration were not established. |
| Integrity | Destructive SQL statements, a ransom-note-style database/message and the executable-handler modification were recorded. |
| Availability | Database-drop and SHUTDOWN statements were issued. Exact downtime and the success of every statement were not established. |
| Scope | Compromise of the honeypot was confirmed. All eight observed outbound RDP attempts failed; successful lateral compromise was not established. |
| Business impact | The database contained synthetic lab data. Production business loss was not established. |

## MITRE ATT&CK mapping

These mappings follow the behaviour assessments in the incident report.

| Technique | Evidence and assessment |
| --- | --- |
| [T1046 — Network Service Discovery](https://attack.mitre.org/techniques/T1046/) | Masscan execution and command lines support attempted TCP/3389 scanning. Successful Masscan connections to that port were not directly established. |
| [T1036 — Masquerading](https://attack.mitre.org/techniques/T1036/) | The misleading `svchost.com` name, execution artifacts and use in executable handling are consistent with masquerading. |
| [T1021.001 — Remote Desktop Protocol](https://attack.mitre.org/techniques/T1021/001/) | Eight Administrator-context RDP connection attempts to private hosts support attempted lateral movement; all recorded attempts failed. |

WinPcap installation alone does not establish packet capture. The exact role of Synaptics.exe remained unresolved.

## Evidence and full report

- [Evidence folders](evidence/) — 41 images with descriptions covering build, monitoring, exposure, investigation and recovery.
- [Cyber Defence Incident Report](<evidence/10-report/Cyber Defence Incident Report.docx>) — Detailed timeline, forensic comparison, findings, limitations and investigation queries.

The report documents the underlying log and investigation-package analysis; those raw artifacts are not currently published in this repository.

## Key analyst takeaways

- **Validate query labels against source records.** Blank MySQL Connect rows showed why derived success labels need checking.
- **Track host identity and time basis.** Reused device names and mixed timestamp conventions can combine unrelated activity.
- **Separate actions from outcomes.** A scan command, successful connection or extortion claim does not establish successful compromise or data transfer.
- **Preserve a baseline early.** Comparing packages exposed historical execution and system changes that a live process list missed.
- **Treat recovery as several checks.** A clean rebuild, restricted access, restored data and a completed scan each address a different part of recovery.
