# Enterprise Hybrid Security Lab

A hands-on enterprise infrastructure and security lab built to design, deploy, secure, and monitor a realistic Microsoft-based environment.

EHSL focuses on practical implementation rather than isolated product demonstrations.

The project currently covers:

- Windows Server infrastructure
- Active Directory Domain Services
- DNS and DHCP
- Group Policy
- enterprise file services
- AGDLP access control
- Advanced Audit Policy
- Windows Event Forwarding
- Windows Event Collection
- Windows LAPS
- BitLocker
- Active Directory-integrated credential and recovery management

The lab is developed incrementally, with each phase implemented, validated, and documented before the next security layer is introduced.

---

# Current Architecture

The current environment contains three Windows virtual machines.

| System | Operating System | Primary Role | Internal Address |
|---|---|---|---|
| `EHSL-DC01` | Windows Server 2022 | AD DS, DNS, DHCP, Group Policy, WEC | `10.10.10.10` |
| `EHSL-FS01` | Windows Server 2022 | Domain Member / File Server | `10.10.10.20` |
| `EHSL-CLIENT01` | Windows 11 Pro Education | Managed Domain Workstation | DHCP |

Active Directory domain:

`ehsl.internal`

Internal network:

`10.10.10.0/24`

Virtualization platform:

`Oracle VirtualBox`

Each VM uses:

- Host-Only networking for internal EHSL communication;
- NAT for outbound Internet connectivity.

The current architecture does not use VLANs, a DMZ, or a Linux server.

Those technologies may be introduced later only when they support a defined project objective.

---

# Architecture Overview

```text
                         Internet
                            |
                     VirtualBox NAT
                            |
          +-----------------+-----------------+
          |                 |                 |
     EHSL-DC01          EHSL-FS01       EHSL-CLIENT01
     Server 2022        Server 2022      Windows 11
          |                 |                 |
          +-----------------+-----------------+
                            |
                   Host-Only Network
                     10.10.10.0/24
```

Core infrastructure relationships:

```text
                    EHSL-DC01
             AD DS / DNS / DHCP / WEC
                       |
                ehsl.internal
                       |
           +-----------+-----------+
           |                       |
      EHSL-FS01               EHSL-CLIENT01
      File Server              Workstation
           |                       |
           +-----------+-----------+
                       |
                Group Policy
                       |
        Security configuration and
        centralized event forwarding
```

---

# Security Architecture

EHSL currently implements several complementary security layers.

## Identity

Active Directory provides:

- centralized authentication;
- structured Organizational Units;
- standard and privileged identities;
- security groups;
- administrative separation;
- Group Policy targeting.

## Authorization

File services use:

`Accounts -> Global Groups -> Domain Local Groups -> Permissions`

through the AGDLP model.

This separates user or business-role membership from resource permissions.

## Endpoint Configuration

Group Policy provides centralized security configuration for:

- workstations;
- member servers;
- domain controllers;
- Windows Event Forwarding;
- Windows LAPS;
- BitLocker.

## Credential Protection

Windows LAPS manages local Administrator passwords for:

- EHSL-CLIENT01;
- EHSL-FS01.

The implementation includes:

- unique passwords;
- automatic password management;
- Active Directory backup;
- encrypted password storage;
- delegated retrieval;
- controlled password decryption.

## Data Protection

BitLocker protects the operating-system volume on EHSL-CLIENT01.

The current implementation includes:

- TPM 2.0;
- full C: encryption;
- XTS-AES 128;
- TPM protection;
- Recovery Password protection;
- Active Directory recovery escrow.

## Monitoring

Advanced Audit Policy generates security telemetry.

Windows Event Forwarding centralizes selected events from:

- EHSL-FS01;
- EHSL-CLIENT01.

EHSL-DC01 acts as the Windows Event Collector.

The production subscription is:

`EHSL - Security Events`

---

# Centralized Security Monitoring

The implemented monitoring pipeline is:

```text
EHSL-CLIENT01                 EHSL-FS01
      |                            |
      | Windows Security Events    |
      +-------------+--------------+
                    |
                    v
          Windows Event Forwarding
                    |
                    v
               EHSL-DC01
                    |
                    v
             ForwardedEvents
```

Selected telemetry includes:

- successful logons;
- failed logons;
- explicit credential use;
- privileged logons;
- process creation;
- account creation and modification;
- security-group membership changes;
- account lockouts;
- Kerberos activity;
- file access;
- audit-policy changes.

End-to-end forwarding has been validated from both current source systems.

A real file-access Event ID `4663` generated on EHSL-FS01 was successfully forwarded to EHSL-DC01.

---

