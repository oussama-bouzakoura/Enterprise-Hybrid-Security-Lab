@'
# EHSL Implementation Phases

This directory provides the implementation roadmap and current phase status for the Enterprise Hybrid Security Lab (EHSL).

Detailed technical documentation for each implemented phase is maintained under:

`/docs`

The roadmap reflects the actual development of the lab. A phase is considered completed only when its core functionality has been implemented and validated.

---

# Project Strategy

EHSL is developed incrementally.

Each phase builds on capabilities established by previous phases:

`Architecture -> Infrastructure -> Identity -> Policy -> Network Services -> Access Control -> Monitoring -> Security Hardening`

The project follows several implementation principles:

- build foundational infrastructure before advanced security tooling;
- validate each major control before considering it complete;
- document the implemented state rather than the intended state;
- preserve troubleshooting findings that provide engineering value;
- introduce additional technologies only when they serve a defined technical objective;
- avoid unnecessary infrastructure that does not improve the lab.

---

# Phase 0 - Architecture and Design

**Status: Completed**

## Objective

Define the infrastructure model that subsequent EHSL phases use.

## Implemented

- Microsoft-first enterprise architecture
- Active Directory domain design
- `ehsl.internal` namespace
- `10.10.10.0/24` internal network
- VirtualBox Host-Only internal connectivity
- VirtualBox NAT Internet connectivity
- three-system infrastructure model
- infrastructure addressing strategy
- initial security architecture
- resource-aware lab design

## Current Systems

- `EHSL-DC01`
- `EHSL-FS01`
- `EHSL-CLIENT01`

## Documentation

- `docs/phase-0/phase-0-architecture.md`
- `docs/phase-0/network-design.md`
- `docs/phase-0/server-inventory.md`

---

# Phase 1 - Virtual Machine Deployment

**Status: Completed**

## Objective

Deploy the virtual infrastructure required for the enterprise lab.

## Implemented

### EHSL-DC01

Windows Server 2022 system providing the central domain infrastructure.

### EHSL-FS01

Windows Server 2022 member server providing dedicated business file services.

### EHSL-CLIENT01

Windows 11 managed workstation representing a domain endpoint.

## Architecture

Each VM has:

- an internal Host-Only network path;
- a NAT path for outbound Internet access.

FS01 also has a dedicated data disk for business file storage.

## Documentation

- `docs/phase-1/vm-specifications.md`

---

# Phase 2 - Active Directory

**Status: Completed**

## Objective

Build the centralized identity and administrative foundation of EHSL.

## Implemented

- Active Directory Domain Services
- domain `ehsl.internal`
- DNS-integrated domain infrastructure
- structured Organizational Unit hierarchy
- departmental user separation
- workstation OUs
- server OU
- group OU
- service-account OU
- privileged administrative-account OU
- standard domain users
- administrative identities
- Global security groups
- Domain Local security groups

The Active Directory structure provides the foundation for:

- Group Policy;
- delegated administration;
- access control;
- LAPS;
- BitLocker recovery integration;
- enterprise authentication.

## Documentation

- `docs/phase-2/active-directory-design.md`

---

# Phase 3 - Group Policy and Security Baselines

**Status: Completed**

## Objective

Introduce centralized Windows configuration and security-policy enforcement.

## Implemented

Current baseline GPO architecture includes:

- `EHSL - Workstation Baseline`
- `EHSL - Member Servers Baseline`
- `EHSL - Domain Controllers Baseline`

Security configuration includes controls such as:

- AutoPlay restrictions;
- AutoRun restrictions;
- Windows security auditing;
- Advanced Audit Policy;
- process creation auditing;
- process command-line auditing.

Group Policy later became the delivery mechanism for additional controls including:

- Windows Event Forwarding;
- Windows LAPS;
- BitLocker.

Those technologies are maintained through dedicated GPOs rather than being merged into a single baseline.

## Documentation

- `docs/phase-3/workstation-gpo-baseline.md`

---

# Phase 4 - DHCP

**Status: Completed**

## Objective

Provide centralized IPv4 address configuration for the EHSL internal network.

## Implemented

DHCP Server:

`EHSL-DC01`

Scope:

`10.10.10.0/24`

Dynamic range:

`10.10.10.100 - 10.10.10.200`

Delivered configuration includes:

- internal DNS server `10.10.10.10`;
- DNS domain `ehsl.internal`;
- eight-day lease.

Infrastructure reservation:

`EHSL-FS01 -> 10.10.10.20`

Dynamic client addressing validated with EHSL-CLIENT01.

## Documentation

- `docs/phase-4/dhcp-deployment.md`

---

# Phase 5 - File Services and Access Control

**Status: Completed**

## Objective

Implement centralized business file services with Active Directory-based access control.

## Implemented

Dedicated file server:

`EHSL-FS01`

Dedicated data volume:

