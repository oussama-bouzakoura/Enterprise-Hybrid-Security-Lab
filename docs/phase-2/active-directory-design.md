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

Users and workstations are organized by department to simplify administration, delegation, and Group Policy assignment.

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
├── Workstations
│   ├── IT
│   ├── Security
│   ├── HR
│   ├── Finance
│   ├── Sales
│   └── Engineering
│
├── Servers
├── Groups
├── Service Accounts
└── Admin Accounts