# Implemented Group Policy Architecture

Current custom GPOs include:

| GPO | Purpose |
|---|---|
| `EHSL - Workstation Baseline` | Workstation security baseline |
| `EHSL - Member Servers Baseline` | Member-server security baseline |
| `EHSL - Domain Controllers Baseline` | Domain-controller security baseline |
| `EHSL - Windows Event Forwarding` | Centralized event forwarding |
| `EHSL - Windows LAPS - Workstations` | Workstation local Administrator password management |
| `EHSL - Windows LAPS - Member Servers` | Member-server local Administrator password management |
| `EHSL - BitLocker - Workstations` | Workstation disk-encryption policy |

Security technologies are separated into dedicated policies where appropriate rather than combined into a single monolithic GPO.

---

# Active Directory Structure

The environment uses a structured EHSL OU hierarchy.

```text
ehsl.internal
|
+-- EHSL
    |
    +-- Admin Accounts
    |
    +-- Groups
    |
    +-- Servers
    |
    +-- Service Accounts
    |
    +-- Users
    |   |
    |   +-- IT
    |   +-- Security
    |   +-- HR
    |   +-- Finance
    |   +-- Sales
    |   +-- Engineering
    |
    +-- Workstations
        |
        +-- Standard
        +-- IT
        +-- Kiosk
        +-- Developers
        +-- Testing
```

This structure supports:

- policy targeting;
- administrative separation;
- departmental organization;
- future infrastructure growth.

---

# File Services and AGDLP

EHSL-FS01 provides centralized business file services.

Current business shares:

- `HR`
- `Finance`
- `Engineering`
- `Shared`

Business data is stored under:

`E:\Shares`

Access control uses Global and Domain Local groups.

Example:

```text
john.smith
    |
    v
GG_HR_Users
    |
    v
DL_FS_HR_RW
    |
    v
NTFS permission on E:\Shares\HR
```

The implementation combines:

- SMB permissions;
- NTFS permissions;
- AGDLP;
- departmental separation;
- file-access auditing.

---

# Windows LAPS

Modern Windows LAPS is integrated with Active Directory.

Current scope:

- EHSL-CLIENT01
- EHSL-FS01

Implemented controls include:

- management of the built-in Administrator account;
- 20-character passwords;
- password complexity;
- 30-day password age;
- Active Directory backup;
- password encryption;
- computer self-permissions;
- delegated read permissions;
- authorized password decryption through `GG_IT_Admins`.

Validation included both:

- authorized retrieval;
- unauthorized retrieval attempt.

The negative test confirmed that a standard domain user could not retrieve the managed password.

---

# BitLocker

BitLocker is currently implemented on:

`EHSL-CLIENT01`

Validated state includes:

- TPM 2.0 available and operational;
- C: fully encrypted;
- encryption percentage: 100%;
- XTS-AES 128;
- protection enabled;
- TPM protector;
- Recovery Password protector;
- Active Directory recovery escrow.

Recovery-password rotation was also validated.

Actual recovery passwords are intentionally excluded from this repository.

BitLocker is not currently implemented on EHSL-FS01.

---

# DHCP

EHSL-DC01 provides DHCP for the internal network.

Current scope:

`10.10.10.0/24`

Dynamic range:

`10.10.10.100 - 10.10.10.200`

Configured options include:

- DNS server: `10.10.10.10`
- DNS domain: `ehsl.internal`
- lease duration: 8 days

EHSL-FS01 uses a DHCP reservation:

`10.10.10.20`

EHSL-CLIENT01 uses dynamic addressing.

---

# Implementation Phases

| Phase | Area | Status |
|---|---|---|
| Phase 0 | Architecture and Design | Completed |
| Phase 1 | Virtual Infrastructure | Completed |
| Phase 2 | Active Directory | Completed |
| Phase 3 | Group Policy Baselines | Completed |
| Phase 4 | DHCP | Completed |
| Phase 5 | File Services and Access Control | Completed |
| Phase 6 | Security Monitoring / WEF | Completed |
| Phase 7 | Credential and Data Protection | Completed |
| Phase 8 | Windows Firewall Hardening | Next |
| Phase 9 | Microsoft Defender Hardening | Planned |

Detailed implementation progress is maintained in:

[Implementation Phases](phases/README.md)

---

# Documentation

Technical documentation is available under:

[Documentation Index](docs/README.md)

Current documentation includes:

