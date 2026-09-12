# Phase 6 - Security Monitoring

## Objective

Establish a Windows security telemetry foundation by implementing Advanced Audit Policy and centralized Windows Event Forwarding (WEF).

The objective is to improve visibility across domain controllers, member servers and workstations while preparing the environment for future SIEM integration.

---

## Audit Policy Architecture

EHSL uses separate security baselines for different system roles rather than placing all audit configuration in the default domain policies.

The following GPOs are used:

```text
EHSL - Workstation Baseline
EHSL - Member Servers Baseline
EHSL - Domain Controllers Baseline
```

This allows audit requirements to be adapted to the role and security relevance of each system.

The following setting is also enabled to ensure Advanced Audit Policy subcategories take precedence over legacy audit categories:

```text
Audit: Force audit policy subcategory settings
to override audit policy category settings
```

---

## Member Server Audit Baseline

GPO:

```text
EHSL - Member Servers Baseline
```

Target:

```text
OU=Servers,OU=EHSL
```

The following Advanced Audit Policy subcategories are configured:

| Category | Subcategory | Auditing |
|---|---|---|
| Account Logon | Credential Validation | Success + Failure |
| Account Management | User Account Management | Success + Failure |
| Account Management | Security Group Management | Success + Failure |
| Logon/Logoff | Logon | Success + Failure |
| Logon/Logoff | Logoff | Success |
| Logon/Logoff | Special Logon | Success |
| Object Access | File System | Success + Failure |
| Policy Change | Audit Policy Change | Success + Failure |
| Privilege Use | Sensitive Privilege Use | Success + Failure |
| Detailed Tracking | Process Creation | Success |
| System | Security System Extension | Success + Failure |
| System | System Integrity | Success + Failure |

Process Creation auditing also includes:

```text
Include command line in process creation events: Enabled
```

This provides additional context for Event ID `4688`.

---

## Domain Controller Audit Baseline

GPO:

```text
EHSL - Domain Controllers Baseline
```

Target:

```text
OU=Domain Controllers
```

The Domain Controller baseline includes:

| Category | Subcategory | Auditing |
|---|---|---|
| Account Logon | Credential Validation | Success + Failure |
| Account Logon | Kerberos Authentication Service | Success + Failure |
| Account Logon | Kerberos Service Ticket Operations | Success + Failure |
| Account Management | User Account Management | Success + Failure |
| Account Management | Security Group Management | Success + Failure |
| Account Management | Computer Account Management | Success + Failure |
| Logon/Logoff | Logon | Success + Failure |
| Logon/Logoff | Logoff | Success |
| Logon/Logoff | Special Logon | Success |
| DS Access | Directory Service Changes | Success + Failure |
| Policy Change | Audit Policy Change | Success + Failure |
| Privilege Use | Sensitive Privilege Use | Success + Failure |
| Detailed Tracking | Process Creation | Success |
| System | Security System Extension | Success + Failure |
| System | System Integrity | Success + Failure |

Command-line information for Process Creation events is enabled.

The additional Kerberos and Directory Service auditing reflects the security relevance of a Domain Controller.

---

## Workstation Audit Baseline

The existing workstation baseline was extended with Advanced Audit Policy.

Configured subcategories include:

| Category | Subcategory | Auditing |
|---|---|---|
| Account Logon | Credential Validation | Success + Failure |
| Account Management | User Account Management | Success + Failure |
| Account Management | Security Group Management | Success + Failure |
| Logon/Logoff | Logon | Success + Failure |
| Logon/Logoff | Logoff | Success |
| Logon/Logoff | Special Logon | Success |
| Policy Change | Audit Policy Change | Success + Failure |
| Privilege Use | Sensitive Privilege Use | Success + Failure |
| Detailed Tracking | Process Creation | Success |
| System | Security System Extension | Success + Failure |
| System | System Integrity | Success + Failure |

Command-line information for Process Creation events is enabled.

File System auditing is intentionally not enabled on workstations at this stage because centralized business data is hosted on EHSL-FS01.

---

## Audit Policy Validation

Audit configuration was validated using multiple layers:

```powershell
gpresult /r /scope computer
auditpol /get /category:*
Get-GPOReport
```

`gpresult` confirms that the intended GPO reaches the computer.

`auditpol` confirms the effective Advanced Audit Policy on the endpoint.

`Get-GPOReport` confirms that the settings are actually stored inside the GPO.

---

## Troubleshooting Finding - GPO Storage

During implementation, several Advanced Audit Policy settings appeared configured in the Group Policy editor but did not appear in `Get-GPOReport`.

Although the GPO itself was applied, the affected audit settings were therefore not actually stored.

The affected subcategories were reopened and explicitly configured using:

```text
Configure the following audit events
Success and/or Failure
Apply
OK
```

Afterwards, `Get-GPOReport` correctly displayed the settings and `auditpol` confirmed their effective application.

This established an important validation principle for the lab:

```text
GPO linked/applied
        ≠
setting necessarily stored and effective
```

Configuration should be validated at both the GPO and endpoint levels.

---

## Process Creation Validation

Process Creation auditing was successfully validated using:

```text
Event ID 4688
```

Validation on EHSL-FS01 and EHSL-DC01 confirmed that process events include full command-line information.

This telemetry provides useful context for detecting suspicious command execution, PowerShell activity and other process-based behavior.

---

# Windows Event Forwarding

## Architecture

Windows Event Forwarding is being introduced to centralize selected security events.

Current architecture:

```text
EHSL-FS01 ───────┐
                 │
                 ├── WEF / WinRM / HTTP 5985
                 │
EHSL-CLIENT01 ───┘
                         ↓
                    EHSL-DC01
                         ↓
                  Windows Event
                     Collector
                         ↓
                  Forwarded Events
```

