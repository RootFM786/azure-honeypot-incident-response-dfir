# Analyse the Breach

## Purpose

Triage alerts, enrich indicators and examine endpoint, database and network telemetry to understand the observed compromise.

## Evidence

- [Sentinel alert triage](<P7-01 - Sentinel Alert Triage - Successful Administrator Logon.png>) — Shows triage of an alert for a successful privileged Windows logon.
- [Administrator logon source IP confirmed](<P7-02 - Source IP Confirmed for Successful Administrator Logon.png>) — Shows KQL results connecting successful Administrator logons to observed source IPs.
- [Source-IP threat intelligence](<P7-04 - Source IP Threat Intelligence - AbuseIPDB.png>) — Provides external reputation context for one observed source IP; it is supporting context rather than attribution.
- [Destructive MySQL commands and ransom-note-style message](<P7-08 - MySQL Commands - Database Drops and Ransom Note.png>) — Shows database-drop commands and creation of a ransom-note-style database or message in MySQL audit telemetry.
- [Masscan and WinPcap process activity](<P7-15 - Process Results - Masscan and WinPcap Activity.png>) — Shows host process telemetry containing Masscan- and WinPcap-related activity; it does not by itself prove successful scanning or lateral movement.
- [Device file-event results](<P7-17 - Device File Events - Results.png>) — Shows file-event hunting across the post-compromise window without treating every returned event as malicious.
- [Device registry-event results](<P7-19 - Device Registry Events - Results.png>) — Shows registry telemetry reviewed during the incident window.
- [Network events across the incident window](<P7-22 - DeviceNetworkEvents Full Incident Window Results.png>) — Shows network telemetry used for connection analysis; it does not establish successful exfiltration or lateral movement.

These examples document the investigation while keeping conclusions within what the captured evidence supports.
