@'
# EHSL Documentation

This directory contains the technical documentation for the Enterprise Hybrid Security Lab (EHSL).

The documentation is organized by implementation phase and is intended to reflect the actual deployed state of the lab.

Planned technologies and future improvements are identified explicitly as roadmap items rather than documented as implemented capabilities.

---

## Documentation Principles

EHSL documentation follows several principles:

- document what is actually implemented;
- distinguish current state from future architecture;
- record important architectural decisions;
- document security controls together with their validation;
- preserve troubleshooting findings that provide technical value;
- avoid exposing passwords, recovery keys, secrets, or other sensitive material;
- keep historical implementation notes in the project journal rather than presenting them as current architecture.

---

## Current Environment

The current EHSL environment consists of:

| System | Role |
|---|---|
| `EHSL-DC01` | Active Directory Domain Services, DNS, DHCP, Group Policy, Windows Event Collector |
| `EHSL-FS01` | Domain Member Server and File Server |
| `EHSL-CLIENT01` | Managed Windows Domain Workstation |

Core environment:

- Active Directory domain: `ehsl.internal`
- Internal network: `10.10.10.0/24`
- Virtualization: Oracle VirtualBox
- Internal connectivity: VirtualBox Host-Only network
- Internet connectivity: VirtualBox NAT

---

# Documentation by Phase

## Phase 0 - Architecture and Design

**Status: Completed**

Phase 0 defines the current architecture and infrastructure model used by the rest of the project.

Documents:

### [Architecture and Design](phase-0/phase-0-architecture.md)

Defines:

- project vision;
- business scenario;
- design principles;
- current three-VM architecture;
- identity architecture;
- infrastructure roles;
- security architecture;
- architectural decisions;
- architecture evolution;
- future direction.

### [Network Design](phase-0/network-design.md)

Defines:

- `10.10.10.0/24` internal network;
- Host-Only and NAT network model;
- internal addressing;
- DNS architecture;
- DHCP architecture;
- current network security model;
- design constraints;
- future segmentation roadmap.

### [Infrastructure Inventory](phase-0/server-inventory.md)

Provides the current inventory for:

- EHSL-DC01;
- EHSL-FS01;
- EHSL-CLIENT01;
- compute resources;
- storage;
- addressing;
- server roles;
- Active Directory placement;
- deployed file shares;
- endpoint security state.

---

## Phase 1 - Virtual Machine Deployment

**Status: Completed**

### [Virtual Machine Specifications](phase-1/vm-specifications.md)

Documents:

- deployed virtual machines;
- guest operating systems;
- compute allocation;
- storage layout;
- network interfaces;
- VM roles;
- resource allocation strategy;
- snapshot strategy.

Current VM set:

- `EHSL-DC01`
- `EHSL-FS01`
- `EHSL-CLIENT01`

No Linux VM is currently part of the implemented architecture.

---

## Phase 2 - Active Directory

**Status: Completed**

### [Active Directory Design](phase-2/active-directory-design.md)

Documents the Active Directory structure for:

`ehsl.internal`

including:

- Organizational Units;
- users;
- administrative accounts;
- groups;
- workstations;
- servers;
- departmental separation;
- administrative structure.

The AD design provides the identity and policy foundation for subsequent EHSL phases.

---

## Phase 3 - Group Policy Baseline

**Status: Completed**

### [Workstation GPO Baseline](phase-3/workstation-gpo-baseline.md)

Documents the initial Group Policy security baseline and its validation.

The EHSL Group Policy architecture has subsequently expanded beyond the original workstation baseline and currently includes dedicated policies for:

- workstations;
- member servers;
- domain controllers;
- Windows Event Forwarding;
- Windows LAPS;
- BitLocker.

The Phase 3 document will continue to represent the baseline implementation while later security controls are documented in their corresponding phases.

---

## Phase 4 - DHCP

**Status: Completed**

### [DHCP Deployment](phase-4/dhcp-deployment.md)

Documents the Windows DHCP deployment on EHSL-DC01.

Current implementation includes:

- active `10.10.10.0/24` scope;
- dynamic range `10.10.10.100 - 10.10.10.200`;
- `ehsl.internal` DNS domain option;
- `10.10.10.10` DNS server option;
- eight-day lease;
- DHCP reservation for EHSL-FS01;
- dynamic addressing for EHSL-CLIENT01.

---

## Phase 5 - File Services and Access Control

**Status: Completed**

### [File Services and Access Control](phase-5/file-services-and-access-control.md)

Documents the enterprise file-service implementation on EHSL-FS01.

Implemented business shares:

- HR
- Finance
- Engineering
- Shared

The phase covers:

- dedicated data storage;
- SMB shares;
- Active Directory security groups;
- AGDLP;
- NTFS permissions;
- share permissions;
- departmental access control;
- file-access auditing;
- positive and negative access validation.

