# Detect the Breach

## Purpose

Identify signs of compromise across database, Windows authentication and Microsoft Sentinel telemetry.

## Evidence

- [MySQL connection activity and IP enrichment](<P6-01 - MySQL Connection Activity and IP Enrichment.png>) — Shows observed database authentication activity reviewed alongside source-IP context.
- [Failed and successful MySQL root logons](<P6-02 - MySQL Failed and Successful Root Logons.png>) — Records repeated failed attempts and successful privileged database logons.
- [MySQL privilege and log-manipulation activity](<P6-04 - MySQL Privilege and Log Manipulation Activity.png>) — Shows privilege- and configuration-related SQL statements recorded in the audit trail.
- [Successful administrator RDP logons](<P6-05 - Successful Administrator RDP Logons.png>) — Records successful remote-interactive Administrator logons.
- [RDP brute-force attempts from multiple IPs](<P6-06 - RDP Brute Force Attempts from Multiple IPs.png>) — Shows repeated failed network logons from several source IPs, consistent with brute-force activity.
- [Sentinel incident with multiple alerts](<P6-07 - Sentinel Incident with Multiple Alerts.png>) — Shows a Microsoft Sentinel incident containing multiple correlated alerts.

Together, the screenshots establish the authentication, database and alert evidence used to recognise the breach.
