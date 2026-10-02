@'
# Phase 0 - Architecture and Design

## 1. Project Vision

The Enterprise Hybrid Security Lab (EHSL) is a Microsoft-first enterprise infrastructure and security lab designed to reproduce realistic administration, identity, endpoint, monitoring, and security engineering scenarios.

The project focuses on building and validating infrastructure rather than simply deploying standalone security tools.

The current environment provides a foundation for:

- Windows Server administration
- Active Directory Domain Services
- DNS and DHCP
- Group Policy
- enterprise access control
- file services
- security auditing
- centralized Windows event collection
- Windows LAPS
- BitLocker
- future endpoint and Microsoft security hardening

Hybrid and cloud capabilities are part of the long-term direction of the project but are not represented as implemented capabilities unless they have been deployed and validated.

---

## 2. Business Scenario

EHSL models the infrastructure of a small-to-medium enterprise with multiple business departments and centralized IT administration.

The Active Directory structure currently represents:

- IT
- Security
- HR
- Finance
- Sales
- Engineering

The environment is designed to support realistic enterprise scenarios involving:

- standard users;
- privileged administrators;
- managed workstations;
- member servers;
- departmental file access;
- centralized policy enforcement;
- credential protection;
- security logging and investigation.

The business scenario provides context for technical decisions without requiring unnecessary infrastructure purely for simulation purposes.

---

## 3. Design Principles

The lab follows several architectural principles.

### Implementation Before Documentation Claims

Documentation must describe the environment that actually exists.

Planned technologies are identified as roadmap items and are not presented as deployed capabilities.

### Microsoft-First Infrastructure

The current implementation focuses on the Microsoft enterprise ecosystem:

- Windows Server
- Windows 11
- Active Directory
- DNS
- DHCP
- Group Policy
- Windows security controls
- PowerShell

### Least Privilege

Administrative privileges and access to business data are separated wherever possible.

### Centralized Management

Identity, policy, addressing, permissions, security configuration, and event collection are centrally managed.

### Security by Design

Security controls are integrated into infrastructure deployment rather than added only after services are operational.

### Validation

Controls are tested through positive and negative validation wherever practical.

---

## 4. Current Architecture

The currently implemented environment contains three virtual machines:

| System | Operating System | Primary Role |
|---|---|---|
| EHSL-DC01 | Windows Server 2022 Standard Evaluation | AD DS, DNS, DHCP, GPO, WEC |
| EHSL-FS01 | Windows Server 2022 Standard Evaluation | Member Server / File Server |
| EHSL-CLIENT01 | Windows 11 Pro Education | Managed Domain Workstation |

All systems are members of or provide services for:

`ehsl.internal`

The environment runs in Oracle VirtualBox on a Windows host.

---

## 5. Network Architecture

The current internal network is:

`10.10.10.0/24`

Each VM uses:

- a Host-Only adapter for the EHSL internal network;
- a NAT adapter for outbound Internet connectivity.

Current internal addressing:

| Host | Address |
|---|---:|
| EHSL-DC01 | `10.10.10.10` |
| EHSL-FS01 | `10.10.10.20` |
| EHSL-CLIENT01 | DHCP, currently `10.10.10.100` |

EHSL-DC01 uses a static internal address.

EHSL-FS01 receives `10.10.10.20` through a DHCP reservation.

EHSL-CLIENT01 receives its internal address dynamically from the EHSL DHCP scope.

There are currently no VLANs, DMZs, or separate server/workstation subnets.

Network segmentation remains a future architectural improvement.

---

## 6. Identity Architecture

The Active Directory forest and domain are:

`ehsl.internal`

EHSL-DC01 provides the domain controller role.

The EHSL Organizational Unit structure separates:

- users;
- workstations;
- servers;
- groups;
- service accounts;
- privileged administrative accounts.

User OUs are further divided by business department.

Workstation OUs provide additional separation for different endpoint use cases.

This structure supports targeted Group Policy deployment and delegated administration.

---

## 7. Infrastructure Roles

### EHSL-DC01

EHSL-DC01 provides the central infrastructure services required by the domain.

Implemented responsibilities include:

- Active Directory Domain Services
- DNS
- DHCP
- Group Policy infrastructure
- Windows Event Collector