EHSL-DC01 currently performs the Windows Event Collector role.

This design is acceptable for the current lab because of resource constraints.

In a production environment, the collector role should be evaluated for separation from the Domain Controller depending on scale, availability and security requirements.

---

## Windows Event Collector

The collector was initialized on EHSL-DC01 using:

```powershell
wecutil qc
```

Windows Event Collector is running and the `ForwardedEvents` log is available.

---

## WEF Group Policy

GPO:

```text
EHSL - Windows Event Forwarding
```

The GPO is linked to:

```text
OU=EHSL
```

It therefore currently reaches EHSL member servers and workstations.

The Domain Controller OU is outside this scope.

The configured Subscription Manager is:

```text
Server=http://EHSL-DC01.ehsl.internal:5985/wsman/SubscriptionManager/WEC,Refresh=60
```

The configuration was verified on EHSL-CLIENT01 through:

```text
HKLM\SOFTWARE\Policies\Microsoft\Windows\EventLog\EventForwarding\SubscriptionManager
```

---

## Source-Initiated Subscription

Subscription:

```text
EHSL - Security Events
```

Configuration:

| Property | Value |
|---|---|
| Subscription Type | Source Initiated |
| Destination Log | Forwarded Events |
| Allowed Sources | Domain Computers |
| Transport | HTTP |
| Port | 5985 |
| Configuration Mode | Minimize Latency |
| Content Format | RenderedText |

Source-initiated forwarding allows domain systems that match the authorization policy to register with the collector without manually defining each endpoint in the subscription.

---

## Selected Security Events

The current subscription includes the following Event IDs:

```text
4624
4625
4634
4648
4672
4688
4720
4722
4725
4726
4728
4729
4732
4733
4740
4768
4769
4771
4776
4663
4719
```

The selection focuses on authentication, privileged logons, process execution, account and group changes, Kerberos activity, file access and audit policy changes.

The event selection can be refined later as the monitoring architecture evolves.

---

## Connectivity Validation

The WEF infrastructure has passed several validation layers.

DNS resolution correctly directs clients to:

```text
EHSL-DC01.ehsl.internal
10.10.10.10
```

TCP connectivity to the collector was validated using:

```powershell
Test-NetConnection EHSL-DC01.ehsl.internal -Port 5985
```

WS-Management connectivity was also tested after enabling WinRM on the source system.

The Subscription Manager GPO is successfully applied.

---

## Source Registration

Collector runtime status was inspected using:

```powershell
wecutil gr "EHSL - Security Events"
```

EHSL-FS01 successfully registered with the collector and reported:

```text
RunTimeStatus: Active
LastError: 0
```

EHSL-CLIENT01 was also successfully prepared for WEF communication after WinRM was started.

---

## Current Issue - Security Event Forwarding

End-to-end Security log forwarding is not yet operational.

EHSL-FS01 correctly generates local Security Event ID `4663` when an authorized user accesses an audited file.

Example validated activity:

```text
john.smith
    ↓
\\EHSL-FS01\HR
    ↓
File modification
    ↓
Event ID 4663 generated locally on EHSL-FS01
```

However, these events are not currently appearing in:

```text
EHSL-DC01
→ Forwarded Events
```

The source-side log:

```text
Microsoft-Windows-Forwarding/Operational
```

reports repeated:

```text
Event ID: 102
Level: Error
Error code: 5004
```

with the message:

```text
The subscription EHSL - Security Events can not be created.
The error code is 5004.
```

The next troubleshooting area identified is the permission context used by Windows Event Forwarding to access the Security event log.

A remediation has not yet been validated.

For this reason, no fix is documented as implemented.

---

## EHSL-CLIENT01 Observation

A separate validation test attempted to generate Process Creation Event ID `4688` on EHSL-CLIENT01.

At the time of testing, no matching local `4688` events were found in the Security log.

This is therefore treated as a separate audit-policy validation issue and not as proof of a WEF delivery failure.

Further troubleshooting is pending.

---

## Current Monitoring Pipeline

The intended monitoring pipeline is:

```text
Windows activity
       ↓
Advanced Audit Policy
       ↓
Local Security Event
       ↓
Windows Event Forwarding
       ↓
EHSL-DC01 / WEC
       ↓
Forwarded Events
       ↓
Future SIEM / Detection Layer
```

At the current stage, local event generation is validated on the server side while centralized Security event delivery remains under troubleshooting.

---

## Phase Status

**In Progress**

Completed:

- Member Server Advanced Audit Policy
- Domain Controller Advanced Audit Policy
- Workstation Advanced Audit Policy configuration
- Process Creation auditing validation on server/DC
- Command-line auditing
- File System auditing
- WEC collector deployment
- Source-initiated WEF subscription
- Subscription Manager GPO
- WinRM connectivity validation
- Source registration

Pending:

- Resolve WEF Security log error `5004`
- Validate end-to-end Event ID `4663` forwarding
- Revalidate Process Creation auditing on EHSL-CLIENT01
- Validate Event ID `4688` forwarding from EHSL-CLIENT01
- Evaluate EHSL-DC01 as a WEF source
- Refine event collection as monitoring requirements evolve

---

## Security Value

This phase introduces the foundation for centralized defensive monitoring.

Once completed, the architecture will provide:

- Centralized Windows security telemetry
- Authentication visibility
- Account and group change visibility
- Process execution telemetry
- File access monitoring
- Kerberos security events
- Audit policy change monitoring
- A telemetry source suitable for future SIEM ingestion and detection engineering