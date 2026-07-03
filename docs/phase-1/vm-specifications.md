# VM Specifications

## EHSL-DC01

| Property | Value |
|----------|------|
| Hostname | EHSL-DC01 |
| Operating System | Windows Server 2022 Standard Evaluation |
| Role | Domain Controller |
| vCPU | 2 |
| Memory | 4096 MB |
| Disk | 50 GB (Dynamic) |
| Network | Servers |
| IP Address | 10.10.20.10 |
| Status | Planned |

---

## Justification

EHSL-DC01 is the first server deployed in the infrastructure.

It will later provide:

- Active Directory
- DNS
- Group Policy

The server has been intentionally sized with moderate resources because Domain Controllers have relatively low hardware requirements in environments of this size.
