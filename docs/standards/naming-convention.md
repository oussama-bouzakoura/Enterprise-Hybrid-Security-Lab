@'
# EHSL Naming Convention

## 1. Purpose

This document defines the naming conventions used throughout the Enterprise Hybrid Security Lab (EHSL).

Consistent naming improves:

- infrastructure readability;
- administrative efficiency;
- troubleshooting;
- automation;
- security analysis;
- documentation quality;
- scalability of the lab.

The conventions in this document reflect the currently implemented EHSL environment.

New naming standards should only be added when the corresponding technology or resource type is introduced into the lab.

---

## 2. General Principles

EHSL naming follows these principles:

1. Names should clearly communicate the purpose of the object.
2. Similar object types should follow the same naming structure.
3. Prefixes should be used where they provide useful context.
4. Names should remain concise enough for daily administration.
5. Naming should support PowerShell automation and filtering.
6. Production objects should not use ambiguous names such as `test`, `server1`, or `user1`.
7. Documentation must use the same names as the implemented environment.

The project identifier used throughout the lab is:

`EHSL`

---

## 3. Computer Naming

Windows computer names use the following general format:

`EHSL-<ROLE><NUMBER>`

or, for endpoint systems:

`EHSL-<TYPE><NUMBER>`

Current examples:

| Hostname | Meaning |
|---|---|
| `EHSL-DC01` | EHSL Domain Controller 01 |
| `EHSL-FS01` | EHSL File Server 01 |
| `EHSL-CLIENT01` | EHSL Client Workstation 01 |

---

## 4. Server Naming

Server names should identify the primary infrastructure role.

Current role abbreviations:

| Prefix | Role |
|---|---|
| `DC` | Domain Controller |
| `FS` | File Server |

Examples:

`EHSL-DC01`

`EHSL-FS01`

The numeric suffix allows additional systems of the same role to be introduced without changing the naming model.

For example, if a second domain controller were deployed in the future, the expected naming pattern would be:

`EHSL-DC02`

This is a naming convention example only and does not indicate that EHSL-DC02 currently exists.

---

## 5. Workstation Naming

Managed Windows endpoints use the current pattern:

`EHSL-CLIENT<number>`

Current implementation:

`EHSL-CLIENT01`

The endpoint name identifies the system as part of EHSL while remaining independent of a specific user.

This allows workstation ownership or user assignment to change without requiring the computer to be renamed.

---

## 6. Active Directory Domain Naming

The implemented Active Directory DNS domain is:

`ehsl.internal`

The NetBIOS domain name is:

`EHSL`

Examples:

`EHSL\john.smith`

`EHSL\adm.oussama`

The DNS namespace must be documented consistently as:

`ehsl.internal`

Legacy design names from earlier architecture iterations must not be used as current domain names.

---

## 7. Organizational Unit Naming

Organizational Units use descriptive names rather than technical abbreviations where practical.

Current top-level EHSL structure includes:

`OU=EHSL`

with functional child OUs including:

- `Admin Accounts`
- `Groups`
- `Servers`
- `Service Accounts`
- `Users`
- `Workstations`

Departmental user OUs include:

- `IT`
- `Security`
- `HR`
- `Finance`
- `Sales`
- `Engineering`

Workstation sub-OUs include:

- `Standard`
- `IT`
- `Kiosk`
- `Developers`
- `Testing`

Examples:

`OU=Servers,OU=EHSL,DC=ehsl,DC=internal`

`OU=Standard,OU=Workstations,OU=EHSL,DC=ehsl,DC=internal`

OU names should describe administrative or policy boundaries rather than individual machines.

---

## 8. Standard User Accounts

Standard user accounts use the following format:

`firstname.lastname`

Current examples include:

`john.smith`

`sara.johnson`

`alex.brown`

The naming pattern makes account ownership immediately identifiable while remaining predictable for administration and automation.

Standard user accounts must remain separate from privileged administrative accounts.

---

## 9. Privileged Administrative Accounts

Privileged administrative accounts use the prefix:

`adm.`

Current administrative account naming follows:

`adm.<name>`

Current example:

`adm.oussama`

The `adm.` prefix makes privileged identities visually distinguishable from normal user accounts.

Administrative accounts should be used only for tasks requiring elevated privileges.

Normal day-to-day user activity should not be performed using privileged administrative identities.

---

## 10. Service Accounts

Service accounts are stored separately under:

`OU=Service Accounts,OU=EHSL,DC=ehsl,DC=internal`

When service accounts are introduced, their names should clearly identify them as non-human identities.

The preferred naming pattern is:

`svc.<service>`

Example:

`svc.application`

This example defines the naming standard only and does not indicate that the account currently exists.

Service accounts should not use personal user naming conventions.

---

## 11. Active Directory Security Groups

EHSL distinguishes between Global Groups and Domain Local Groups through explicit prefixes.

### Global Groups

Global security groups use:

`GG_<FUNCTION>`

or:

`GG_<DEPARTMENT>_Users`

Current examples:

- `GG_IT_Admins`
- `GG_IT_Users`
- `GG_Security_Users`
- `GG_HR_Users`
- `GG_Finance_Users`
- `GG_Sales_Users`
- `GG_Engineering_Users`

Global groups represent users or administrative roles.

---

## 12. Domain Local Groups

Domain Local groups use the prefix:

`DL_`

For file services, the current structure uses:

`DL_FS_<RESOURCE>_<ACCESS>`

Where:

- `DL` = Domain Local
- `FS` = File Services
- `<RESOURCE>` = protected resource
- `<ACCESS>` = permission level

Examples:

`DL_FS_HR_RW`

`DL_FS_HR_RO`

`DL_FS_Finance_RW`

