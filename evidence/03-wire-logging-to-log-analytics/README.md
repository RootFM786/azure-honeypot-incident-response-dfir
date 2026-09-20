# Wire Logging to Log Analytics

## Purpose

Route MySQL telemetry into Azure Log Analytics so database activity could be searched and used by detections.

## Evidence

- [MySQL DCR log source](<P3-01 - MySQL DCR Log Source.png>) — Shows MySQL configured as a custom log source through the Azure data-collection pipeline.
- [MySQL log ingestion verified](<P3-04 - MySQL Log Ingestion Verified.png>) — Confirms that MySQL audit records were successfully available in Log Analytics for querying.

Together, these screenshots document both the collection configuration and the resulting searchable telemetry.
