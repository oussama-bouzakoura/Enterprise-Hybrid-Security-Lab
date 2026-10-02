@'
# EHSL Network Design

## 1. Purpose

This document describes the current network architecture of the Enterprise Hybrid Security Lab (EHSL).

The network is intentionally compact because the lab runs on a resource-constrained physical host. The current design prioritizes functional enterprise services, centralized management, security controls, and observability before introducing additional network segmentation.

This document reflects the implemented environment. Future architectural improvements are explicitly identified as roadmap items rather than current capabilities.

---

## 2. Current Network Architecture

EHSL currently uses two VirtualBox network connections per virtual machine:

1. **Host-Only network** for internal EHSL domain communication.
2. **NAT network** for Internet connectivity.

The internal enterprise network is:

- Network: `10.10.10.0/24`
- Subnet mask: `255.255.255.0`
- AD DNS domain: `ehsl.internal`
- Internal DNS server: `10.10.10.10`
- DHCP server: `10.10.10.10`

All domain communication between EHSL systems uses the Host-Only network.

The NAT interface provides outbound connectivity without being used for internal Active Directory name resolution or domain services.

---

## 3. Current Addressing

| System | Role | Internal Address | Addressing Method |
|---|---|---:|---|
| EHSL-DC01 | Domain Controller / DNS / DHCP / WEC | `10.10.10.10/24` | Static |
| EHSL-FS01 | Member Server / File Server | `10.10.10.20/24` | DHCP reservation |
| EHSL-CLIENT01 | Windows Domain Workstation | `10.10.10.100/24` | DHCP |

### DHCP Scope

The active IPv4 DHCP scope is:

- Scope ID: `10.10.10.0`
- Scope name: `General DHCP range`
- Range: `10.10.10.100 - 10.10.10.200`
- Subnet mask: `255.255.255.0`
- State: Active
- Lease duration: `691200` seconds (8 days)
- DNS domain: `ehsl.internal`
- DNS server: `10.10.10.10`

### DHCP Reservation

EHSL-FS01 uses DHCP but receives a predictable server address through a reservation:

- Host: `EHSL-FS01.ehsl.internal`
- Reserved IP: `10.10.10.20`
- Client ID: `08-00-27-11-5e-ad`

This provides centralized address management while maintaining a stable address for the file server.

---

## 4. VirtualBox Network Model

Each EHSL VM currently has two network adapters.

### Internal Adapter

The Host-Only adapter connects the system to the EHSL internal network.

Its responsibilities include:

- Active Directory communication
- DNS resolution for `ehsl.internal`
- DHCP
- Group Policy processing
- SMB access
- Windows Event Forwarding
- Administrative communication between domain systems

The internal network does not use a default gateway.

### NAT Adapter

The NAT adapter provides outbound Internet connectivity through VirtualBox.

Observed NAT addressing is:

- VM address: `10.0.3.15/24`
- Gateway: `10.0.3.2`
- VirtualBox-provided DNS: `10.0.3.3`

The exact NAT-side addressing is provided by VirtualBox and is not part of the EHSL enterprise addressing plan.

---

## 5. DNS Architecture

EHSL-DC01 provides authoritative internal DNS services for the Active Directory domain:

`ehsl.internal`

Domain members use:

`10.10.10.10`

as their DNS server on the internal interface.

This ensures that Active Directory service discovery and internal hostname resolution are handled by the domain DNS infrastructure rather than by the VirtualBox NAT DNS service.

EHSL-DC01 itself uses its local DNS service on the internal interface.

---

## 6. DHCP Architecture

DHCP is hosted on EHSL-DC01.

The current DHCP service provides addressing for the internal `10.10.10.0/24` network.

The client allocation range is:

`10.10.10.100 - 10.10.10.200`

Infrastructure addresses below the dynamic client range can therefore be used for statically addressed or reserved systems.

EHSL-FS01 demonstrates the reservation model, receiving `10.10.10.20`.

EHSL-CLIENT01 demonstrates normal dynamic client addressing and currently receives `10.10.10.100`.

---

## 7. Current Security Model

The current network design provides logical separation between:

- the EHSL internal domain network; and
- outbound Internet connectivity.

However, the internal EHSL network is currently a single Layer 3 subnet.

There are currently no implemented:

- VLANs
- dedicated management network
- dedicated server subnet
- dedicated workstation subnet
- DMZ
- internal routing/firewall boundaries between EHSL systems

This is intentional at the current stage of the project.

Security boundaries are currently implemented primarily through:

- Active Directory
- Organizational Units
- Group Policy
- security groups
- NTFS permissions
- SMB permissions
- Windows auditing
- Windows Event Forwarding
- Windows LAPS
- BitLocker on the workstation

---

## 8. Design Constraints

EHSL runs on a single physical Windows host with limited memory resources.

For this reason, the project currently prioritizes depth of implementation over unnecessary VM count.

The architecture therefore consolidates several infrastructure functions while preserving separation where it provides clear security or operational value.

For example:

- Domain services are hosted on EHSL-DC01.
- File services are separated onto EHSL-FS01.
- User/workstation behavior is represented by EHSL-CLIENT01.
- Centralized Windows Event Collection is hosted on EHSL-DC01.

This design allows enterprise Windows security concepts to be implemented and validated within the available hardware.

---

## 9. Future Network Evolution

Potential future improvements include:

- separating servers and workstations into different network segments;
- introducing a dedicated management network;
- implementing inter-segment firewall policy;
- introducing a DMZ if externally exposed services are deployed;
- adding cloud connectivity where it provides meaningful hybrid identity or security use cases.

These are roadmap items and are not part of the currently implemented EHSL architecture.

---

## 10. Current State

The current EHSL network provides a functional foundation for:

- Active Directory
- DNS
- DHCP
- Group Policy
- centralized file services
- centralized Windows event collection
- endpoint security controls
- future Microsoft security integrations

The network architecture will evolve only when additional segmentation or hybrid connectivity provides a concrete security or operational benefit.
'@ | Set-Content -Path ".\docs\phase-0\network-design.md" -Encoding UTF8