@'
# Phase 1 - Virtual Machine Specifications

## 1. Purpose

This document defines the virtual machines currently deployed in the Enterprise Hybrid Security Lab and records their validated guest-visible resource allocation.

EHSL runs in Oracle VirtualBox on a Windows physical host.

The lab is intentionally resource-conscious and currently uses three virtual machines.

---

## 2. Virtual Machine Overview

| VM | Operating System | RAM | Logical Processors | System Disk | Role |
|---|---|---:|---:|---:|---|
| EHSL-DC01 | Windows Server 2022 Standard Evaluation | 4 GB | 2 | 50 GB | Domain Controller / Infrastructure Services |
| EHSL-FS01 | Windows Server 2022 Standard Evaluation | 2 GB | 2 | 40 GB | Member Server / File Server |
| EHSL-CLIENT01 | Windows 11 Pro Education | 3.98 GB | 2 | 50 GB | Managed Workstation |

EHSL-FS01 also has a dedicated 20 GB data disk.

---

## 3. EHSL-DC01

### Operating System

- Microsoft Windows Server 2022 Standard Evaluation
- Version: `10.0.20348`
- Build: `20348`

### Compute

- RAM: 4 GB
- Logical processors visible to guest: 2

### Storage

- System disk: 50 GB
- Partition style: MBR

### Networking

Two network interfaces are used.

#### Internal

- Interface: Ethernet
- Address: `10.10.10.10/24`
- Static addressing
- No default gateway
- Provides connectivity to the EHSL Host-Only network

#### Internet / NAT

- Interface: Ethernet 2
- VirtualBox NAT
- Observed guest address: `10.0.3.15/24`
- Gateway: `10.0.3.2`

### Roles

EHSL-DC01 provides:

- Active Directory Domain Services
- DNS
- DHCP
- Group Policy infrastructure
- Windows Event Collector

---

## 4. EHSL-FS01

### Operating System

- Microsoft Windows Server 2022 Standard Evaluation
- Version: `10.0.20348`
- Build: `20348`

### Compute

- RAM: 2 GB
- Logical processors visible to guest: 2

### Storage

#### System Disk

- Size: 40 GB
- Partition style: MBR
- C: NTFS

#### Data Disk

- Size: 20 GB
- Partition style: GPT
- E: NTFS
- Volume label: `DATA`

The data disk provides dedicated storage for departmental and shared file services.

### Networking

#### Internal

- Interface: Ethernet
- Address: `10.10.10.20/24`
- Address assignment: DHCP reservation
- DNS: `10.10.10.10`

#### Internet / NAT

- Interface: Ethernet 2
- VirtualBox NAT
- Observed guest address: `10.0.3.15/24`
- Gateway: `10.0.3.2`

### Roles

- Domain member server
- Windows File Server
- Windows Event Forwarding source
- Windows LAPS-managed server

---

## 5. EHSL-CLIENT01

### Operating System

- Microsoft Windows 11 Pro Education
- Version: `10.0.26200`
- Build: `26200`

### Compute

- RAM visible to guest: 3.98 GB
- Logical processors visible to guest: 2

### Storage

- System disk: 50 GB
- Partition style: GPT
- C: NTFS

### Networking

#### Internal

- Interface: Ethernet
- Address assignment: DHCP
- Observed address: `10.10.10.100/24`
- DNS: `10.10.10.10`

#### Internet / NAT

- Interface: Ethernet 2
- VirtualBox NAT
- Observed guest address: `10.0.3.15/24`
- Gateway: `10.0.3.2`

### Security Capabilities

EHSL-CLIENT01 currently provides the managed endpoint used to validate:

- domain membership;
- Group Policy;
- Advanced Audit Policy;
- Windows Event Forwarding;
- Windows LAPS;
- TPM-backed BitLocker;
- AD DS recovery information escrow.

---

## 6. Network Adapter Model

Each current VM has two logical network paths:

### Host-Only

Used for enterprise lab traffic:

`10.10.10.0/24`

This includes:

- AD DS
- DNS
- DHCP
- Group Policy
- SMB
- administrative traffic
- Windows Event Forwarding

### NAT

Used for outbound Internet access.

The NAT network is provided by VirtualBox and is not treated as part of the EHSL internal enterprise addressing scheme.

---

## 7. Resource Allocation Strategy

The physical EHSL host has limited memory resources.

VM allocation is therefore intentionally conservative.

EHSL prioritizes:

1. maintaining a functional domain controller;
2. separating business file services from the domain controller;
3. maintaining at least one managed workstation;
4. implementing security controls deeply;
5. avoiding additional VMs without a defined technical requirement.

This approach provides greater implementation depth without exceeding the practical capacity of the host.

---

## 8. Current VM Set

The currently supported VM set is:

- `EHSL-DC01`
- `EHSL-FS01`
- `EHSL-CLIENT01`

No Linux VM is currently deployed.

Additional VMs should only be introduced when they provide a concrete infrastructure, security, monitoring, or hybrid-cloud use case.

---

## 9. Snapshot Strategy

Snapshots are used before significant security or infrastructure changes to provide a controlled recovery point during lab development.

A validated snapshot exists for the current systems from the security-hardening stage:

`Pre-BitLocker - WEF-LAPS Validated`

Snapshots are a lab recovery mechanism and should not be interpreted as a substitute for an enterprise backup strategy.

---

## 10. Phase 1 Status

**Status: Completed**

The three-VM architecture provides the current virtualization foundation for EHSL:

- centralized infrastructure services on EHSL-DC01;
- dedicated business file services on EHSL-FS01;
- managed endpoint validation on EHSL-CLIENT01.

Subsequent phases build security and enterprise services on this foundation.
'@ | Set-Content -Path ".\docs\phase-1\vm-specifications.md" -Encoding UTF8