```text
docs/
|
+-- phase-0/
|   +-- network-design.md
|   +-- phase-0-architecture.md
|   +-- server-inventory.md
|
+-- phase-1/
|   +-- vm-specifications.md
|
+-- phase-2/
|   +-- active-directory-design.md
|
+-- phase-3/
|   +-- workstation-gpo-baseline.md
|
+-- phase-4/
|   +-- dhcp-deployment.md
|
+-- phase-5/
|   +-- file-services-and-access-control.md
|
+-- phase-6/
|   +-- security-monitoring.md
|
+-- phase-7/
|   +-- credential-and-data-protection.md
|
+-- standards/
    +-- naming-convention.md
```

---

# Engineering Validation

The project emphasizes validation rather than configuration alone.

Examples of completed validation include:

- domain join;
- DNS resolution;
- DHCP addressing;
- DHCP reservation;
- Group Policy application;
- departmental file-access testing;
- positive authorization testing;
- negative authorization testing;
- NTFS auditing;
- Event ID 4663 generation;
- Advanced Audit Policy;
- Event ID 4688 process auditing;
- source-initiated WEF;
- WEF from workstation and member server;
- centralized Event ID 4624;
- centralized Event ID 4663;
- LAPS password backup;
- LAPS authorized retrieval;
- LAPS unauthorized retrieval test;
- BitLocker encryption state;
- TPM protection;
- AD DS recovery escrow;
- BitLocker Recovery Password rotation.

---

# Troubleshooting as Engineering Evidence

EHSL documentation intentionally preserves important troubleshooting findings.

Examples include the Windows Event Forwarding deployment.

During WEF implementation:

- the sources initially reported Error 5004;
- Security-log access was investigated;
- the required service permissions for the implemented lab configuration were identified;
- the sources reached `Active / LastError = 0`;
- an additional apparent delay was traced to subscription delivery configuration;
- a temporary bandwidth-oriented subscription allowed approximately six hours of delivery latency;
- low-latency delivery was validated;
- real Security events were successfully received centrally.

The final documentation records both the solution and the scope of the finding rather than presenting troubleshooting-specific configuration as a universal Windows requirement.

---

# Current Security Coverage

The current lab provides practical experience across:

### Identity Security

- Active Directory
- administrative identities
- security groups
- LAPS
- delegated credential retrieval

### Endpoint Security

- Group Policy
- security baselines
- Advanced Audit Policy
- process auditing
- BitLocker

### Infrastructure Security

- DNS
- DHCP
- Windows Server
- centralized file services
- NTFS permissions
- SMB permissions

### Security Monitoring

- Windows Security Event Log
- Windows Event Forwarding
- Windows Event Collector
- centralized authentication telemetry
- process telemetry
- file-access telemetry

---

# Next Phase

The next implementation phase is:

## Phase 8 - Windows Firewall Hardening

The objective is to move from the current Windows firewall state to a deliberately designed and centrally managed firewall policy.

The phase will focus on:

- workstation firewall configuration;
- member-server firewall configuration;
- required Active Directory communication;
- WEF connectivity;
- SMB requirements;
- inbound exposure reduction;
- explicit rule validation;
- positive and negative connectivity testing.

Firewall hardening will be implemented and validated before it is marked complete.

---

# Future Direction

After the Windows Firewall phase, the next planned host-security area is:

## Phase 9 - Microsoft Defender Hardening

Potential later development may include:

- PowerShell automation;
- additional endpoint telemetry;
- network segmentation;
- Microsoft Entra ID;
- hybrid identity;
- Microsoft Intune;
- Microsoft Defender for Endpoint;
- Microsoft Sentinel;
- cloud security integration;
- detection engineering.

These technologies are future directions and are not represented as currently deployed EHSL capabilities.

---

# Repository Structure

```text
Enterprise-Hybrid-Security-Lab/
|
+-- configs/
+-- diagrams/
+-- docs/
+-- journal/
+-- phases/
+-- scripts/
|
+-- README.md
```

The repository separates:

- technical documentation;
- implementation phases;
- engineering journal entries;
- future configuration artifacts;
- automation;
- architectural diagrams.

---

# Security and Repository Hygiene

The public repository must not contain:

- passwords;
- LAPS credentials;
- BitLocker Recovery Passwords;
- authentication tokens;
- API keys;
- private keys;
- secrets.

Command output and screenshots must be reviewed before publication.

If a real credential or recovery secret is exposed during testing, it should be rotated rather than relying only on deletion from documentation.

---

# Project Status

EHSL currently has a functional Windows enterprise foundation with:

- centralized identity;
- centralized policy;
- centralized network configuration;
- role-based file access;
- security auditing;
- centralized Windows event collection;
- local Administrator credential management;
- workstation disk encryption.

Phases 0 through 7 are complete.

The project now moves into host-level network protection with Windows Firewall hardening.
