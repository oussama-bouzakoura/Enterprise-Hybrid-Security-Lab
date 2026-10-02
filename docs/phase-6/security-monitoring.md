@'
# Phase 6 - Security Monitoring and Windows Event Forwarding

## 1. Purpose

Phase 6 introduces centralized Windows security monitoring into the Enterprise Hybrid Security Lab (EHSL).

The objective is to move from security controls that generate local audit events to an architecture in which relevant events from multiple systems are collected centrally.

The implementation uses native Windows technologies:

- Advanced Audit Policy
- Windows Security Event Log
- Windows Event Forwarding (WEF)
- Windows Event Collector (WEC)

This creates a centralized telemetry foundation that can later support additional detection engineering or SIEM integration.

---

## 2. Architecture

The current monitoring architecture uses:

### Collector

`EHSL-DC01`

EHSL-DC01 runs the Windows Event Collector service and receives forwarded events.

### Sources

Current WEF sources are:

- `EHSL-FS01`
- `EHSL-CLIENT01`

### Destination Log

Forwarded events are stored on EHSL-DC01 in:

`ForwardedEvents`

### Subscription

The production subscription is:

`EHSL - Security Events`

The subscription uses a source-initiated model.

---

## 3. Monitoring Flow

The implemented event flow is:

`EHSL-FS01 / EHSL-CLIENT01`

↓

`Windows Security Event Log`

↓

`Windows Event Forwarding`

↓

`EHSL-DC01`

↓

`ForwardedEvents`

This architecture centralizes selected security telemetry without requiring a third-party agent.

---

## 4. Source-Initiated Architecture

EHSL uses source-initiated Windows Event Forwarding.

In this model:

1. Group Policy configures domain systems with the Subscription Manager.
2. Source systems contact EHSL-DC01.
3. The collector determines which subscription applies.
4. Matching events are forwarded to the collector.
5. Events are stored in the ForwardedEvents log.

This model is suitable for centrally managed Active Directory environments because source configuration can be distributed through Group Policy.

---

## 5. Windows Event Collector

EHSL-DC01 provides the collector role.

The Windows Event Collector service receives events from configured domain systems.

The collector is intentionally hosted on EHSL-DC01 because of the limited resources available to the lab.

In a larger production environment, event collection infrastructure could be separated from the domain controller based on:

- scale;
- security boundaries;
- availability requirements;
- event volume;
- retention requirements.

Within EHSL, this consolidation is an intentional lab design decision.

---

## 6. Group Policy

Windows Event Forwarding source configuration is centrally managed using:

`EHSL - Windows Event Forwarding`

The policy is linked at the EHSL OU level so applicable domain systems can receive the source configuration.

The WEF configuration works together with the system-specific baseline GPOs.

Relevant GPOs include:

- `EHSL - Windows Event Forwarding`
- `EHSL - Workstation Baseline`
- `EHSL - Member Servers Baseline`
- `EHSL - Domain Controllers Baseline`

The baseline policies also provide the Advanced Audit Policy settings required to generate useful security telemetry.

---

## 7. Advanced Audit Policy

EHSL uses Advanced Audit Policy to generate security events required for monitoring and investigation.

The auditing strategy includes categories relevant to:

- authentication;
- logon and logoff;
- account management;
- privilege use;
- process creation;
- Kerberos;
- audit-policy changes;
- object access.

This provides the event source for the centralized WEF architecture.

---

## 8. Process Creation Auditing

Process creation auditing is enabled to generate:

`Event ID 4688`

This event records process creation activity.

EHSL also enables command-line information in process creation events.

This increases visibility into execution behavior and improves the value of the telemetry for:

- incident investigation;
- suspicious process analysis;
- PowerShell investigation;
- malware analysis;
- future detection engineering.

Command-line event data can contain sensitive information if applications pass secrets as arguments and must therefore be treated as security-sensitive telemetry.

---

## 9. File Access Auditing

EHSL-FS01 uses object-access auditing on business file resources.

SACLs are configured on the relevant file-service folders.

This allows Windows to generate:

`Event ID 4663`

when audited file-system objects are accessed.

Phase 5 validates local file-access auditing.

Phase 6 extends that capability by forwarding selected file-access telemetry to the central collector.

This creates a complete monitoring path:

`User access -> FS01 Security log -> WEF -> DC01 ForwardedEvents`

---

## 10. Production Subscription

The production WEF subscription is:

`EHSL - Security Events`

The subscription collects selected Security log events from configured source systems.

The final event selection includes:

- `4624` - Successful logon
- `4625` - Failed logon
- `4634` - Logoff
- `4648` - Logon using explicit credentials
- `4672` - Special privileges assigned to new logon
- `4688` - Process creation
- `4720` - User account created
- `4722` - User account enabled
- `4725` - User account disabled
- `4726` - User account deleted
- `4728` - Member added to a global security group
- `4729` - Member removed from a global security group
- `4732` - Member added to a local security group
- `4733` - Member removed from a local security group
- `4740` - User account locked out
- `4768` - Kerberos TGT requested
- `4769` - Kerberos service ticket requested
- `4771` - Kerberos pre-authentication failed
- `4776` - Credential validation
- `4663` - Object access
- `4719` - System audit policy changed

