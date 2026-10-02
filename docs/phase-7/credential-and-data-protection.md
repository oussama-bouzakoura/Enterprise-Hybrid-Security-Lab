# Phase 7 - Credential and Data Protection

## 1. Purpose

Phase 7 strengthens endpoint and member-server security by protecting two different but related security assets:

- local administrative credentials;
- data stored on managed Windows endpoints.

The phase implements:

- Windows Local Administrator Password Solution (Windows LAPS);
- BitLocker Drive Encryption.

Windows LAPS provides centralized lifecycle management for local Administrator passwords.

BitLocker provides encryption at rest and centralized recovery information through Active Directory Domain Services.

Both technologies are centrally controlled through Group Policy and integrated with the existing `ehsl.internal` Active Directory environment.

---

## 2. Security Objectives

Phase 7 addresses two important enterprise security risks.

### Local Administrator Password Reuse

Using the same local Administrator password across multiple systems can allow compromise of one credential to affect multiple endpoints.

Windows LAPS mitigates this by providing:

- unique local Administrator passwords;
- automatic password rotation;
- centralized password backup;
- controlled password retrieval;
- encrypted password storage.

### Data Exposure

Data stored on a workstation can remain accessible if the operating-system disk is removed or the device is accessed offline.

BitLocker mitigates this risk by encrypting the operating-system volume and protecting access through TPM-backed key protection.

Recovery information is stored centrally in Active Directory.

---

# Part I - Windows LAPS

## 3. Windows LAPS Architecture

EHSL uses the modern Windows LAPS implementation integrated with Active Directory Domain Services.

Password backup directory:

`Active Directory`

Windows LAPS currently protects:

- `EHSL-CLIENT01`
- `EHSL-FS01`

Separate Group Policy Objects are used for workstations and member servers.

This allows the security configuration to evolve independently for each system class.

---

## 4. LAPS Group Policy Objects

The implemented GPOs are:

`EHSL - Windows LAPS - Workstations`

and:

`EHSL - Windows LAPS - Member Servers`

The workstation policy targets systems under the EHSL Workstations OU hierarchy.

The member-server policy targets systems under:

`OU=Servers,OU=EHSL,DC=ehsl,DC=internal`

This separation avoids unnecessarily coupling workstation and server LAPS configuration.

---

## 5. Managed Administrator Account

Windows LAPS manages the built-in local Administrator account.

The account is identified through its built-in RID:

`500`

The Windows LAPS setting:

`Name of administrator account to manage`

is intentionally left:

`Not Configured`

This allows Windows LAPS to manage the built-in Administrator account based on its well-known RID rather than depending on a specific localized or renamed account name.

---

## 6. Password Policy

The implemented Windows LAPS password policy uses:

- password length: 20 characters;
- password age: 30 days;
- password complexity: enabled;
- uppercase characters;
- lowercase characters;
- numbers;
- special characters.

The objective is to generate strong, unique local administrative credentials automatically rather than relying on manually maintained local passwords.

---

## 7. Password Backup

Managed passwords are backed up to:

`Active Directory Domain Services`

This provides centralized recovery and administrative access while keeping password management tied to the domain identity infrastructure.

The password is associated with the corresponding computer object in Active Directory.

Passwords must never be stored manually in EHSL documentation or committed to the Git repository.

---

## 8. Password Encryption

Windows LAPS password encryption is enabled.

This provides additional protection for password information stored in Active Directory.

Because encrypted LAPS passwords require an authorized decryptor, EHSL explicitly configures:

`EHSL\GG_IT_Admins`

as the authorized password decryptor.

This is configured in both:

- `EHSL - Windows LAPS - Workstations`;
- `EHSL - Windows LAPS - Member Servers`.

The relevant policy is:

`Configure authorized password decryptors`

This ensures that the group intended to retrieve managed passwords is also authorized to decrypt the encrypted password data.

---

## 9. Active Directory Preparation

Windows LAPS required Active Directory preparation before policy deployment.

The Windows LAPS schema was extended to support the required LAPS attributes.

The schema extension required a sufficiently privileged administrative context.