File access auditing generated on EHSL-FS01 is subsequently integrated into the centralized monitoring architecture documented in Phase 6.

---

## Phase 6 - Security Monitoring

**Status: Completed**

### [Security Monitoring](phase-6/security-monitoring.md)

Documents the Windows-native security monitoring architecture.

The implementation includes:

- Advanced Audit Policy;
- process creation auditing;
- command-line process auditing;
- Windows Event Collector on EHSL-DC01;
- source-initiated Windows Event Forwarding;
- EHSL-FS01 as a WEF source;
- EHSL-CLIENT01 as a WEF source;
- centralized Security event collection.

The production subscription is:

`EHSL - Security Events`

The centralized event pipeline includes selected events covering:

- authentication;
- account management;
- privilege use;
- process creation;
- Kerberos;
- audit policy;
- file access.

Troubleshooting findings and final WEF validation are maintained in the Phase 6 document.

---

## Phase 7 - Credential and Data Protection

**Status: Completed**

### [Credential and Data Protection](phase-7/credential-and-data-protection.md)

Documents the credential and data-protection controls implemented in EHSL.

The phase covers two complementary security technologies:

- Windows LAPS;
- BitLocker.

Windows LAPS is implemented for:

- EHSL-CLIENT01;
- EHSL-FS01.

The LAPS implementation includes:

- Active Directory password backup;
- encrypted password storage;
- unique local Administrator passwords;
- 20-character password policy;
- 30-day password age;
- computer self-permissions;
- delegated retrieval through `GG_IT_Admins`;
- positive and negative access validation.

BitLocker is currently implemented on:

`EHSL-CLIENT01`

The BitLocker implementation includes:

- TPM-backed operating-system protection;
- full C: volume encryption;
- XTS-AES 128;
- Recovery Password protection;
- Active Directory recovery escrow;
- recovery-password rotation validation.

BitLocker is not currently implemented on EHSL-FS01.

---

# Cross-Phase Standards

## [Naming Convention](standards/naming-convention.md)

Defines the naming model used throughout EHSL.

Current conventions include:

- `EHSL-DC##` for domain controllers;
- `EHSL-FS##` for file servers;
- `EHSL-CLIENT##` for workstations;
- `firstname.lastname` for standard users;
- `adm.*` for privileged administrative identities;
- `svc.*` as the service-account naming standard;
- `GG_*` for Global Groups;
- `DL_*` for Domain Local Groups;
- `EHSL - ...` for custom Group Policy Objects.

---

# Current Security Controls

The lab currently has validated implementations of:

- Active Directory centralized identity;
- DNS;
- DHCP;
- Group Policy;
- workstation security baseline;
- member server security baseline;
- domain controller security baseline;
- Advanced Audit Policy;
- process creation auditing;
- centralized file services;
- AGDLP access control;
- NTFS and SMB permissions;
- file-access auditing;
- Windows Event Forwarding;
- Windows Event Collection;
- Windows LAPS;
- BitLocker on EHSL-CLIENT01;
- AD DS BitLocker recovery escrow.

Windows LAPS and BitLocker are documented in Phase 7 as completed security controls.

---

# Current Documentation Map

```text
docs/
|
+-- README.md
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

This map reflects the repository structure at the current documentation stage.

Additional phase documentation should be added only when the corresponding implementation scope is defined.

---

# Implementation Status

| Phase | Area | Status |
|---|---|---|
| Phase 0 | Architecture and Design | Completed |
| Phase 1 | Virtual Machines | Completed |
| Phase 2 | Active Directory | Completed |
| Phase 3 | Group Policy Baseline | Completed |
| Phase 4 | DHCP | Completed |
| Phase 5 | File Services and Access Control | Completed |
| Phase 6 | Security Monitoring / WEF | Completed |
| Phase 7 | Credential and Data Protection | Completed |

Next technical hardening phases:

- Phase 8 - Windows Firewall Hardening
- Phase 9 - Microsoft Defender Hardening

---

# Repository Navigation

For the high-level project overview, see:

[Project README](../README.md)

For implementation history and engineering notes, see:

[Project Journal](../journal/README.md)

For automation and scripting documentation, see:

[Scripts](../scripts/README.md)

For architectural diagrams, see:

[Diagrams](../diagrams/README.md)

---

# Documentation Maintenance

Documentation should be updated when:

- architecture changes;
- new infrastructure is deployed;
- security controls are implemented;
- a validation materially changes the known state;
- a troubleshooting finding changes the final configuration;
- a phase reaches completion;
- obsolete planned components are removed from the architecture.

Git history and the project journal should preserve historical implementation context.

Current architecture documents should remain focused on the state that actually exists.
'@ | Set-Content -Path ".\docs\README.md" -Encoding UTF8