The selection is intentionally focused on events with useful security and investigative value.

---

## 11. Delivery Configuration

The production subscription uses a low-latency delivery model.

The final configuration uses:

`MinLatency`

with push delivery.

This configuration was selected after validation during troubleshooting.

Low-latency delivery is appropriate for the current lab because the event volume is limited and rapid visibility is more useful than bandwidth optimization.

---

# 12. Troubleshooting and Engineering Findings

The WEF deployment required troubleshooting before end-to-end forwarding became operational.

The issues and their resolution are preserved here because they represent important engineering findings from the implementation.

---

## 12.1 Initial Symptom

During initial deployment, the collector recognized the configured source systems but Security events were not successfully forwarded.

WEF source status reported:

`LastError = 5004`

This indicated that the forwarding architecture was partially functional but access to the requested event channel was failing.

---

## 12.2 Security Log Access Investigation

The problem was isolated to access to the Windows Security log.

In the implemented EHSL configuration, the forwarding service context required additional rights before Security events could be forwarded successfully.

The validated configuration added:

`NETWORK SERVICE`

to the local:

`Event Log Readers`

group.

This membership was deployed through Restricted Groups in the relevant baseline GPOs.

---

## 12.3 SeSecurityPrivilege

Event Log Readers membership alone did not fully resolve the behavior in the EHSL configuration.

The forwarding service context also required:

`SeSecurityPrivilege`

corresponding to:

`Manage auditing and security log`

This user right was assigned to:

`NETWORK SERVICE`

through Group Policy.

The configuration was applied to the relevant source classes through:

- `EHSL - Workstation Baseline`
- `EHSL - Member Servers Baseline`

After policy application and restart, the WEF sources reached:

`Active`

with:

`LastError = 0`

---

## 12.4 Scope of the Finding

The `NETWORK SERVICE` permissions described above are recorded as a finding from the implemented EHSL environment.

They should not be interpreted as a universal requirement for every Windows Event Forwarding deployment.

WEF behavior can depend on factors including:

- operating-system version;
- event channel;
- subscription configuration;
- service context;
- Group Policy;
- existing local security configuration.

The important engineering result is that the issue was isolated, corrected, and validated in the deployed environment.

---

## 12.5 Apparent Forwarding Failure After Error 5004

After resolving the Security-log access problem, source status showed:

`Active`

and:

`LastError = 0`

but test events still appeared not to arrive immediately.

This initially suggested that another forwarding problem might remain.

Further investigation showed that the issue was not source connectivity or Security-log access.

---

## 12.6 Delivery Latency Root Cause

A temporary troubleshooting subscription:

`EHSL - Security Test`

was configured using a bandwidth-oriented delivery mode.

The effective configuration included a delivery maximum latency of:

`21600000 ms`

which corresponds to approximately:

`6 hours`

The source was therefore healthy, but the subscription was allowed to delay event delivery significantly.

This explained why:

- the source appeared Active;
- LastError was 0;
- events did not appear promptly on the collector.

---

## 12.7 Delivery Mode Correction

The troubleshooting subscription was changed to a low-latency delivery model.

After changing the configuration to:

`MinLatency`

events were forwarded successfully.

This confirmed that the remaining issue was delivery behavior rather than event-generation, authentication, or source connectivity.

The production subscription was subsequently configured using the validated low-latency model.

---

## 12.8 Temporary Troubleshooting Objects

The temporary subscription:

`EHSL - Security Test`

was removed after validation.

Temporary troubleshooting artifacts were also cleaned up after the WEF pipeline was confirmed operational.

The production environment therefore retains only the required operational configuration.

---

# 13. End-to-End Validation

The final architecture was validated from both WEF source systems.

---

## 13.1 EHSL-CLIENT01

Successful logon telemetry from EHSL-CLIENT01 was received on EHSL-DC01.

This validated the path:

`CLIENT01 Security Log`

↓

`Windows Event Forwarding`

↓

`DC01 ForwardedEvents`

Event ID `4624` was successfully observed from the workstation source.

---

## 13.2 EHSL-FS01

Successful logon telemetry from EHSL-FS01 was also received by the collector.

Event ID `4624` confirmed successful forwarding from the member server.

This demonstrated that the production subscription could collect Security events from both endpoint classes represented in the lab.

---

## 13.3 File Access Validation

A domain user accessed the HR share on EHSL-FS01.

The access generated:

`Event ID 4663`

on the file server.

The event was subsequently received in:

`ForwardedEvents`

on EHSL-DC01.

This validated the complete business-security monitoring path:

`Domain user`

↓

`SMB resource on EHSL-FS01`

