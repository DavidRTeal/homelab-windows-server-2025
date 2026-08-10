# Hybrid Active Directory Infrastructure Lab

A self-built, production-style hybrid identity and infrastructure environment — built on Proxmox, extended into Azure — designed to mirror how a real mid-size company manages on-prem and cloud infrastructure, identity, and access.

**Status:** In Progress 

## Overview

This lab simulates a single-domain Active Directory environment with departmental network segmentation, multi-DC redundancy, GPO-based policy management, and hybrid identity sync to Microsoft Entra ID. It was built to gain hands-on experience with the same systems and failure scenarios found in real enterprise environments, and to validate that knowledge against real-world design trade-offs rather than just exam objectives.

## Architecture

- **Hypervisor:** Proxmox VE
- **Routing/Firewall:** pfSense/OPNsense — VLAN routing, NAT, inter-VLAN firewall rules
- **Network segmentation:** VLAN 10 (Root/HQ), VLAN 21 (Sales), VLAN 22 (HR), VLAN 99 (Management)
- **Directory services:** Single domain (`corp.local`) with three domain controllers (DC01, DC02, DC03), one per network segment
- **Identity:** Hybrid sync to Microsoft Entra ID via Entra Connect
- **Cloud:** Azure VM joined to the on-prem domain over hybrid networking

Full network and hybrid architecture diagrams are included in `/diagrams`.

## What's Implemented

### Core Infrastructure

- Proxmox networking with VLAN-aware bridging (vmbr0/vmbr1)
- pfSense/OPNsense handling routing, NAT, and DHCP per VLAN
- Least-privilege firewall rules between VLANs (not default-allow)

### Active Directory

- Single domain, multi-DC design with AD Sites & Subnets so each department authenticates against its local DC
- OU structure (Sales, HR) with GPOs linked per OU — password policy, mapped drives, printer deployment
- File and print services, with NTFS/share permissions mapped to OU/security group structure

### Failure & Recovery Testing

Rather than just standing up services, this lab includes simulated failure scenarios with written incident reports covering diagnosis, resolution, and lessons learned:

- Primary DC failure and FSMO role seizure
- GPO misconfiguration/conflict troubleshooting
- DNS failure diagnosis and resolution

See `/incident-reports` for each writeup.

### Identity Hardening

- Privileged account separation (no daily-driver domain admin use)
- Hardened inter-VLAN firewall rules limiting client access to only required AD ports
- Logon/lockout auditing enabled on all DCs
- Threat-modeling writeup: what a compromised workstation VLAN could access, and how the design limits lateral movement

### Hybrid Cloud Extension

- Entra Connect syncing on-prem AD to Microsoft Entra ID
- Azure VM deployed and domain-joined via hybrid networking
- Azure VNet/subnet/NSG configuration connecting back to the on-prem environment

### Automation

PowerShell scripts used throughout the environment, included in `/scripts`:

- Bulk user and OU creation from CSV input
- Stale account / security audit reporting
- Automated Azure resource provisioning

### Operations & Resilience

- Windows Server Backup (or Veeam Community Edition) performing scheduled DC system-state backups
- Backup restore test performed and documented as an incident report
- WSUS deployed, demonstrating update approval and deployment workflow
- NTP hierarchy configured — PDC emulator set as authoritative time source, documented with rationale (Kerberos clock-skew sensitivity)
- Lightweight monitoring/alerting (Zabbix, PRTG, or similar) watching DC health, disk, and core services

### Extended AD Services

- Active Directory Certificate Services (AD CS) deployed, issuing internal certificates
- Onboarding/offboarding automation: end-to-end PowerShell workflow (create account → assign OU → add to groups → license in Microsoft 365 → disable/move to "Disabled Users" OU on termination)

## Design Rationale

Key decisions documented in `/docs`, including:

- Why a single domain with OUs was used instead of a multi-domain forest
- Why AD Sites were configured instead of relying on default DC discovery
- How the hybrid identity sync method was chosen
- What would change to scale this design to a ~500-person organization

Each phase also includes a short **Lessons Learned** note — what worked, what didn't, and what would be done differently next time.

