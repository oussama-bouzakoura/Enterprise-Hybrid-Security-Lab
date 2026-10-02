@'
# Phase 3 - Workstation Group Policy Baseline

## 1. Purpose

This phase establishes the initial centralized security baseline for Windows workstations in the Enterprise Hybrid Security Lab (EHSL).

The baseline demonstrates how Active Directory Group Policy can be used to apply consistent security configuration to managed endpoints.

The workstation baseline has evolved since its initial deployment. This document reflects the current implemented state while maintaining separation between baseline settings and security technologies managed through dedicated GPOs.

---

## 2. Policy Architecture

The primary workstation baseline GPO is:

`EHSL - Workstation Baseline`

It is linked to:

`OU=Workstations,OU=EHSL,DC=ehsl,DC=internal`

This allows the baseline to apply to workstation computer objects while remaining separate from member-server and domain-controller policy.

Current workstation sub-OUs include:

- Standard
- IT
- Kiosk
- Developers
- Testing

EHSL-CLIENT01 is currently located at:

`CN=EHSL-CLIENT01,OU=Standard,OU=Workstations,OU=EHSL,DC=ehsl,DC=internal`

Because the baseline is linked at the Workstations OU level, child workstation OUs inherit the policy unless inheritance or filtering is intentionally changed.

---

## 3. Baseline Design Principles

The EHSL Group Policy design follows several principles:

### Centralized Configuration

Security settings should be centrally managed where practical rather than configured manually on individual endpoints.

### Role Separation

Workstation, member-server, and domain-controller baselines are maintained separately.

Current baseline GPOs include:

- `EHSL - Workstation Baseline`
- `EHSL - Member Servers Baseline`
- `EHSL - Domain Controllers Baseline`

### Technology Separation

Security technologies that require their own lifecycle or targeting are maintained through dedicated GPOs.

Examples include:

- Windows Event Forwarding
- Windows LAPS
- BitLocker

This avoids turning the workstation baseline into a single monolithic policy containing unrelated controls.

### Validation

Policy deployment is verified on target systems rather than assuming that successful GPO creation means successful enforcement.

---

# 4. Workstation Baseline Controls

## 4.1 AutoPlay

AutoPlay is disabled through Group Policy.

The objective is to reduce automatic interaction with removable or externally supplied media.

This helps reduce exposure to scenarios in which untrusted media automatically triggers content or encourages unsafe execution paths.

---

## 4.2 AutoRun

AutoRun functionality is disabled through Group Policy.

This complements the AutoPlay restriction and reduces the ability of removable media to influence automatic execution behavior.

Together, the AutoPlay and AutoRun controls provide a basic removable-media hardening measure.

---

# 5. Security Auditing

The EHSL workstation security configuration was subsequently expanded to provide detailed Windows security telemetry.

Advanced Audit Policy is used to generate security-relevant events required by the monitoring architecture.

Auditing is centrally managed through Group Policy rather than configured independently on each endpoint.

The auditing design supports telemetry including:

- authentication activity;
- account activity;
- privilege use;
- process creation;
- Kerberos activity;
- audit-policy changes;
- other security-relevant Windows activity.

The complete centralized monitoring architecture is documented in:

`docs/phase-6/security-monitoring.md`

---

# 6. Process Creation Auditing

Process creation auditing is enabled to generate Windows Security Event ID:

`4688`

This provides visibility into process execution on managed Windows systems.

Process creation events are valuable for:

- security investigations;
- suspicious execution analysis;
- PowerShell activity review;
- malware investigation;
- parent/child process analysis;
- future detection engineering.

---

# 7. Command-Line Process Auditing

EHSL also enables inclusion of process command-line information in process creation events.

This increases the investigative value of Event ID `4688` by providing additional execution context.

Command-line visibility can expose sensitive information if applications place credentials or secrets directly in process arguments.

For that reason, command-line telemetry must be treated as security-sensitive log data.

---

# 8. Windows Event Forwarding Prerequisites

During Phase 6, additional configuration became necessary on WEF source systems to support access to the Windows Security log in the implemented EHSL configuration.

The workstation and member-server baselines were used to provide the required local security configuration.

The implemented source configuration includes:

- `NETWORK SERVICE` membership in the local `Event Log Readers` group through Restricted Groups;
- `NETWORK SERVICE` assignment of the `SeSecurityPrivilege` user right (`Manage auditing and security log`).

These settings were introduced as part of troubleshooting Windows Event Forwarding Error 5004 in the EHSL environment.

After policy application and system restart, the affected WEF sources successfully reached:

`Active / LastError = 0`

This should be understood as an implementation finding from the current EHSL configuration rather than a claim that every Windows Event Forwarding architecture universally requires the same configuration.

The complete troubleshooting history is documented in Phase 6.

---

# 9. Related Workstation GPOs

The workstation security architecture now contains multiple GPOs with different responsibilities.

## EHSL - Workstation Baseline

Provides general workstation hardening and baseline configuration.

Current responsibilities include:

