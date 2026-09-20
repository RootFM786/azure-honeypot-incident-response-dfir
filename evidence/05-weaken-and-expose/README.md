# Weaken and Expose

## Purpose

Deliberately reduce selected protections and expose the honeypot in a controlled exercise so hostile activity could be observed.

## Evidence

- [MDE investigation package collected](<P5-09 - MDE Investigation Package Collected.png>) — Records an investigation package collection during the phase.
- [Windows Defender Firewall disabled](<P5-10 - Windows Defender Firewall Disabled.png>) — Confirms the host firewall was disabled for the controlled exposure.
- [Allow-all inbound NSG rule](<P5-11 - Azure NSG Allow All Inbound Rule.png>) — Shows the permissive Azure network rule used to make the honeypot reachable.
- [Exposure timestamp recorded](<P5-12 - Exposure Timestamp Recorded.png>) — Establishes when the exposure began for later timeline analysis.

The sequence documents the exposure conditions and provides a time reference for interpreting subsequent alerts.
