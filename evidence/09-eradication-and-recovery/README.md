# Eradication and Recovery

## Purpose

Remove the unsafe exposure, replace the compromised system and restore essential security controls and services.

## Evidence

- [Malicious allow-all NSG rule removed](<P9-01 - Malicious Allow-All NSG Rule Removed.png>) — Shows removal of the permissive inbound network rule.
- [Replacement VM creation configured](<P9-11 - Replacement VM Creation Configured.png>) — Records configuration of the clean replacement host.
- [Replacement VM onboarded to Defender](<P9-12 - Replacement VM Onboarded to Defender.png>) — Confirms security visibility for the replacement endpoint.
- [Compromised VM deletion initiated](<P9-13 - Compromised VM Deletion Initiated.png>) — Records retirement of the compromised system.
- [MySQL reinstalled with a strong root password](<P9-14 - MySQL Reinstalled with Strong Root Password.png>) — Shows the database service rebuilt with improved credential protection.
- [Windows Firewall enabled](<P9-17 - Windows Firewall Enabled.png>) — Confirms restoration of the host firewall.
- [Guest account disabled](<P9-19 - Guest Account Disabled.png>) — Confirms removal of an unnecessary local access path.

The sequence provides a concise record of network remediation, clean rebuilding, endpoint onboarding and security hardening.
