@'
# EHSL Infrastructure Inventory

## 1. Purpose

This document provides the current infrastructure inventory for the Enterprise Hybrid Security Lab.

Only systems that are actually deployed are listed as active infrastructure.

---

## 2. Current Systems

| Hostname | Operating System | RAM | Logical Processors | Internal IP | Primary Role |
|---|---|---:|---:|---:|---|
| EHSL-DC01 | Windows Server 2022 Standard Evaluation | 4 GB | 2 | `10.10.10.10` | Domain Controller / DNS / DHCP / WEC |
| EHSL-FS01 | Windows Server 2022 Standard Evaluation | 2 GB | 2 | `10.10.10.20` | Member Server / File Server |
| EHSL-CLIENT01 | Windows 11 Pro Education | 3.98 GB | 2 | DHCP (`10.10.10.100` observed) | Managed Workstation |

---

## 3. EHSL-DC01

### System

- Hostname: `EHSL-DC01`
- Operating System: Microsoft Windows Server 2022 Standard Evaluation
- Version: `10.0.20348`
- Build: `20348`
- RAM: 4 GB
- Logical processors: 2

### Storage

- Disk 0: 50 GB
- Partition style: MBR

### Internal Network

- Interface: Ethernet
- Address: `10.10.10.10/24`
- Addressing: Static
- Default gateway: None on internal interface
- DNS: Local DNS service

### NAT Network

- Interface: Ethernet 2
- Observed address: `10.0.3.15/24`
- Gateway: `10.0.3.2`

### Core Roles

- Active Directory Domain Services
- DNS Server
- DHCP Server
- Group Policy infrastructure
- Windows Event Collector

EHSL-DC01 also has the Windows File Server feature installed, but EHSL-FS01 is the designated business file server in the current architecture.

---

## 4. EHSL-FS01

### System

- Hostname: `EHSL-FS01`
- Operating System: Microsoft Windows Server 2022 Standard Evaluation
- Version: `10.0.20348`
- Build: `20348`
- Domain: `ehsl.internal`
- Domain joined: Yes
- RAM: 2 GB
- Logical processors: 2

### Storage

#### System Disk

- Disk 0: 40 GB
- Partition style: MBR
- C: approximately 39.39 GB NTFS

#### Data Disk

- Disk 1: 20 GB
- Partition style: GPT
- E: approximately 19.98 GB
- Label: `DATA`
- File system: NTFS

The dedicated E: volume hosts business file shares.

### Internal Network

- Interface: Ethernet
- Address: `10.10.10.20/24`
- Addressing: DHCP reservation
- Internal DNS: `10.10.10.10`

DHCP reservation:

- Scope: `10.10.10.0`
- Reserved address: `10.10.10.20`
- Name: `EHSL-FS01.ehsl.internal`

### NAT Network

- Interface: Ethernet 2
- Observed address: `10.0.3.15/24`
- Gateway: `10.0.3.2`

### Active Directory Placement

`CN=EHSL-FS01,OU=Servers,OU=EHSL,DC=ehsl,DC=internal`

### File Services

The Windows File Server role is installed.

Business shares:

| Share | Path |
|---|---|
| HR | `E:\Shares\HR` |
| Finance | `E:\Shares\Finance` |
| Engineering | `E:\Shares\Engineering` |
| Shared | `E:\Shares\Shared` |

The administrative `E$` share is a standard Windows administrative share and is not considered a business share.

Business share-level permissions use:

- `BUILTIN\Administrators` - Full
- `Authenticated Users` - Change

Detailed business access control is enforced through NTFS permissions and Active Directory groups as documented in Phase 5.

---

## 5. EHSL-CLIENT01

### System

- Hostname: `EHSL-CLIENT01`
- Operating System: Microsoft Windows 11 Pro Education
- Version: `10.0.26200`
- Build: `26200`
- Domain: `ehsl.internal`
- Domain joined: Yes
- RAM: 3.98 GB
- Logical processors: 2

### Storage

- Disk 0: 50 GB
- Partition style: GPT
- C: approximately 48.95 GB NTFS

### Internal Network

- Interface: Ethernet
- Addressing: DHCP
- Observed address: `10.10.10.100/24`
- Internal DNS: `10.10.10.10`

The observed address may change because CLIENT01 uses dynamic DHCP addressing.

### NAT Network

- Interface: Ethernet 2
- Observed address: `10.0.3.15/24`
- Gateway: `10.0.3.2`

### Active Directory Placement

`CN=EHSL-CLIENT01,OU=Standard,OU=Workstations,OU=EHSL,DC=ehsl,DC=internal`

### Endpoint Security

Current validated endpoint security capabilities include:

- TPM present and ready
- BitLocker fully encrypted
- XTS-AES 128 encryption
- TPM protector
- Recovery Password protector
- BitLocker protection enabled
- Windows LAPS
- centralized Group Policy
- Windows Event Forwarding

Recovery passwords are not stored in project documentation.

---

## 6. DHCP Infrastructure

EHSL-DC01 hosts the active DHCP service.

Current scope:

- Network: `10.10.10.0/24`
- Dynamic range: `10.10.10.100 - 10.10.10.200`
- DNS domain: `ehsl.internal`
- DNS server: `10.10.10.10`
- Lease duration: 8 days

Current infrastructure reservation:

| Host | Reserved IP |
|---|---:|
| EHSL-FS01 | `10.10.10.20` |

---

## 7. Deployment Status

| System | Status |
|---|---|
| EHSL-DC01 | Operational |
| EHSL-FS01 | Operational |
| EHSL-CLIENT01 | Operational |

No Linux server is currently deployed.

No additional server should be listed as active infrastructure until it has actually been deployed and validated.

---

## 8. Inventory Maintenance

This document should be updated whenever:

- a VM is added or removed;
- VM resources materially change;
- server roles change;
- persistent internal addressing changes;
- storage architecture changes;
- a new infrastructure service becomes operational.

Historical plans should remain in the project journal or Git history rather than being represented as current inventory.
'@ | Set-Content -Path ".\docs\phase-0\server-inventory.md" -Encoding UTF8