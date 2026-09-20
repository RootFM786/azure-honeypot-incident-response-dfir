# Detect the Breach

## Purpose

Identify signs of compromise across database and Windows authentication telemetry.

## Evidence

- [MySQL connection activity and IP enrichment](<P6-01 - MySQL Connection Activity and IP Enrichment.png>) — Shows database connections investigated alongside source-IP context.
- [Failed and successful MySQL root logons](<P6-02 - MySQL Failed and Successful Root Logons.png>) — Records repeated failures followed by successful privileged database access.
- [MySQL privilege and log-manipulation activity](<P6-04 - MySQL Privilege and Log Manipulation Activity.png>) — Shows suspicious database actions relevant to the breach.
- [Successful administrator RDP logons](<P6-05 - Successful Administrator RDP Logons.png>) — Records successful privileged remote-desktop access.
- [RDP brute-force attempts from multiple IPs](<P6-06 - RDP Brute Force Attempts from Multiple IPs.png>) — Shows repeated remote logon attempts from several sources.

Together, the screenshots establish the observable authentication and database activity used to recognise the breach.