↓

`NTFS/SACL auditing`

↓

`Security Event 4663`

↓

`Windows Event Forwarding`

↓

`EHSL-DC01 ForwardedEvents`

This is an important validation because it connects the access-control implementation from Phase 5 directly to the centralized monitoring architecture from Phase 6.

---

# 14. Operational Verification

## Collector Service

On EHSL-DC01:

```powershell
Get-Service Wecsvc
```

The Windows Event Collector service should be running.

---

## Subscription Inventory

On EHSL-DC01:

```powershell
wecutil enum-subscription
```

The production subscription should include:

`EHSL - Security Events`

---

## Subscription Configuration

```powershell
wecutil get-subscription "EHSL - Security Events"
```

This can be used to review the effective subscription configuration.

---

## Runtime Status

```powershell
wecutil get-subscriptionruntimestatus "EHSL - Security Events"
```

This provides source runtime information for the subscription.

---

## Forwarded Events

Recent forwarded events can be reviewed with:

```powershell
Get-WinEvent -LogName ForwardedEvents -MaxEvents 20 |
Select-Object TimeCreated, Id, MachineName
```

Specific event IDs can also be queried.

Example for successful logons:

```powershell
Get-WinEvent -FilterHashtable @{
    LogName = 'ForwardedEvents'
    Id      = 4624
} -MaxEvents 20
```

Example for file access:

```powershell
Get-WinEvent -FilterHashtable @{
    LogName = 'ForwardedEvents'
    Id      = 4663
} -MaxEvents 20
```

---

# 15. Monitoring Architecture Value

The completed Phase 6 implementation provides EHSL with a centralized native Windows telemetry layer.

Security activity can now be observed centrally rather than requiring manual inspection of individual systems.

The architecture provides visibility into areas including:

- successful authentication;
- failed authentication;
- privileged logons;
- explicit credential use;
- process execution;
- account changes;
- security-group membership changes;
- account lockouts;
- Kerberos activity;
- file access;
- audit-policy changes.

This creates a stronger foundation for investigation and future detection engineering.

---

# 16. Relationship to Future SIEM Integration

Windows Event Forwarding is not treated as the final monitoring platform.

Instead, it provides a validated Windows telemetry pipeline that can later feed additional security platforms.

Potential future integrations include:

- Microsoft Sentinel;
- Microsoft Defender security telemetry;
- other SIEM platforms;
- custom PowerShell analysis;
- detection-engineering workflows.

Those integrations are future work and are not represented as currently deployed capabilities.

---

# 17. Security Considerations

Centralized event collection introduces several security considerations.

### Log Access

Forwarded security telemetry can contain sensitive information and should only be accessible to authorized administrators.

### Command-Line Data

Event ID `4688` may contain process command-line information.

Secrets should never intentionally be placed in command-line arguments.

### Event Volume

Additional event IDs should be introduced based on monitoring value rather than collecting all available Windows events without purpose.

### Collector Availability

EHSL currently uses a single collector.

This is acceptable for the lab but does not provide high availability.

### Collector Placement

EHSL-DC01 currently performs both domain-controller and collector responsibilities due to resource constraints.

A larger production design should evaluate whether these functions should be separated.

---

# 18. Lessons Learned

Phase 6 demonstrated several practical monitoring lessons.

### A Healthy Source Does Not Guarantee Immediate Delivery

`Active / LastError = 0` confirms important aspects of source health but does not necessarily mean an event must appear immediately.

Delivery configuration must also be considered.

### Permissions Matter at the Event Channel

A working WinRM or WEF connection does not automatically guarantee access to every Windows event channel.

### Delivery Mode Matters

A bandwidth-optimized subscription can create substantial event-delivery delay while remaining technically healthy.

### End-to-End Testing Is Essential

The final validation did not stop at checking subscription status.

Real security activity was generated and traced from source to collector.

### Business Events Provide Better Validation

Forwarding a real `4663` generated through access to a protected departmental share provided stronger evidence than validating only synthetic or generic events.

---

# 19. Phase 6 Status

**Status: Completed**

Validated capabilities include:

- Advanced Audit Policy;
- process creation auditing;
- command-line process auditing;
- Windows Event Collector on EHSL-DC01;
- source-initiated Windows Event Forwarding;
- EHSL-CLIENT01 forwarding;
- EHSL-FS01 forwarding;
- Security-log forwarding;
- production Security event filtering;
- low-latency delivery;
- centralized successful-logon events;
- centralized file-access Event ID 4663;
- resolved Error 5004;
- validated end-to-end event delivery.

The production monitoring path is operational:

`EHSL sources -> Windows Security logs -> WEF -> EHSL-DC01 -> ForwardedEvents`

Phase 6 therefore provides the centralized Windows security-monitoring foundation for subsequent EHSL security work.
'@ | Set-Content -Path ".\docs\phase-6\security-monitoring.md" -Encoding UTF8