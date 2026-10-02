# EHSL Automation

This directory is reserved for automation developed as part of the Enterprise Hybrid Security Lab.

EHSL currently prioritizes manual implementation and validation of infrastructure and security controls before automating them.

This ensures that automation is built around understood and validated processes rather than hiding configuration steps that have not been technically verified.

---

## Current Status

The repository does not currently contain a production automation toolkit.

The scripting directories are retained as the foundation for future automation.

Current scripting focus:

- PowerShell for Windows and Active Directory administration;
- Python for future cross-platform utilities, validation, reporting, or data processing.

Bash is not currently maintained as a project scripting area because the implemented EHSL architecture does not contain Linux infrastructure.

---

## Directory Structure

```text
scripts/
|
+-- powershell/
|   +-- README.md
|
+-- python/
    +-- README.md
```

---

## PowerShell

PowerShell is the primary automation language for the current Microsoft-based environment.

Potential automation areas include:

- Active Directory administration;
- infrastructure inventory;
- Group Policy validation;
- Windows LAPS validation;
- BitLocker validation;
- DHCP inspection;
- Windows Event Forwarding validation;
- Windows Firewall validation;
- Microsoft Defender validation;
- security-state reporting.

PowerShell scripts will be added when a repeatable administrative or validation workflow provides enough value to justify automation.

---

## Python

Python is reserved for automation that benefits from a general-purpose language rather than direct Windows administration.

Potential future uses include:

- report generation;
- configuration analysis;
- log processing;
- security-data transformation;
- validation tooling;
- integration with future APIs or cloud services.

No Python-based EHSL infrastructure component is currently deployed.

---

## Development Principles

EHSL automation should follow several principles.

### Understand Before Automating

A process should be implemented and validated manually before it is automated.

### Idempotence Where Practical

Scripts that modify infrastructure should be designed to avoid unnecessary or destructive changes when executed repeatedly.

### Clear Scope

Scripts should state:

- their purpose;
- required privileges;
- intended execution system;
- prerequisites;
- expected output.

### Validation

Automation should verify important results rather than assuming that a command completed the intended configuration.

### Error Handling

Operational scripts should provide useful errors when required dependencies, privileges, objects, or configuration are missing.

---

## Security

Scripts must not contain hard-coded:

- passwords;
- LAPS credentials;
- BitLocker Recovery Passwords;
- API keys;
- tokens;
- private keys;
- other secrets.

Sensitive values should be handled using an appropriate secure mechanism when automation requiring them is introduced.

---

## Naming

PowerShell scripts should prefer descriptive `Verb-Noun.ps1` naming where practical.

Examples:

`Get-EHSLInventory.ps1`

`Test-EHSLConnectivity.ps1`

Python scripts should also use descriptive filenames that communicate their purpose.

Example:

`analyze_security_events.py`

These examples define the intended naming approach and do not indicate that the scripts currently exist.

---

## Documentation

Each implemented script should document:

1. purpose;
2. prerequisites;
3. execution context;
4. required privileges;
5. parameters;
6. example usage;
7. expected result.

The documentation should make it possible to understand the script without reading every implementation detail first.

---

## Roadmap

Automation will be introduced progressively as EHSL grows.

The priority is to automate tasks that are:

- repetitive;
- error-prone;
- useful for validation;
- useful for security reporting;
- representative of real infrastructure engineering workflows.

The scripting area should evolve alongside the implemented environment rather than being populated with placeholder automation.
