# Session 001

## Objectives

- Create the project repository.
- Design the initial architecture.
- Deploy the first Windows Server.
- Validate the server baseline.

---

## Completed

- GitHub repository created.
- Initial documentation completed.
- Windows Server 2022 deployed.
- Host-Only management network configured.
- NAT adapter added for Internet access.
- Windows fully updated.
- Guest Additions installed.
- Baseline validated.
- First snapshot created.

---

## Challenges

### Networking

Initially, the server was deployed using only a Host-Only adapter. This allowed communication with the host but provided no Internet access.

A second NAT adapter was added to simplify updates while maintaining an isolated management network.

### Guest Additions

Guest Additions were correctly installed, but automatic screen resizing was disabled in VirtualBox. Enabling "Auto-resize Guest Display" solved the issue.

---

## Engineering Decisions

- Selected Windows Server 2022 Standard Desktop Experience.
- Selected Dynamic VDI storage.
- Selected Host-Only networking for management.
- Added a NAT adapter for Internet connectivity during the build phase.
- Created the first baseline snapshot before deploying any server roles.

---

## Lessons Learned

- Infrastructure should be designed before deployment.
- Always validate connectivity before assuming a networking issue.
- Build, Validate and Document is a more efficient workflow than documenting every individual step.