During implementation, the delegated administrative account used for normal EHSL administration did not have sufficient privileges to complete the schema operation.

The built-in domain Administrator account was therefore used for the required privileged operation.

This reinforces the distinction between:

- normal delegated administration; and
- operations requiring higher Active Directory privileges.

---

## 10. Computer Self-Permissions

Managed computers require permission to update their own Windows LAPS information in Active Directory.

Self-permissions were configured for the EHSL workstation hierarchy and member-server OU.

The Windows LAPS cmdlet used for this purpose was:

```powershell
Set-LapsADComputerSelfPermission
```

Permissions were applied to the relevant:

- Workstations OU;
- Servers OU.

This allows managed systems to update their own LAPS attributes without granting unnecessary permissions to unrelated identities.

---

## 11. Password Read Permissions

Password retrieval is restricted to authorized administrators.

EHSL grants LAPS password-read permissions to:

`EHSL\GG_IT_Admins`

for both:

- managed workstations;
- member servers.

The configuration was applied using:

```powershell
Set-LapsADReadPasswordPermission
```

This separates the ability to administer systems from general domain-user access.

---

## 12. Extended Rights Review

Windows LAPS permissions were reviewed using:

```powershell
Find-LapsADExtendedRights
```

This was used during implementation to understand which principals had extended rights over the relevant Active Directory locations.

The review formed part of the validation process before finalizing delegated LAPS access.

---

## 13. Password Retrieval

Authorized administrators can retrieve a managed password using Windows LAPS tooling.

Example:

```powershell
Get-LapsADPassword -Identity EHSL-CLIENT01
```

Where plaintext retrieval is explicitly required for an authorized administrative operation:

```powershell
Get-LapsADPassword -Identity EHSL-CLIENT01 -AsPlainText
```

Plaintext password output must be treated as sensitive.

It must not be:

- copied into documentation;
- stored in repository files;
- included in screenshots intended for publication;
- committed to Git.

---

## 14. LAPS Positive Validation

Windows LAPS was successfully validated against both managed system classes.

Distinct managed passwords were successfully retrieved for:

- EHSL-CLIENT01;
- EHSL-FS01.

This confirmed that the systems were independently managing their local Administrator credentials rather than sharing a common local password.

An authorized user belonging to:

`GG_IT_Admins`

was able to retrieve the managed password.

This validated the intended delegated-access model.

---

## 15. LAPS Negative Validation

A standard domain user without the required LAPS permissions was used for negative testing.

The standard user was unable to retrieve the managed LAPS password.

This validated that password access was not available to normal domain users.

Positive and negative validation together confirmed that the intended authorization boundary was functioning.

---

## 16. LAPS Security Model

The resulting LAPS security path is:

`Managed Windows system`

↓

`Unique local Administrator password`

↓

`Encrypted LAPS information in Active Directory`

↓

`Authorized decryptor`

↓

`EHSL\GG_IT_Admins`

This provides centralized management without returning to a shared local-administrator credential model.

---

# Part II - BitLocker

## 17. BitLocker Scope

BitLocker is currently implemented and validated on:

`EHSL-CLIENT01`

BitLocker has not been implemented on EHSL-FS01 as part of the current project state.

This distinction is intentional and must remain explicit in the documentation.

---

## 18. BitLocker Group Policy

The implemented GPO is:

`EHSL - BitLocker - Workstations`

The policy targets managed workstation systems through the EHSL Workstations OU hierarchy.

The current implementation focuses on operating-system drive protection.

---

## 19. Hardware Security

EHSL-CLIENT01 has a functional TPM.

Validated TPM state:

- TPM present: True
- TPM ready: True
- TPM enabled: True
- TPM activated: True

The environment provides TPM 2.0 capability for the workstation.

BitLocker uses the TPM to protect access to the operating-system encryption key.

---

## 20. Operating-System Drive State

The C: volume on EHSL-CLIENT01 is fully encrypted.

Validated state:

- volume: `C:`
- volume status: FullyEncrypted
- encryption percentage: 100%
- encryption method: XTS-AES 128
- protection status: On

The workstation therefore has active encryption-at-rest protection on its operating-system volume.

---

## 21. Key Protectors

