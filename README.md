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
- Document engineering decisions and implementation rationale
- Develop a professional infrastructure portfolio

---

# Current Project Status

## Core Infrastructure

| Component | Status |
|-----------|:------:|
| GitHub Repository | ✅ |
| Project Documentation | ✅ |
| Windows Server 2022 | ✅ |
| Active Directory Domain Services | ✅ |
| DNS | ✅ |
| Enterprise Domain | ✅ |
| Domain Controller (EHSL-DC01) | ✅ |
| Windows 11 Client | ✅ |
| Domain Join | ✅ |

---

## Identity Management

| Component | Status |
|-----------|:------:|
| Enterprise OU Structure | ✅ |
| Security Groups | ✅ |
| Administrative Accounts | ✅ |
| Standard Users | ✅ |
| Naming Convention | ✅ |

---

## Group Policy

| Component | Status |
|-----------|:------:|
| Workstation Baseline | ✅ |
| AutoPlay Hardening | ✅ |
| AutoRun Hardening | ✅ |

---

## Validation

The following components have been successfully validated.

- Active Directory deployment
- DNS functionality
- Domain join
- SYSVOL replication
- Group Policy deployment
- Administrative account model
- Enterprise OU design

---

# Current Architecture

```
EHSL
│
├── Users
│   ├── IT
│   ├── Security
│   ├── HR
│   ├── Finance
│   ├── Sales
│   └── Engineering
│
├── Workstations
│   ├── Standard
│   ├── IT
│   ├── Developers
│   ├── Kiosk
│   └── Testing
│
├── Servers
├── Groups
├── Service Accounts
└── Admin Accounts
```

---

# Engineering Decisions

The project documents not only implementations, but also the reasoning behind each architectural decision.

| Decision | Documentation |
|----------|---------------|
| Active Directory Design | [Phase 2 documentation](docs/phase-2/) |
| OU Structure | [Phase 2 documentation](docs/phase-2/) |
| Administrative Account Strategy | [Phase 2 documentation](docs/phase-2/) |
| Group Strategy (AGDLP) | [Phase 2 documentation](docs/phase-2/) |
| Workstation Baseline GPO | [Workstation GPO Baseline](docs/phase-3/workstation-gpo-baseline.md) |


# Repository Structure

```
Enterprise-Hybrid-Security-Lab
│
├── configs/
├── docs/
│   ├── phase-0/
│   ├── phase-1/
│   ├── phase-2/
│   ├── phase-3/
│   └── standards/
│
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
- Active Directory
- DNS
- Group Policy

## Administration

- PowerShell
- Windows Administration Tools
- RSAT

## Security

Currently implemented:

- Group Policy hardening
- AutoPlay and AutoRun restrictions

Planned:

- Microsoft Security Baselines
- Microsoft Defender
- Windows Firewall hardening
---

# Roadmap

## Phase 1

- Repository
- Documentation
- Lab planning

✅ Completed

---

## Phase 2

- Active Directory
- DNS
- Enterprise structure
- Administrative model

✅ Completed

---

## Phase 3

- Group Policy
- Workstation baseline
- Security hardening

🟡 In Progress

---

## Upcoming Phases

- DHCP
- File Server
- DFS
- PKI
- WSUS
- LAPS
- BitLocker
- Windows Event Forwarding
- Defender for Endpoint
- Microsoft Intune
- Microsoft Entra ID
- Microsoft Sentinel integration
- Automation with PowerShell
- Infrastructure monitoring

---

# Project Methodology

Every implementation follows the same engineering workflow:

1. Design
2. Implementation
3. Validation
4. Documentation
5. Version Control

This ensures that every change is reproducible, validated and properly documented.

---

# Author

**Oussama Bouzakoura**

Infrastructure • Systems • Security Engineering

This project is continuously evolving as new enterprise technologies are implemented and documented.
<!-- Git workflow test -->