`DL_FS_Engineering_RW`

`DL_FS_Shared_RW`

Access suffixes currently include:

| Suffix | Meaning |
|---|---|
| `RW` | Read / Write |
| `RO` | Read Only |

This naming model makes the intended resource and permission level visible directly from the group name.

---

## 13. AGDLP Naming Relationship

EHSL file services follow the AGDLP model:

`Accounts -> Global Groups -> Domain Local Groups -> Permissions`

For example:

`john.smith`

becomes a member of:

`GG_HR_Users`

which can be nested into:

`DL_FS_HR_RW`

which receives permissions on the HR file resource.

The naming convention therefore distinguishes between:

- identity or business-role groups: `GG_*`
- resource-permission groups: `DL_*`

This separation is intentional and should be preserved.

---

## 14. Group Policy Object Naming

Custom EHSL Group Policy Objects use the prefix:

`EHSL - `

This makes project-specific GPOs easy to distinguish from built-in Active Directory policies.

Current GPOs include:

- `EHSL - Workstation Baseline`
- `EHSL - Member Servers Baseline`
- `EHSL - Domain Controllers Baseline`
- `EHSL - Windows Event Forwarding`
- `EHSL - Windows LAPS - Workstations`
- `EHSL - Windows LAPS - Member Servers`
- `EHSL - BitLocker - Workstations`

The general pattern is:

`EHSL - <Technology or Purpose>`

Where separate policies are required for different device classes:

`EHSL - <Technology> - <Target>`

Examples:

`EHSL - Windows LAPS - Workstations`

`EHSL - Windows LAPS - Member Servers`

Built-in policies retain their standard Microsoft names and are not renamed merely to match the EHSL convention.

---

## 15. File Share Naming

Business SMB shares use clear business-resource names.

Current shares:

- `HR`
- `Finance`
- `Engineering`
- `Shared`

Their corresponding paths are:

`E:\Shares\HR`

`E:\Shares\Finance`

`E:\Shares\Engineering`

`E:\Shares\Shared`

Share names should describe the resource from the user's perspective rather than expose unnecessary implementation details.

Windows administrative shares such as `E$` are system-generated and are not subject to the EHSL business-share naming convention.

---

## 16. File System Structure

Business file data on EHSL-FS01 uses the root path:

`E:\Shares`

Departmental or shared resources are created below this location.

General pattern:

`E:\Shares\<Resource>`

This keeps business data separate from the operating system volume and provides a predictable structure for administration and permissions.

---

## 17. Windows Event Forwarding Naming

Windows Event Forwarding subscriptions use descriptive EHSL-prefixed names.

The production security subscription is:

`EHSL - Security Events`

Temporary troubleshooting subscriptions may be created when required, but they must be removed after validation if they are no longer operationally necessary.

Temporary objects should not remain in the environment or documentation as if they were production components.

---

## 18. DHCP Naming

DHCP reservations should use the DNS identity of the target infrastructure system where practical.

Current example:

`EHSL-FS01.ehsl.internal`

The reservation maps this system to:

`10.10.10.20`

DHCP scope names should describe their purpose rather than a specific client.

The current scope is:

`General DHCP range`

---

## 19. Documentation Naming

Project documentation uses lowercase descriptive filenames with hyphens.

Examples:

`network-design.md`

`active-directory-design.md`

`workstation-gpo-baseline.md`

`file-services-and-access-control.md`

`security-monitoring.md`

Markdown files should use the `.md` extension.

Phase-specific documentation is stored under:

`docs/phase-<number>/`

Standards that apply across multiple phases are stored under:

`docs/standards/`

---

## 20. Script Naming

When operational scripts are added to the repository, filenames should describe the action performed.

Preferred style:

`Verb-Noun.ps1`

for PowerShell scripts where practical.

Examples of possible names:

`Get-EHSLInventory.ps1`

`Test-EHSLConnectivity.ps1`

These examples define the convention only and do not indicate that the scripts currently exist.

Scripts should not be named with ambiguous filenames such as:

`script1.ps1`

`test.ps1`

`new.ps1`

---

## 21. Future Technologies

Naming conventions for technologies that are not currently implemented should not be prematurely defined as part of the active architecture.

This currently includes areas such as:

- Azure resources;
- Microsoft Entra ID cloud objects;
- Intune objects;
- Microsoft Sentinel resources;
- additional network segments;
- Linux infrastructure.

When these technologies are implemented, their naming conventions should be added to this document based on the actual deployed design.

---

## 22. Naming Standard Summary

| Object Type | Convention | Example |
|---|---|---|
| Domain Controller | `EHSL-DC##` | `EHSL-DC01` |
| File Server | `EHSL-FS##` | `EHSL-FS01` |
| Workstation | `EHSL-CLIENT##` | `EHSL-CLIENT01` |
| Standard User | `firstname.lastname` | `john.smith` |
| Admin Account | `adm.<name>` | `adm.oussama` |
| Service Account | `svc.<service>` | Naming standard only |
| Global Group | `GG_<Role>` | `GG_HR_Users` |
| Domain Local Group | `DL_<Resource>_<Access>` | `DL_FS_HR_RW` |
| Custom GPO | `EHSL - <Purpose>` | `EHSL - Workstation Baseline` |
| Business Share | `<Resource>` | `Finance` |
| Documentation | lowercase-hyphenated `.md` | `network-design.md` |

---

## 23. Standard Status

**Status: Active**

These conventions represent the current EHSL naming model and should be followed when new objects are introduced.

If the architecture evolves, this document should be updated before inconsistent naming patterns become established.
'@ | Set-Content -Path ".\docs\standards\naming-convention.md" -Encoding UTF8
