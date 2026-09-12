# Enterprise Hybrid Security Lab (EHSL)

> A realistic enterprise infrastructure and security lab designed to simulate the architecture, operational practices and security controls found in modern corporate environments.

---

## Project Vision

Enterprise Hybrid Security Lab (EHSL) is not intended to be a traditional homelab.

Its objective is to build a realistic enterprise environment following industry best practices, documenting not only the implementation of technologies but also the architectural decisions behind them.

The project focuses on infrastructure engineering, Microsoft enterprise technologies, system administration, automation and defensive security.

Every implementation is designed, validated and documented as if it were deployed inside a production environment.

---

## Objectives

- Design a scalable Active Directory infrastructure
- Build a hybrid enterprise environment
- Implement Microsoft security best practices
- Automate administrative tasks using PowerShell
- Deploy enterprise security controls
- Implement scalable identity and access management
- Centralize security telemetry
- Document engineering decisions and implementation rationale
- Develop a professional infrastructure and security portfolio

---

# Current Project Status

## Core Infrastructure

| Component | Status |
|---|:---:|
| GitHub Repository | ✅ |
| Windows Server 2022 | ✅ |
| Windows 11 Client | ✅ |
| Active Directory Domain Services | ✅ |
| DNS | ✅ |
| DHCP | ✅ |
| Enterprise Domain | ✅ |
| Domain Controller - EHSL-DC01 | ✅ |
| File Server - EHSL-FS01 | ✅ |
| Domain Join | ✅ |
| DHCP Address Management | ✅ |

## Identity and Access Management

| Component | Status |
|---|:---:|
| Enterprise OU Structure | ✅ |
| Security Groups | ✅ |
| Administrative Accounts | ✅ |
| Standard Users | ✅ |
| Naming Convention | ✅ |
| AGDLP Authorization Model | ✅ |
| Resource-specific Access Groups | ✅ |
| SMB Access Control | ✅ |
| NTFS Access Control | ✅ |

## Group Policy and Hardening

| Component | Status |
|---|:---:|
| Workstation Baseline | ✅ |
| Member Server Baseline | ✅ |
| Domain Controller Baseline | ✅ |
| AutoPlay Hardening | ✅ |
| AutoRun Hardening | ✅ |
| Advanced Audit Policy | ✅ |
| Process Creation Auditing | ✅ |
| Command-line Auditing | ✅ |

## File Services

| Component | Status |
|---|:---:|
| File Server Role | ✅ |
| Departmental SMB Shares | ✅ |
| AGDLP Permissions | ✅ |
| NTFS Least Privilege | ✅ |
| File System Auditing | ✅ |
| Event ID 4663 Validation | ✅ |

## Security Monitoring

| Component | Status |
|---|:---:|
| Advanced Windows Auditing | ✅ |
| Windows Event Collector | ✅ |
| Source-initiated WEF Subscription | ✅ |
| WEF Group Policy | ✅ |
| Source Registration | ✅ |
| End-to-end Security Event Forwarding | 🚧 |

Current troubleshooting is focused on Security log forwarding error `5004`.

---

# Current Architecture

```text
                       EHSL.INTERNAL
                            |
                       EHSL-DC01
                  AD DS / DNS / DHCP
                   GPO / WEC Collector
                            |
              +-------------+-------------+
              |                           |
         EHSL-FS01                  EHSL-CLIENT01
     Windows Server 2022             Windows 11 Pro
       File Services               Domain Workstation
       SMB / NTFS
      File Auditing
```

Internal addressing currently includes:

```text
EHSL-DC01      10.10.10.10     Static
EHSL-FS01      10.10.10.20     DHCP Reservation
EHSL-CLIENT01  DHCP            Workstation Scope
```

Active Directory structure:

```text
EHSL
├── Admin Accounts
├── Groups
├── Servers
├── Service Accounts
├── Users
│   ├── Engineering
│   ├── Finance
│   ├── HR
│   ├── IT
│   ├── Sales
│   └── Security
└── Workstations
    ├── Developers
    ├── IT
    ├── Kiosk
    ├── Standard
    └── Testing
```

