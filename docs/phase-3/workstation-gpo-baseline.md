# EHSL - Workstation Baseline

## Overview

The **EHSL - Workstation Baseline** Group Policy Object defines the minimum configuration applied to every corporate workstation within the Enterprise Hybrid Security Lab.

Its purpose is to provide a consistent, secure and manageable baseline for all standard domain-joined endpoints.

The baseline will evolve incrementally as new infrastructure components are introduced (DHCP, WSUS, Defender, LAPS, PKI, BitLocker, Microsoft Intune, etc.).

---

## Scope

Linked OU:

```
EHSL
└── Workstations
```

The baseline applies to every workstation under this Organizational Unit through inheritance.

Device-specific configurations will be implemented in dedicated child OUs using additional GPOs.

Example:

```
Workstations
│
├── EHSL - Workstation Baseline
│
├── Standard
├── IT
├── Developers
├── Kiosk
└── Testing
```

---

## Design Principles

The baseline follows these principles:

- One GPO with one clear purpose.
- Incremental evolution.
- Only settings applicable to every corporate workstation belong here.
- Device-specific configurations are implemented separately.
- Validation is mandatory after every change.

---

## Current Configuration

| Policy | Configuration | Reason |
|----------|--------------|--------|
| Turn off AutoPlay | Enabled (All drives) | Prevent automatic execution from removable media. |
| Set default behavior for AutoRun | Do not execute any autorun commands | Reduce malware execution risk from removable media. |

---

## Validation

The following command verifies that the GPO has been successfully applied:

```cmd
gpresult /scope computer /r
```

Expected result:

```
Applied Group Policy Objects

EHSL - Workstation Baseline
```

---

## Future Roadmap

The following configurations are planned for future versions of this baseline:

- Windows Defender
- Windows Firewall
- PowerShell Logging
- Windows Event Forwarding
- Windows Update
- Security Auditing
- Microsoft Security Baselines
- BitLocker
- LAPS
