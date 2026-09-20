# Eradication and Recovery

## Purpose

Remove the unsafe exposure, replace the compromised system and restore essential security controls and services.

## Evidence

- [Permissive allow-all NSG rule removed](<P9-01 - Permissive Allow-All NSG Rule Removed.png>) — Shows removal of the deliberately permissive inbound network rule used for the honeypot exposure.
- [Replacement VM creation configured](<P9-11 - Replacement VM Creation Configured.png>) — Records configuration of the clean replacement host; it is evidence of rebuild preparation rather than completed deployment by itself.
- [Replacement VM onboarded to Defender](<P9-12 - Replacement VM Onboarded to Defender.png>) — Shows successful Defender for Endpoint onboarding on the replacement host.
- [Compromised VM deletion initiated](<P9-13 - Compromised VM Deletion Initiated.png>) — Records initiation of the compromised VM’s deletion rather than claiming backend deletion had fully completed.
- [MySQL reinstalled with a strong root password](<P9-14 - MySQL Reinstalled with Strong Root Password.png>) — Shows MySQL reinstallation with the replacement root password masked.
- [Corporate database repopulated and verified](<P9-15 - Corporate Database Repopulated and Verified.png>) — Shows the corporate database restored or repopulated and queried in the replacement environment.
- [Replacement VM NSG hardened](<P9-16 - Replacement VM NSG Hardened.png>) — Shows network rules returned to a restricted posture.
- [Windows Firewall enabled](<P9-17 - Windows Firewall Enabled.png>) — Confirms that the Windows Firewall profiles were re-enabled.
- [MySQL remote root access removed](<P9-18 - MySQL Remote Root Access Removed.png>) — Shows remote MySQL root access removed from the rebuilt service.
- [Guest account disabled](<P9-19 - Guest Account Disabled.png>) — Confirms the Guest account was disabled on the replacement system.
- [Full Defender malware scan completed](<P9-20 - Full Defender Malware Scan Completed.png>) — Shows a completed full scan with zero current threats detected; this is a point-in-time validation result.

The sequence documents removal of the exposure, clean rebuilding, service restoration and layered hardening.