## Demo

Architecture diagrams are embedded below (also available at full resolution in `/diagrams`).

<!-- Embed diagram images here, e.g.: --> <!-- ![Network Topology](diagrams/network-topology.png) --> <!-- ![Hybrid Identity Architecture](diagrams/hybrid-architecture.png) -->

A short walkthrough demo (AD structure, GPO application) is available here: `[link to GIF/recording]`

## Repo Structure

```
/diagrams          Network and hybrid architecture diagrams
/incident-reports   Failure/recovery scenario writeups
/scripts            PowerShell automation
/docs               Design decisions and rationale, lessons learned per phase
```

## Build Progress

Tracking progress against each phase of the project. Updated as work is completed.

### 1. Core Infrastructure 

- [x] Proxmox host installed and configured
- [x] vmbr0 (WAN/home LAN bridge) configured
- [x] vmbr1 (internal, VLAN-aware trunk) configured
- [x] VLANs created: 10 (Root/HQ), 21 (Sales), 22 (HR), 99 (Management)
- [x] OPNsense deployed, routing, NAT, DHCP per VLAN
- [x] Inter-VLAN firewall rules (least-privilege)


### 2. Active Directory - Single Domain, Multi-DC

- [ ] DC01 deployed, domain created (`corp.local`)
- [ ] DC02 (Sales) and DC03 (HR) deployed as additional DCs
- [ ] AD replication verified across all DCs
- [ ] DNS configured and verified (AD-integrated zones)
- [ ] OU structure created (Sales, HR)
- [ ] Workstations joined, placed in correct OUs
- [ ] AD Sites & Subnets configured
- [ ] GPOs created, linked, and verified per OU

### 3. Failure & Recovery Scenarios

- [ ] DC01 failure simulated, authentication continuity confirmed
- [ ] FSMO roles seized and documented
- [ ] GPO conflict diagnosed and resolved
- [ ] DNS failure diagnosed and resolved
- [ ] Incident reports written for each scenario

### 4. Identity Hardening

- [ ] Hardened password/lockout policy
- [ ] Privileged accounts separated from standard accounts
- [ ] Firewall rules hardened (Clients → DCs limited to required ports)
- [ ] Logon/lockout auditing enabled
- [ ] Threat-model writeup completed

### 5. Hybrid Cloud Extension

- [ ] Azure free-tier subscription created
- [ ] Entra Connect installed, sync verified
- [ ] Azure VM deployed and domain-joined
- [ ] Azure networking (VNet/subnet/NSG) connected to on-prem

### 6. File & Print Services

- [ ] File server deployed, shares mapped to OU/security groups
- [ ] Print server deployed, printer assigned via GPO

### 7. Automation

- [ ] PowerShell: bulk user/OU creation script
- [ ] PowerShell: stale account/security audit script
- [ ] Script: Azure resource provisioning
- [ ] PowerShell: end-to-end onboarding/offboarding workflow

### 8. Operations & Resilience

- [ ] Windows Server Backup / Veeam CE configured for DC system-state backups
- [ ] Backup restore test performed and documented
- [ ] WSUS deployed, update approval/deployment workflow demonstrated
- [ ] NTP hierarchy configured (PDC emulator as authoritative source)
- [ ] Monitoring/alerting deployed (Zabbix/PRTG/similar) watching DC health

### 9. Extended AD Services

- [ ] AD Certificate Services (AD CS) deployed, issuing internal certs
- [ ]  Lessons Learned notes written per phase

### 10. Documentation & Packaging

- [ ] Network and hybrid architecture diagrams added 
- [ ] Design rationale docs written
- [ ] Incident reports published (including backup/restore test)
- [ ] Demo GIF/screen recording added
- [ ] Repo structure finalized and cleaned

---

## Purpose

This project was built to develop and demonstrate practical, hands-on experience with hybrid identity and infrastructure administration — Active Directory, Group Policy, network segmentation, and Azure integration — as a complement to Microsoft certifications (MD-102, AZ-900, AZ-802, AZ-104, Security+, Network+).