# Python Automation

This directory is reserved for Python tooling developed for the Enterprise Hybrid Security Lab.

Python is not currently used to provide a core EHSL infrastructure service.

Its intended role is to complement PowerShell where general-purpose processing, reporting, analysis, or future integrations are more appropriate.

---

## Current Status

No reusable Python script has been published in this directory yet.

The directory is retained because Python is part of the planned EHSL automation strategy.

This should not be interpreted as an implemented Python-based infrastructure component.

---

## Potential Use Cases

Future Python tooling may support:

### Security Event Analysis

Processing exported or collected security telemetry for:

- filtering;
- aggregation;
- normalization;
- reporting;
- detection-development exercises.

### Reporting

Generating structured reports from infrastructure or security data.

### Configuration Analysis

Parsing configuration exports and identifying:

- expected settings;
- deviations;
- missing controls.

### API Integration

Future cloud or security-platform phases may introduce APIs where Python provides a practical integration method.

### Data Transformation

Python may be used to convert security or infrastructure data between formats required by future tools.

---

## Relationship to PowerShell

PowerShell remains the preferred language for direct administration of the current Windows and Active Directory environment.

Python should be used when it provides a clear technical advantage rather than duplicating functionality that is better handled through native PowerShell tooling.

---

## Naming

Python scripts should use descriptive lowercase filenames.

Example:

`analyze_security_events.py`

This is a naming example only and does not indicate that the script currently exists.

---

## Security Requirements

Python tooling must not contain hard-coded:

- passwords;
- LAPS credentials;
- BitLocker Recovery Passwords;
- API keys;
- authentication tokens;
- private keys;
- secrets.

Sensitive input and output must be handled appropriately for the technology being integrated.

---

## Documentation Requirements

Each implemented Python utility should document:

- purpose;
- Python requirements;
- dependencies;
- input;
- output;
- execution example;
- security considerations.

Dependency files should only be introduced when actual Python tooling requires them.

---

## Repository Principle

Python tooling will be added when it supports a real EHSL engineering or security workflow.

Placeholder scripts will not be added solely to populate the directory.