EHSL-CLIENT01 uses:

- TPM protector;
- Recovery Password protector.

The TPM protector supports normal protected startup.

The Recovery Password provides a recovery mechanism if normal TPM-based unlock is unavailable.

Actual recovery passwords are sensitive security material and must never be stored in project documentation or committed to Git.

---

## 22. TPM Startup Policy

The workstation BitLocker policy uses TPM-based startup protection.

The implemented policy does not require:

- startup PIN;
- startup key;
- startup key and PIN.

The current lab design therefore uses TPM-only protection for normal operating-system startup.

This configuration provides a practical enterprise endpoint baseline while allowing future hardening to evaluate stronger pre-boot authentication if required.

---

## 23. Active Directory Recovery Escrow

BitLocker recovery information is backed up to Active Directory Domain Services.

The policy requires recovery information to be stored in AD DS.

The configuration includes storage of:

- recovery passwords;
- key packages.

The policy is configured so BitLocker should not be enabled through the managed deployment process unless the required recovery information can be stored in Active Directory.

This provides centralized recovery capability for domain-managed endpoints.

---

## 24. Existing Encryption State

An important implementation detail is that EHSL-CLIENT01 was already encrypted before the final EHSL BitLocker Group Policy configuration was introduced.

The project therefore did not initially encrypt a plaintext workstation through the GPO.

Instead, the implementation process:

1. inspected the existing BitLocker state;
2. introduced the enterprise BitLocker policy;
3. validated the existing TPM and recovery protectors;
4. validated AD DS recovery escrow;
5. tested recovery-password rotation;
6. confirmed the final protected state.

This distinction is preserved so the documentation accurately describes what was actually implemented and tested.

---

## 25. Recovery Information Validation

The BitLocker recovery object was verified under the EHSL-CLIENT01 computer object in Active Directory.

This confirmed that recovery information was available through the centralized domain infrastructure.

The validation demonstrates the recovery path:

`EHSL-CLIENT01`

↓

`BitLocker Recovery Password protector`

↓

`Active Directory computer object`

↓

`Authorized administrative recovery`

---

## 26. Recovery Password Rotation

During implementation, the existing Recovery Password protector required rotation.

A controlled rotation was performed.

The workflow was:

1. create a new Recovery Password protector;
2. back up the new recovery protector to Active Directory;
3. verify the new protector;
4. remove the previous Recovery Password protector from the endpoint;
5. confirm that BitLocker protection remained operational.

This demonstrated that BitLocker recovery material can be rotated without decrypting the operating-system volume.

---

## 27. Historical Recovery Objects

Active Directory may retain historical BitLocker recovery objects even after a corresponding protector has been removed from the endpoint.

The existence of an older AD recovery object therefore does not necessarily mean that the old recovery password remains an active protector on the workstation.

The authoritative endpoint state must also be reviewed when validating active protectors.

---

## 28. BitLocker Verification

Current BitLocker state can be reviewed on EHSL-CLIENT01 with:

```powershell
Get-BitLockerVolume -MountPoint C:
```

The traditional command-line utility can also be used:

```powershell
manage-bde -status C:
```

Protectors can be reviewed with:

```powershell
manage-bde -protectors -get C:
```

These commands can expose sensitive recovery information depending on the command and environment.

Output must therefore be reviewed before being copied into public documentation.

---

## 29. Active Directory Recovery Backup

A Recovery Password protector can be backed up to Active Directory using the appropriate BitLocker PowerShell tooling.

The protector identifier must correspond to the intended active Recovery Password protector.

Recovery operations should always be verified before an older protector is removed.

The safe sequence is:

`Create -> Escrow -> Verify -> Remove old protector`

rather than removing existing recovery capability before validating the replacement.

---

# Part III - Combined Security Architecture

## 30. Relationship Between LAPS and BitLocker

Windows LAPS and BitLocker protect different layers of the endpoint.

### Windows LAPS

Protects:

`Local administrative credentials`

### BitLocker

Protects:

`Data at rest`

Together they reduce two different risks:

- reuse or compromise of local Administrator credentials;
- offline access to workstation data.

Neither technology replaces the other.

---

