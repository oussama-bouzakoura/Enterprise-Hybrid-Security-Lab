@'
# Phase 4 - DHCP Deployment

## 1. Purpose

This phase implements centralized IPv4 address management for the EHSL internal network using the Windows Server DHCP role.

DHCP is hosted on EHSL-DC01 and provides addressing and domain DNS configuration to systems connected to the internal VirtualBox Host-Only network.

The deployment supports both:

- dynamic workstation addressing; and
- DHCP reservations for infrastructure systems that require predictable addresses.

---

## 2. DHCP Architecture

The DHCP Server role is installed on:

`EHSL-DC01`

Internal DHCP service operates on:

`10.10.10.0/24`

EHSL uses DHCP only for the internal enterprise network.

The VirtualBox NAT interfaces use VirtualBox-provided network configuration independently from the EHSL DHCP infrastructure.

---

## 3. DHCP Scope

The active DHCP scope is:

| Setting | Value |
|---|---|
| Scope ID | `10.10.10.0` |
| Scope Name | `General DHCP range` |
| Start Range | `10.10.10.100` |
| End Range | `10.10.10.200` |
| Subnet Mask | `255.255.255.0` |
| State | Active |
| Lease Duration | 8 days |

The dynamic client pool therefore contains:

`10.10.10.100 - 10.10.10.200`

Addresses outside this range can be used for infrastructure systems through static configuration or DHCP reservations.

---

## 4. DHCP Options

The following scope-level options are configured:

### Option 006 - DNS Servers

`10.10.10.10`

Domain clients therefore use EHSL-DC01 for internal DNS resolution.

### Option 015 - DNS Domain Name

`ehsl.internal`

This aligns DHCP clients with the Active Directory DNS namespace.

### Option 051 - Lease

`691200 seconds`

This corresponds to an eight-day DHCP lease.

No additional server-level DHCP options are currently configured.

---

## 5. Infrastructure Addressing Strategy

EHSL uses different addressing methods depending on the system role.

### EHSL-DC01

EHSL-DC01 uses a static internal address:

`10.10.10.10/24`

A domain controller, DNS server, and DHCP server requires predictable network availability and should not depend on its own DHCP service for its primary internal address.

### EHSL-FS01

EHSL-FS01 uses DHCP with a reservation.

Reserved address:

`10.10.10.20`

This provides centralized address management while maintaining a stable address for the file server.

### EHSL-CLIENT01

EHSL-CLIENT01 uses normal dynamic DHCP allocation.

During validation it received:

`10.10.10.100/24`

Unlike the infrastructure reservation, this address should not be treated as permanently assigned to CLIENT01.

---

## 6. DHCP Reservation

The current reservation is:

| Property | Value |
|---|---|
| Host | `EHSL-FS01.ehsl.internal` |
| Scope | `10.10.10.0` |
| Reserved IP | `10.10.10.20` |
| Client ID | `08-00-27-11-5e-ad` |

The reservation ensures that EHSL-FS01 maintains a predictable internal address while still receiving its configuration from the centralized DHCP service.

---

## 7. DNS Integration

The DHCP configuration directs internal clients to:

`10.10.10.10`

for DNS resolution.

This is important because Active Directory depends on DNS for services including:

- domain controller discovery;
- LDAP service discovery;
- Kerberos;
- Group Policy;
- domain joins;
- internal hostname resolution.

Domain clients should therefore use the Active Directory DNS infrastructure on their internal interface rather than an external DNS resolver.

---

## 8. Dual-NIC Considerations

Current EHSL systems also have a VirtualBox NAT adapter for Internet connectivity.

The two network paths serve different purposes.

### Host-Only Adapter

Used for:

- Active Directory
- DNS
- DHCP
- Group Policy
- SMB
- Windows Event Forwarding
- internal administration

### NAT Adapter

Used for:

- outbound Internet connectivity;
- software and package downloads;
- external connectivity required by lab activities.

The VirtualBox NAT DHCP/DNS behavior is independent of the DHCP service running on EHSL-DC01.

---

## 9. Validation

DHCP functionality was validated through the deployed systems.

### Server Validation

EHSL-DC01 reports the scope as:

`Active`

with the configured range:

`10.10.10.100 - 10.10.10.200`

### Reservation Validation

EHSL-FS01 receives:

`10.10.10.20/24`

through DHCP.

Its internal interface uses:

`10.10.10.10`

as DNS.

This confirms that the reservation is functioning as intended.

### Dynamic Client Validation

EHSL-CLIENT01 receives its internal network configuration through DHCP.

Observed configuration during validation:

- IPv4: `10.10.10.100/24`
- DNS: `10.10.10.10`
- DHCP: Enabled
- Domain: `ehsl.internal`

This confirms successful dynamic client allocation and delivery of the domain DNS configuration.

---

## 10. Operational Verification

The DHCP configuration can be reviewed from EHSL-DC01 with:

```powershell
Get-DhcpServerv4Scope
```

Reservations:

```powershell
Get-DhcpServerv4Reservation -ScopeId 10.10.10.0
```

Scope options:

```powershell
Get-DhcpServerv4OptionValue -ScopeId 10.10.10.0
```

Client leases:

```powershell
Get-DhcpServerv4Lease -ScopeId 10.10.10.0
```

On a DHCP client:

```powershell
Get-NetIPConfiguration
```

and:

```powershell
ipconfig /all
```

can be used to validate the received configuration.

---

## 11. Security and Operational Considerations

The current DHCP design is appropriate for the existing single-subnet lab.

Important operational principles include:

- infrastructure services must have predictable addresses;
- domain clients must use the AD-integrated DNS infrastructure;
- DHCP reservations should be preferred when centralized address management is desired;
- dynamic client addresses must not be documented as permanent infrastructure addresses;
- the VirtualBox NAT network must not be confused with the internal EHSL addressing architecture.

If EHSL later introduces multiple internal network segments, the DHCP architecture will need to be reviewed to support the additional scopes and routing model.

---

## 12. Phase 4 Status

**Status: Completed**

Validated capabilities:

- Windows DHCP Server deployed on EHSL-DC01
- `10.10.10.0/24` scope active
- dynamic range configured
- domain DNS delivered through DHCP
- `ehsl.internal` DNS suffix delivered through DHCP
- EHSL-FS01 reservation operational
- EHSL-CLIENT01 dynamic allocation operational

DHCP now provides centralized IPv4 configuration for the EHSL internal network.
'@ | Set-Content -Path ".\docs\phase-4\dhcp-deployment.md" -Encoding UTF8