`E:\`

Business shares:

- HR
- Finance
- Engineering
- Shared

Access-control architecture includes:

- Global security groups;
- Domain Local security groups;
- AGDLP;
- SMB permissions;
- NTFS permissions;
- departmental separation;
- read/write permission groups;
- read-only permission groups;
- file-access auditing.

Validation included positive and negative access tests where suitable test identities and group membership were available.

Windows Security Event ID `4663` was used to validate object-access auditing.

## Documentation

- `docs/phase-5/file-services-and-access-control.md`

---

# Phase 6 - Security Monitoring and Windows Event Forwarding

**Status: Completed**

## Objective

Create a centralized native Windows security-event pipeline.

## Architecture

Collector:

`EHSL-DC01`

Sources:

- `EHSL-FS01`
- `EHSL-CLIENT01`

Production subscription:

`EHSL - Security Events`

## Implemented

- Advanced Audit Policy
- process creation auditing
- command-line process auditing
- Windows Event Collector
- source-initiated Windows Event Forwarding
- centralized ForwardedEvents collection
- authentication telemetry
- account-management telemetry
- privilege telemetry
- process telemetry
- Kerberos telemetry
- audit-policy telemetry
- file-access telemetry

End-to-end forwarding was validated from both source systems.

File-access Event ID `4663` generated on EHSL-FS01 was successfully received by EHSL-DC01.

## Engineering Findings

Implementation troubleshooting identified two important issues:

1. the WEF source configuration required additional Security-log access for the forwarding service in the implemented lab configuration;
2. a temporary test subscription using a bandwidth-oriented delivery mode introduced significant delivery latency.

The final production configuration uses the validated low-latency event-delivery model.

Detailed troubleshooting and configuration decisions are maintained in the Phase 6 documentation.

## Documentation

- `docs/phase-6/security-monitoring.md`

---

# Phase 7 - Credential and Data Protection

**Status: Completed**

## Objective

Protect local administrative credentials and workstation data using Active Directory-integrated Windows security controls.

## Windows LAPS

Windows LAPS is implemented for:

- `EHSL-CLIENT01`
- `EHSL-FS01`

Implemented capabilities include:

- modern Windows LAPS;
- Active Directory password backup;
- encrypted password storage;
- built-in local Administrator management;
- 20-character passwords;
- password complexity;
- 30-day password age;
- computer self-permissions;
- delegated password retrieval;
- authorized password decryption through `GG_IT_Admins`.

Dedicated GPOs:

- `EHSL - Windows LAPS - Workstations`
- `EHSL - Windows LAPS - Member Servers`

Validation included:

- successful retrieval by an authorized administrator;
- denied retrieval by a standard domain user;
- independent managed passwords for the workstation and member server.

## BitLocker

BitLocker is implemented and validated on:

`EHSL-CLIENT01`

Implemented capabilities include:

- TPM 2.0;
- TPM-based operating-system protection;
- full C: volume encryption;
- XTS-AES 128;
- Recovery Password protector;
- Active Directory recovery escrow;
- recovery-password rotation validation.

Dedicated GPO:

`EHSL - BitLocker - Workstations`

EHSL-FS01 is not currently included in the BitLocker deployment.

## Documentation

- `docs/phase-7/credential-and-data-protection.md`

---

# Phase 8 - Windows Firewall Hardening

**Status: Next**

## Objective

Establish centrally managed Windows Firewall policy while preserving required EHSL infrastructure and business connectivity.

Planned work includes:

- review existing firewall state;
- define workstation and server requirements;
- centrally enforce firewall profiles;
- review inbound rules;
- minimize unnecessary exposure;
- preserve required Active Directory traffic;
- preserve WEF communication;
- preserve file-service connectivity;
- validate management traffic;
- document required exceptions;
- perform positive and negative connectivity testing.

Phase 8 is not considered implemented until configuration and validation are complete.

---

# Phase 9 - Microsoft Defender Hardening

**Status: Planned**

## Objective

Establish centrally managed Microsoft Defender Antivirus security configuration for applicable EHSL Windows systems.

Planned work includes evaluating and validating:

- real-time protection;
- cloud-delivered protection;
- scanning configuration;
- protection updates;
- security-relevant Defender settings;
- compatibility with existing EHSL workloads.

Additional Defender capabilities may be introduced only after the base configuration is validated.

Phase 9 is planned and is not currently represented as an implemented security control.

---
# Future Development

Potential later stages may include:

- additional PowerShell automation;
- network segmentation;
- Microsoft Entra ID;
- hybrid identity;
- Microsoft Intune;
- Microsoft Defender for Endpoint;
- Microsoft Sentinel;
- cloud security integration;
- additional detection engineering;
- expanded endpoint telemetry.

These items represent potential future development rather than committed or implemented project scope.

---

# Current Project Status

| Area | Status |
|---|---|
| Architecture | Completed |
| Virtual Infrastructure | Completed |
| Active Directory | Completed |
| Group Policy Baselines | Completed |
| DHCP | Completed |
| File Services | Completed |
| AGDLP Access Control | Completed |
| Security Auditing | Completed |
| Windows Event Forwarding | Completed |
| Phase 7 - Windows LAPS | Completed |
| Phase 7 - BitLocker on CLIENT01 | Completed |
| Phase 8 - Windows Firewall Hardening | Next |
| Phase 9 - Microsoft Defender Hardening | Planned |
| Cloud / Hybrid Integration | Future |

---

# Phase Management Rules

A phase should be marked **Completed** only when:

1. the required infrastructure or security control has been implemented;
2. the configuration has been validated;
3. important failures or troubleshooting findings have been understood;
4. the final state is documented;
5. temporary troubleshooting objects have been removed where appropriate.

A phase should not be marked complete simply because configuration work has started.

Likewise, planned technologies must not appear as active EHSL capabilities before they have been implemented.

---

# Documentation Relationship

This file tracks project progression and implementation status.

Detailed technical documentation is maintained under:

`docs/`

Historical implementation notes are maintained under:

`journal/`

Repository-level project presentation is maintained in:

`README.md`

This separation keeps the roadmap, technical documentation, engineering history, and public project overview focused on their respective purposes.
'@ | Set-Content -Path ".\phases\README.md" -Encoding UTF8
