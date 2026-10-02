# Configuration Artifacts

This directory is reserved for reusable configuration artifacts exported or created as part of the Enterprise Hybrid Security Lab.

The main project documentation remains under:

`/docs`

Configuration artifacts stored here should complement the documentation rather than replace it.

---

## Purpose

As EHSL evolves, this directory may contain sanitized and reusable examples such as:

- Windows configuration exports;
- Group Policy-related configuration references;
- Windows Event Forwarding subscription definitions;
- security-policy configuration;
- Microsoft Defender configuration;
- Windows Firewall configuration;
- other infrastructure configuration artifacts.

Only artifacts that provide practical value to the project should be added.

---

## Current Status

No configuration artifact is currently required here for the implemented phases.

The directory is retained intentionally for future reusable configuration material.

The absence of configuration files does not mean the corresponding EHSL technologies are undocumented.

Current implementation details are maintained under:

`/docs`

---

## Repository Safety

Configuration artifacts must be reviewed before publication.

Do not commit:

- passwords;
- LAPS credentials;
- BitLocker Recovery Passwords;
- private keys;
- API keys;
- authentication tokens;
- secrets;
- sensitive environment-specific credentials.

Where a configuration export contains unnecessary environment-specific data, it should be sanitized before being added to the repository.

---

## Naming

Configuration filenames should clearly identify:

- the technology;
- the purpose;
- the target where relevant.

Avoid ambiguous filenames such as:

`config1.xml`

or:

`settings.txt`

Prefer descriptive names that remain understandable without requiring additional context.

---

## Future Use

Likely future uses of this directory include reusable artifacts produced during:

- Windows Firewall hardening;
- Microsoft Defender hardening;
- additional Windows Event Forwarding work;
- future cloud or hybrid security phases.

Artifacts should only be added after the corresponding technology is actually implemented.