It is the central control-plane system of the current lab.

### EHSL-FS01

EHSL-FS01 is a domain-joined member server dedicated to business file services.

It hosts departmental and shared SMB resources on a separate data volume.

Implemented shares include:

- HR
- Finance
- Engineering
- Shared

Access is controlled through Active Directory security groups, SMB permissions, and NTFS permissions.

### EHSL-CLIENT01

EHSL-CLIENT01 represents a managed enterprise Windows workstation.

It is:

- joined to `ehsl.internal`;
- located in the Standard workstation OU;
- centrally managed through Group Policy;
- configured as a Windows Event Forwarding source;
- protected using Windows LAPS;
- protected using BitLocker with TPM and AD DS recovery escrow.

---

## 8. Centralized Security Management

The current architecture supports centralized security controls through Active Directory and Group Policy.

Implemented controls include:

- workstation security baseline;
- member server security baseline;
- domain controller security baseline;
- Advanced Audit Policy;
- process creation auditing;
- Windows Event Forwarding;
- Windows LAPS;
- BitLocker policy for workstations.

Additional endpoint hardening, including Windows Firewall and Microsoft Defender configuration, remains part of the next implementation stages.

---

## 9. Security Monitoring Architecture

EHSL-DC01 operates as the Windows Event Collector.

EHSL-FS01 and EHSL-CLIENT01 forward selected Windows Security events to the collector using source-initiated Windows Event Forwarding.

The centralized event pipeline currently supports security events related to:

- authentication;
- account activity;
- privilege use;
- process creation;
- Kerberos;
- audit policy;
- file access.

This provides a native Windows telemetry layer that can later feed additional monitoring or SIEM technologies.

---

## 10. Architectural Decisions

### Single Internal Subnet

The current environment intentionally uses one internal subnet.

The objective at this stage is to build and validate enterprise services and security controls before introducing additional network complexity.

### Separate File Server

File services are separated from the domain controller to model a more realistic enterprise role boundary and provide a dedicated platform for access-control testing.

### WEC on the Domain Controller

Windows Event Collector currently runs on EHSL-DC01.

In a larger production environment, collection infrastructure would normally be separated according to scale, security, and availability requirements.

Within EHSL, consolidation is an intentional resource-management decision.

### No Linux Server in the Current Architecture

Earlier design iterations considered a Linux server.

No Linux VM is currently deployed, and Linux is therefore not part of the implemented EHSL architecture.

Linux services may be introduced in the future only if they support a defined project requirement.

---

## 11. Architecture Evolution

The initial design of EHSL was broader than the environment eventually implemented.

Earlier planning included:

- multiple internal network segments;
- a Linux server;
- a DMZ;
- a different internal domain naming model;
- additional infrastructure systems.

During implementation, the architecture was simplified to prioritize realistic depth, security validation, and efficient use of the available host resources.

The implemented domain is:

`ehsl.internal`

The implemented internal network is:

`10.10.10.0/24`

The current architecture should be treated as the source of truth for subsequent project phases.

---

## 12. Future Direction

Potential future architecture work includes:

- Windows Firewall hardening;
- Microsoft Defender hardening;
- additional PowerShell automation;
- network segmentation;
- Microsoft Entra ID integration;
- Microsoft Intune;
- Microsoft Defender for Endpoint;
- Microsoft Sentinel;
- additional hybrid identity and cloud security scenarios.

Future technologies will be added to the architecture documentation only after implementation or clearly marked as planned.

---

## 13. Phase 0 Status

**Status: Completed and reconciled with the implemented environment.**

Phase 0 now defines the architecture used by the rest of the EHSL project:

- Domain: `ehsl.internal`
- Internal network: `10.10.10.0/24`
- Domain Controller: EHSL-DC01
- File Server: EHSL-FS01
- Managed Workstation: EHSL-CLIENT01
- Virtualization: Oracle VirtualBox
- Internet access: VirtualBox NAT
- Internal communication: VirtualBox Host-Only network

This architecture is the baseline for all subsequent EHSL phases.
'@ | Set-Content -Path ".\docs\phase-0\phase-0-architecture.md" -Encoding UTF8