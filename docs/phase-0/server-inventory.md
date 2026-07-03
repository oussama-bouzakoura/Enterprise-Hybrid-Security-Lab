# Server Inventory

This document tracks all virtual machines that compose the EHSL infrastructure.

| Hostname | Operating System | Role | IP Address | Status |
|----------|------------------|------|------------|--------|
| EHSL-DC01 | Windows Server 2022 Standard Evaluation (Desktop Experience) | Future Domain Controller | 10.10.10.10 | Deployed |
| EHSL-LNX01 | Ubuntu Server 24.04 LTS | Linux Infrastructure Services | 10.10.20.20 | Planned |
| WIN11-CLIENT01 | Windows 11 | Corporate Workstation | 10.10.30.10 | Planned |

## Notes

EHSL-DC01 has completed its initial baseline and is ready for Active Directory deployment.

Baseline completed:

- Windows Server installed
- Static IP configured
- Host-Only network configured
- NAT adapter added for Internet access
- Windows Updates completed
- Guest Additions installed and validated
- Baseline snapshot created

  
---

## Future Servers

The following servers are planned for future phases:

| Hostname | Purpose |
|----------|---------|
| WEB01 | Internal web services |
| FILE01 | File server |
| MGMT01 | Management workstation |
| BACKUP01 | Backup services |
