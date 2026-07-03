# Server Inventory

This document tracks all virtual machines that compose the EHSL infrastructure.

| Hostname | Operating System | Role | IP Address | Status |
|----------|-----------------|------|------------|--------|
| EHSL-DC01 | Windows Server 2022 Standard (Desktop Experience) | Future Domain Controller | 10.10.10.10 | Deployed |
| EHSL-LNX01 | Ubuntu Server 24.04 LTS | Linux Infrastructure Services | 10.10.20.20 | Planned |
| WIN11-CLIENT01 | Windows 11 | Corporate Workstation | 10.10.30.10 | Planned |

---

## Notes

EHSL-DC01 has completed its initial baseline and is ready for Active Directory deployment.

| Property | Value |
|----------|-------|
| Hostname | EHSL-DC01 |
| Operating System | Windows Server 2022 Standard Evaluation (Desktop Experience) |
| Role | Future Domain Controller |
| vCPU | 2 |
| Memory | 4096 MB |
| Disk | 50 GB Dynamic VDI |
| Network Adapter 1 | Host-Only Adapter - 10.10.10.10 |
| Network Adapter 2 | NAT |
| Snapshot | EHSL-DC01 - Baseline |
| Status | Deployed / Baseline Completed |
