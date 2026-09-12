# Phase 5 - File Services and Access Control

## Objective

Deploy a centralized Windows file server and implement enterprise-style access control using Active Directory groups, AGDLP, SMB permissions, NTFS permissions and file system auditing.

The goal of this phase is to separate user identity from resource permissions and establish a scalable authorization model.

---

## Server

| Property | Value |
|---|---|
| Hostname | EHSL-FS01 |
| Operating System | Windows Server 2022 |
| Domain | ehsl.internal |
| OU | EHSL\Servers |
| File Server Role | Installed |
| Data Volume | E: |
| Data Root | E:\Shares |

EHSL-FS01 is domain joined and uses the EHSL internal network for domain services and SMB access.

---

## File Share Structure

```text
E:\Shares
├── HR
├── Finance
├── Engineering
└── Shared
```

Published SMB shares:

```text
\\EHSL-FS01\HR
\\EHSL-FS01\Finance
\\EHSL-FS01\Engineering
\\EHSL-FS01\Shared
```

---

## Authorization Model

EHSL uses the AGDLP model:

```text
Accounts
   ↓
Global Groups
   ↓
Domain Local Groups
   ↓
Permissions
```

Users are assigned to department-level Global Groups. Global Groups are then nested into resource-specific Domain Local Groups, and permissions are assigned to those Domain Local Groups rather than directly to individual users.

This separates identity management from resource authorization and provides a scalable access-control model.

---

## Global Groups

```text
GG_HR_Users
GG_Finance_Users
GG_Engineering_Users
GG_Sales_Users
GG_Security_Users
GG_IT_Admins
```

Current relevant memberships:

```text
john.smith   → GG_HR_Users
sara.johnson → GG_Finance_Users
alex.brown   → GG_Security_Users
```

---

## Resource Groups

Each file resource uses separate Read/Write and Read-Only Domain Local Groups.

```text
DL_FS_HR_RW
DL_FS_HR_RO

DL_FS_Finance_RW
DL_FS_Finance_RO

DL_FS_Engineering_RW
DL_FS_Engineering_RO

DL_FS_Shared_RW
DL_FS_Shared_RO
```

Configured AGDLP mappings:

```text
GG_HR_Users          → DL_FS_HR_RW
GG_Finance_Users     → DL_FS_Finance_RW
GG_Engineering_Users → DL_FS_Engineering_RW
```

Shared access:

```text
GG_HR_Users
GG_Finance_Users
GG_Engineering_Users
GG_Sales_Users
GG_Security_Users
        ↓
DL_FS_Shared_RW
```

`GG_IT_Admins` is intentionally not included in business data access groups.

Administrative privileges do not automatically imply business-data access.

---

## SMB Permissions

Share-level permissions are configured as:

```text
Authenticated Users → Change
Administrators      → Full Control
```

The effective authorization decision is primarily enforced through NTFS permissions.

`Everyone: Full Control` is not used.

---

## NTFS Permissions

Inheritance is disabled on the root of each managed share.

Inherited permissions were converted to explicit permissions before broad entries were removed.

The resulting authorization model is:

```text
Administrators       → Full Control
SYSTEM               → Full Control
DL_FS_<Resource>_RW  → Modify
DL_FS_<Resource>_RO  → Read & Execute
```

Broad entries removed include:

```text
BUILTIN\Users
CREATOR OWNER
```

RW users receive `Modify`, not `Full Control`. This allows normal file operations without allowing standard users to modify permissions or take ownership.

---

## Access Validation

The following access tests were completed:

| User | HR | Finance | Engineering | Shared |
|---|---|---|---|---|
| john.smith | RW | Denied | Denied | RW |
| sara.johnson | Denied | RW | Denied | RW |
| alex.brown | Denied | Denied | Denied | RW |

The results matched the intended AGDLP design.

Engineering positive access has not yet been functionally tested because `GG_Engineering_Users` currently has no member.

Read-only access has not yet been functionally tested because the RO groups are currently empty.

No artificial users were created solely to make validation tests pass.

---

## File System Auditing

Advanced Audit Policy for File System access is enabled on EHSL-FS01.

Auditing was configured on the managed share roots using a SACL with:

```text
Principal: Authenticated Users
Audit type: Success
```

Audited operations include:

```text
Read/List
Write/Create
Append/Create folders
Write attributes
Write extended attributes
Delete
Delete subfolders/files
Change permissions
Take ownership
```

The auditing configuration is inherited by files and subfolders.

---

## Validation

File auditing was successfully validated using Windows Security Event ID:

```text
4663 - An attempt was made to access an object
```

Validated event information includes:

```text
Account Name
Object Name
Accesses
Process Information
```

Example validated workflow:

```text
john.smith
    ↓
\\EHSL-FS01\HR
    ↓
File create / modify / delete
    ↓
Security Event 4663 on EHSL-FS01
```

The same auditing configuration was extended to:

```text
HR
Finance
Engineering
Shared
```

Finance auditing was additionally validated using `sara.johnson`.

---

## Security Value

This phase establishes several enterprise security principles:

- Role-based access through Active Directory groups
- Separation between identity and resource permissions
- Least privilege
- No direct user ACL assignments
- No broad `Everyone: Full Control` configuration
- Separation between administrative rights and business-data access
- File access auditing
- Security events suitable for later centralized monitoring

---

## Phase Status

**Completed**

The file server, authorization model, SMB permissions, NTFS ACLs and file system auditing have been implemented and validated.