## 31. Active Directory as the Security Control Plane

Both technologies integrate with Active Directory.

Windows LAPS uses Active Directory for:

- password backup;
- authorization;
- encrypted password storage;
- administrative retrieval.

BitLocker uses Active Directory for:

- centralized recovery-information escrow.

This reinforces the role of the EHSL identity infrastructure as a central security control plane.

It also increases the importance of protecting privileged Active Directory access.

---

## 32. Privileged Access Considerations

LAPS and BitLocker recovery information are sensitive administrative assets.

Access should follow least-privilege principles.

Normal domain users should not have access to:

- LAPS passwords;
- BitLocker recovery information;
- privileged Active Directory configuration.

The EHSL administrative model therefore separates standard users from privileged administrative identities and groups.

---

## 33. Sensitive Data Handling

The following information must never be committed to the public EHSL repository:

- LAPS passwords;
- BitLocker Recovery Passwords;
- private credentials;
- authentication tokens;
- secrets;
- private keys.

Screenshots and command output must be reviewed before publication.

If sensitive information is exposed during testing, the affected credential or recovery protector should be rotated rather than merely removed from documentation.

---

## 34. Validation Summary

### Windows LAPS

Validated:

- Active Directory schema support;
- computer self-permissions;
- workstation policy;
- member-server policy;
- encrypted password storage;
- 20-character password policy;
- 30-day password age;
- authorized decryptor configuration;
- delegated read access through `GG_IT_Admins`;
- successful retrieval by an authorized administrator;
- failed retrieval by a standard user;
- independent passwords for EHSL-CLIENT01 and EHSL-FS01.

### BitLocker

Validated on EHSL-CLIENT01:

- TPM available and operational;
- C: fully encrypted;
- XTS-AES 128;
- protection enabled;
- TPM protector;
- Recovery Password protector;
- Active Directory recovery escrow;
- recovery object validation;
- controlled Recovery Password rotation;
- removal of the previous endpoint protector after successful replacement.

---

## 35. Operational Verification

### LAPS

Authorized administrators can review LAPS information with:

```powershell
Get-LapsADPassword -Identity EHSL-CLIENT01
```

or:

```powershell
Get-LapsADPassword -Identity EHSL-FS01
```

Permissions can be reviewed with:

```powershell
Find-LapsADExtendedRights
```

### BitLocker

On EHSL-CLIENT01:

```powershell
Get-BitLockerVolume -MountPoint C:
```

and:

```powershell
manage-bde -status C:
```

can be used to review the current encryption state.

Sensitive output must not be committed to the repository.

---

## 36. Current Limitations

The current implementation intentionally has a limited scope.

### BitLocker

BitLocker is currently validated only on:

`EHSL-CLIENT01`

EHSL-FS01 is not currently included in the BitLocker deployment.

### LAPS

Windows LAPS is currently validated against the existing workstation and member-server systems.

The lab does not currently contain multiple systems of each class for large-scale deployment testing.

### High Availability

The Active Directory infrastructure currently contains a single domain controller.

This is sufficient for the lab but does not provide production-style identity-service redundancy.

---

## 37. Future Improvements

Potential future improvements include:

- evaluate BitLocker for appropriate server workloads;
- review stronger BitLocker startup authentication where justified;
- expand LAPS validation as additional endpoints are introduced;
- automate security-state validation with PowerShell;
- integrate endpoint-security state with future monitoring capabilities.

These are future improvements and are not represented as currently implemented controls.

Windows Firewall and Microsoft Defender hardening are separate subsequent security phases.

---

## 38. Phase 7 Status

**Status: Completed**

Phase 7 establishes validated credential and data protection using:

- Windows LAPS for local Administrator password management;
- Active Directory-based LAPS password backup;
- encrypted LAPS password storage;
- delegated LAPS retrieval;
- BitLocker on EHSL-CLIENT01;
- TPM-backed operating-system protection;
- Active Directory BitLocker recovery escrow;
- validated Recovery Password rotation.

The resulting security model provides:

`Unique local administrative credentials + encrypted endpoint storage + centralized enterprise recovery`

Windows Firewall hardening is the next planned host-security stage.
