# Build VM

## Purpose

Deploy the Windows honeypot in Azure, configure its initial network controls and onboard the endpoint to Microsoft Defender for Endpoint.

## Evidence

- [NSG deny-inbound configuration](<P1-02 - NSG-deny inbound.png>) — Shows the restrictive inbound network rule used during the controlled build stage.
- [VM confirmed in Microsoft Defender](<P1-03 - VM onboarded and confirmed in MDE.png>) — Confirms that the new virtual machine was visible in Defender before exposure.

Together, these screenshots demonstrate that the host and its initial security visibility were established before the live honeypot exercise.
