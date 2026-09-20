# Investigation queries

KQL used to investigate the Azure Windows/MySQL honeypot compromise, reproduced from [Appendix A of the incident report](<../evidence/10-report/Cyber Defence Incident Report.docx>).

## Query files

| File | Analyst question | Data source | Report query |
| --- | --- | --- | --- |
| [windows-logons.kql](windows-logons.kql) | Which Administrator or Guest logons occurred, and which succeeded? What session types and source IPs were recorded? | `DeviceLogonEvents` | A1: account logons; A2: successful logons |
| [process-hunting.kql](process-hunting.kql) | What executed after exposure, with which command lines and initiating processes? What ran under Administrator? | `DeviceProcessEvents` | A3: host processes; A4: Administrator processes |
| [file-events.kql](file-events.kql) | What file activity occurred after the initial logon search threshold, and which processes initiated it? | `DeviceFileEvents` | A5 |
| [registry-events.kql](registry-events.kql) | What registry keys and values changed, and what initiated the changes? | `DeviceRegistryEvents` | A6 |
| [mysql-authentication.kql](mysql-authentication.kql) | Which connection records indicate failed or successful MySQL access, and which accounts and IPs appear? | `MySQLAudit_CL` | A7 |
| [mysql-query-activity.kql](mysql-query-activity.kql) | Which SQL statements were issued after exposure? | `MySQLAudit_CL` | A8 |
| [network-hunting.kql](network-hunting.kql) | Which connections occurred during the incident window, with what outcomes, local endpoints and initiating processes? | `DeviceNetworkEvents` | A9 |

## Sources and execution

A1–A8 were checked against the original Phase 7 investigation notes. A9 was checked against the original incident-window network-query screenshot. The files retain the report's executable query text, including filters, field names, time parameters, parsing and derived labels. Added comments explain scope and interpretation; no replacement queries are included.

The report already standardises the MySQL authentication comment and omits a device-name comment from the file-events notes. These are comment-only differences; executable logic matches the notes.

Use the Sentinel/Log Analytics context containing the listed tables. These queries retain `TimeGenerated` and the custom `MySQLAudit_CL` schema used in this investigation. They have not been executed against a live workspace as part of this publication.

**Run one complete query block at a time, including its `let` statements.** The logon and process files each contain two independent queries; select only the intended block. Other files contain one query each. For A7, include both the `FailedConnections` definition and the final pipeline.

## Scope and interpretation

- **Time window:** A1–A8 have start-time filters but no end-time filter. A9 uses 14 September 2026 22:14:02Z through 18 September 2026 22:41:13Z. For a new run, constrain the workspace time range or explicitly adapt a working copy to the intended incident window.
- **Host identity:** the replacement reused the endpoint naming convention. A9 includes original-host traffic from `10.1.0.10` and replacement-host traffic from `10.1.0.105`; it does not apply a `LocalIP` filter. Scope the exported results to the original host for compromise analysis. A1–A6 also require host-identity checks; their device-name filters alone do not isolate the original machine.
- **Timestamp basis:** query literals use UTC. The report notes that embedded MySQL timestamps are one hour behind the exported `TimeGenerated` values, while endpoint exports do not encode a timezone. Do not assume the displayed exports share one time basis. A5/A6 preserve the 12:29:04 search threshold; the recorded RemoteInteractive event is at 12:29:06.
- **Authentication:** A2 includes successful logon types beyond RDP. A7 derives `LogonSuccess` by exclusion and can mislabel blank Connect records. The report identifies four such rows among 49 success-labelled records, leaving 45 explicit successful root connections. Validate the underlying `RawData`.
- **Database activity:** A8 records statements issued, not returned result sets or the success of every statement. `SELECT` activity and ransom-note claims do not establish exfiltration.
- **Endpoint activity:** the process, file and registry queries are broad reviews. Returned events require assessment; Masscan/WinPcap execution alone does not establish successful discovery or exploitation.
- **Network activity:** A9 is a broad export, not a dedicated failed-RDP or suspicious-process detector. Assess connection outcome, destination port, initiating process and account in the original-host rows. The report's eight failed RDP attempts support attempted lateral movement. Successful connections do not establish C2 or exfiltration; inbound connection counts do not establish successful authentication.

All nine report queries are included. The underlying logs and investigation packages are not published here.
