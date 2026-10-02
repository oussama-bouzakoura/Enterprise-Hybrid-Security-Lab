# PowerShell Automation

This directory contains or will contain PowerShell automation developed for the Enterprise Hybrid Security Lab.

PowerShell is the primary scripting and administrative language for the current Microsoft-based EHSL environment.

---

## Current Status

No reusable PowerShell script has been published in this directory yet.

PowerShell has already been used extensively during the implementation of EHSL for interactive administration, validation, and troubleshooting.

Reusable scripts will be added when those workflows are consolidated into repository-quality automation.

---

## Current PowerShell Use

PowerShell has been used throughout the lab for areas including:

- Active Directory administration;
- user and group management;
- Organizational Unit management;
- Windows LAPS deployment and validation;
- BitLocker inspection and validation;
- DHCP inspection;
- Windows feature inspection;
- networking validation;
- Group Policy troubleshooting;
- Windows Event Forwarding troubleshooting;
- Windows Event Log queries;
- infrastructure inventory.

The absence of `.ps1` files in this directory does not mean PowerShell is absent from the project.

It means the current repository distinguishes between interactive implementation commands and reusable automation.

---

## Planned Automation

Useful future scripts may include:

### Infrastructure Inventory

Collect information such as:

- hostname;
- operating system;
- CPU;
- memory;
- disks;
- network interfaces;
- IP configuration;
- installed server roles.

### Active Directory Validation

Validate:

- required OUs;
- security groups;
- computer placement;
- administrative identities;
- group membership.

### Windows LAPS Validation

Review:

- managed systems;
- password state;
- Active Directory permissions;
- authorized retrieval configuration.

Sensitive passwords must never be written to repository output.

### BitLocker Validation

Review:

- encryption status;
- encryption method;
- protection status;
- TPM state;
- protector types;
- Active Directory escrow state.

Recovery Password values must never be written to repository output.

### WEF Validation

Review:

- WEC service state;
- subscriptions;
- source runtime state;
- recent forwarded events;
- selected security event IDs.

### Security Baseline Validation

Future scripts may validate:

- Windows Firewall;
- Microsoft Defender;
- Group Policy application;
- other endpoint-security controls.

---

## Script Naming

PowerShell scripts should use descriptive names and follow standard PowerShell `Verb-Noun` naming where practical.

Examples:

`Get-EHSLInventory.ps1`

`Test-EHSLWEF.ps1`

`Test-EHSLLapsState.ps1`

`Test-EHSLBitLockerState.ps1`

These filenames are examples of the intended convention and do not indicate that the scripts currently exist.

---

## Execution Context

Each script must document where it is intended to run.

Examples include:

- EHSL-DC01;
- EHSL-FS01;
- EHSL-CLIENT01;
- management host.

The documentation should also specify whether execution requires:

- standard user rights;
- local Administrator;
- delegated domain administration;
- Domain Administrator;
- another privileged context.

Scripts must not assume that all EHSL administrative accounts have identical privileges.

---

## Security Requirements

Scripts must not contain or persist:

- passwords;
- LAPS credentials;
- BitLocker Recovery Passwords;
- tokens;
- API keys;
- private keys;
- other secrets.

Commands that can expose sensitive values should be used deliberately and their output must be reviewed before publication.

---

## Development Standard

A reusable PowerShell script should provide:

1. a clear purpose;
2. prerequisites;
3. required execution context;
4. predictable parameters;
5. error handling;
6. validation of important results;
7. readable output;
8. safe handling of sensitive information.

Where practical, scripts that modify infrastructure should be idempotent.

---

## Repository Principle

EHSL will not add PowerShell scripts solely to make the repository appear more automated.

Automation should represent a real operational workflow that has already been understood and validated.
