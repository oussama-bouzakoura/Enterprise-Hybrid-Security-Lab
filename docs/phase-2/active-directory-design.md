# Active Directory Design

## Purpose

This document defines the identity architecture of the Enterprise Hybrid Security Lab (EHSL).

Rather than focusing on the technical installation of Active Directory, this document describes the organizational model that Active Directory will implement.

---

# Domain

Internal Active Directory Domain:

ehsl.internal

Reasoning:

The project uses an internal namespace to clearly separate the laboratory environment from any future public domain while maintaining a realistic enterprise naming convention.

---

# Organizational Unit Strategy

EHSL follows a hybrid Organizational Unit (OU) model.

Workstations are organized by device type or operational use case, not by department. User access is managed through security groups, while workstation OUs are used mainly for device configuration and GPO targeting.

Infrastructure objects such as servers, groups, service accounts and privileged accounts are separated into dedicated OUs because they require different security controls.

---

# Initial OU Structure

EHSL
├── Users
│   ├── IT
│   ├── Security
│   ├── HR
│   ├── Finance
│   ├── Sales
│   └── Engineering
│
Workstations
├── Standard
├── IT
├── Kiosk
├── Developers
└── Testing
│
├── Servers
├── Groups
├── Service Accounts
└── Admin Accounts