---

# Security Architecture

## Identity and Authorization

File access follows the AGDLP model:

```text
Accounts
   ↓
Global Groups
   ↓
Domain Local Groups
   ↓
NTFS Permissions
```

This separates user identity from resource authorization and avoids assigning file permissions directly to individual users.

## Windows Security Telemetry

The monitoring architecture currently being implemented is:

```text
Windows Activity
       ↓
Advanced Audit Policy
       ↓
Local Security Events
       ↓
Windows Event Forwarding
       ↓
EHSL-DC01 / WEC
       ↓
Forwarded Events
       ↓
Future SIEM / Detection Layer
```

---

# Engineering Decisions

| Decision | Documentation |
|---|---|
| Architecture and Network Design | [Phase 0](docs/phase-0/) |
| Virtual Machine Specifications | [Phase 1](docs/phase-1/) |
| Active Directory Design | [Phase 2](docs/phase-2/) |
| Workstation Security Baseline | [Phase 3](docs/phase-3/workstation-gpo-baseline.md) |
| DHCP and Network Services | [Phase 4](docs/phase-4/dhcp-deployment.md) |
| File Services and AGDLP | [Phase 5](docs/phase-5/file-services-and-access-control.md) |
| Security Monitoring and WEF | [Phase 6](docs/phase-6/security-monitoring.md) |

---

# Repository Structure

```text
Enterprise-Hybrid-Security-Lab
├── configs/
├── docs/
│   ├── phase-0/
│   ├── phase-1/
│   ├── phase-2/
│   ├── phase-3/
│   ├── phase-4/
│   ├── phase-5/
│   ├── phase-6/
│   └── standards/
├── journal/
├── scripts/
├── phases/
└── README.md
```

---

# Technology Stack

## Infrastructure

- Windows Server 2022
- Windows 11
- Active Directory Domain Services
- DNS
- DHCP
- Group Policy
- SMB File Services

## Administration and Automation

- PowerShell
- Windows Administration Tools
- RSAT
- Git
- GitHub

## Security

Currently implemented:

- Active Directory security groups
- AGDLP authorization model
- Administrative account separation
- Workstation security baseline
- Member Server security baseline
- Domain Controller audit baseline
- Advanced Audit Policy
- Process Creation auditing
- Command-line auditing
- File System auditing
- SMB and NTFS least-privilege access
- Windows Event Collector
- Windows Event Forwarding architecture

Currently in progress:

- End-to-end centralized Windows Security event forwarding

---

# Roadmap

## Phase 1 - Lab Foundation

Repository, documentation and infrastructure planning.

**Completed**

## Phase 2 - Active Directory

AD DS, DNS, enterprise OU structure, users, groups and administrative model.

**Completed**

## Phase 3 - Group Policy and Workstation Security

Workstation security baseline and initial hardening.

**Completed**

## Phase 4 - Network Services

DHCP, address management and internal DNS integration.

**Completed**

## Phase 5 - File Services and Access Control

File Server deployment, SMB shares, AGDLP, NTFS permissions and file system auditing.

**Completed**

## Phase 6 - Security Monitoring

Advanced Audit Policy and centralized Windows Event Forwarding.

**In Progress**

---

# Future Development

Planned capabilities include:

- Microsoft LAPS
- BitLocker
- Windows Firewall hardening
- PKI
- Microsoft Defender
- Microsoft Entra ID
- Microsoft Intune
- Microsoft Sentinel
- PowerShell automation
- Infrastructure monitoring
- Detection engineering

The roadmap is intentionally iterative. Technologies are introduced when they provide architectural or security value to the environment rather than solely for portfolio coverage.

---

# Project Methodology

Every major implementation follows the same engineering workflow:

```text
Design
  ↓
Implementation
  ↓
Troubleshooting
  ↓
Validation
  ↓
Documentation
  ↓
Version Control
```

The objective is not only to make the technology work, but to understand and document why each component exists, how it is secured and how it is validated.

---

# Author

**Oussama Bouzakoura**

Infrastructure • Systems • Security Engineering

This project is continuously evolving as new enterprise technologies are implemented and documented.