- AutoPlay restriction;
- AutoRun restriction;
- security-auditing-related configuration;
- WEF Security-log access prerequisites used by the current lab implementation.

## EHSL - Windows Event Forwarding

Provides centralized Windows Event Forwarding configuration.

Its purpose is to configure domain systems as WEF sources for EHSL-DC01.

## EHSL - Windows LAPS - Workstations

Provides Windows LAPS configuration for managed workstations.

Its purpose is to centrally manage and protect the local Administrator password.

## EHSL - BitLocker - Workstations

Provides BitLocker policy for managed workstations.

Its purpose is to establish centralized disk-encryption and recovery requirements.

Keeping these policies separate improves:

- troubleshooting;
- targeting;
- policy ownership;
- rollback;
- future maintenance.

---

# 10. Member Server Baseline

EHSL also maintains:

`EHSL - Member Servers Baseline`

This GPO is targeted at:

`OU=Servers,OU=EHSL,DC=ehsl,DC=internal`

EHSL-FS01 receives the member-server baseline.

The member-server baseline is maintained separately because server security requirements and operational constraints can differ from workstation requirements.

It also contains the WEF Security-log access prerequisites required by the current EHSL monitoring implementation.

---

# 11. Domain Controller Baseline

Domain controllers use:

`EHSL - Domain Controllers Baseline`

This policy is linked to the Active Directory Domain Controllers OU rather than the custom EHSL Workstations or Servers OUs.

Separating domain-controller policy reduces the risk of applying workstation or member-server configuration indiscriminately to the identity infrastructure.

---

# 12. Policy Validation

Group Policy should be validated on the target endpoint after configuration changes.

Useful commands include:

```powershell
gpupdate /force
```

and:

```powershell
gpresult /r
```

For a more detailed report:

```powershell
gpresult /h C:\Windows\Temp\gpresult.html
```

The generated report can be reviewed to confirm:

- applied GPOs;
- denied GPOs;
- security filtering;
- policy inheritance;
- effective configuration.

---

# 13. EHSL-CLIENT01 Validation

EHSL-CLIENT01 is the primary managed workstation used to validate workstation policy.

Current validated capabilities on the endpoint include:

- domain membership;
- placement under the Workstations OU hierarchy;
- workstation baseline application;
- Advanced Audit Policy;
- process creation auditing;
- command-line process auditing;
- Windows Event Forwarding;
- Windows LAPS;
- BitLocker.

The endpoint therefore provides the primary validation platform for the EHSL Windows workstation security architecture.

---

# 14. Policy Scope and Separation

Not every workstation security control belongs in the baseline GPO.

The current architecture deliberately separates major technologies.

| GPO | Primary Purpose |
|---|---|
| `EHSL - Workstation Baseline` | General workstation security baseline |
| `EHSL - Windows Event Forwarding` | Centralized event forwarding |
| `EHSL - Windows LAPS - Workstations` | Local Administrator password management |
| `EHSL - BitLocker - Workstations` | Operating-system drive encryption |

This structure makes the source of an effective setting easier to identify and reduces unnecessary coupling between security controls.

---

# 15. Security Controls Not Yet Implemented

The following endpoint-hardening areas remain planned:

- centralized Windows Firewall hardening;
- Microsoft Defender hardening.

These controls are not considered part of the implemented workstation baseline until they have been configured and validated.

They should be introduced through appropriate policy architecture rather than added to documentation before deployment.

---

# 16. Future Baseline Evolution

The workstation baseline may evolve as additional endpoint-security requirements are introduced.

Potential future improvements include:

- additional attack-surface reduction controls;
- enhanced credential protections;
- PowerShell security configuration;
- additional Windows security options;
- firewall policy;
- Microsoft Defender configuration.

Any future setting should be tested for:

- security benefit;
- operational impact;
- policy conflicts;
- compatibility with existing EHSL services.

---

# 17. Operational Considerations

Group Policy changes can affect authentication, networking, logging, remote administration, and endpoint usability.

Changes should therefore follow a controlled workflow:

1. define the security objective;
2. identify the correct GPO and target OU;
3. configure the minimum required setting;
4. update Group Policy on the target;
5. verify effective policy;
6. validate the intended security behavior;
7. confirm that required enterprise functionality remains operational;
8. document the final state.

This workflow is especially important for upcoming firewall and Microsoft Defender hardening.

---

# 18. Phase 3 Status

**Status: Completed**

Validated workstation policy capabilities include:

- centralized Group Policy management;
- dedicated workstation baseline;
- AutoPlay restriction;
- AutoRun restriction;
- security auditing;
- process creation auditing;
- command-line process auditing;
- WEF-related Security-log access configuration used by the current lab;
- separation of LAPS, BitLocker, and WEF into dedicated policies.

Additional endpoint security technologies are developed as subsequent security-hardening work rather than being represented as unfinished Phase 3 requirements.
'@ | Set-Content -Path ".\docs\phase-3\workstation-gpo-baseline.md" -Encoding UTF8