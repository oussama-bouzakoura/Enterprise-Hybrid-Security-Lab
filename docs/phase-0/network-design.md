# Network Design

## Purpose

The EHSL infrastructure is designed to simulate the network architecture of a medium-sized enterprise.

Instead of placing all devices in a single network, the infrastructure is divided into logical network segments. This approach improves security, simplifies administration and reflects common enterprise network design practices.

---

# Network Segments

| Network | Subnet | Purpose |
|---------|---------|---------|
| Management | 10.10.10.0/24 | Infrastructure administration |
| Servers | 10.10.20.0/24 | Windows and Linux servers |
| Clients | 10.10.30.0/24 | Corporate workstations |
| DMZ | 10.10.40.0/24 | Public-facing services |
| Cloud | Microsoft 365 / Entra ID | Identity and cloud services |

---

# Design Principles

The network follows several fundamental design principles:

- Network segmentation
- Least privilege
- Secure administration
- Scalability
- Defense in depth

---

# Administration

During the first phases of the project, administration will be performed from the Windows 10 host machine.

Later, a dedicated management workstation (MGMT01) will be introduced to centralize administrative access.

---

# Future Improvements

Future versions of the infrastructure may include:

- Dedicated firewall
- VPN
- VLANs
- Reverse proxy
- IDS/IPS
- Monitoring network
