# EHSL Architecture Diagrams

This directory is reserved for architecture and security diagrams for the Enterprise Hybrid Security Lab.

Diagrams should provide a visual representation of architecture already documented under:

`/docs`

They must reflect the implemented environment rather than future components that have not yet been deployed.

---

## Current Status

No standalone graphical architecture diagram is currently versioned in this directory.

The current architecture is documented textually in:

- `docs/phase-0/phase-0-architecture.md`
- `docs/phase-0/network-design.md`
- `docs/phase-0/server-inventory.md`

The root project README also contains simplified text-based architecture views.

A graphical architecture diagram should be added when it accurately represents the current deployed environment.

---

## Current Architecture to Represent

The current diagram should contain three virtual machines:

### EHSL-DC01

Roles:

- Active Directory Domain Services
- DNS
- DHCP
- Group Policy
- Windows Event Collector

Internal address:

`10.10.10.10`

### EHSL-FS01

Roles:

- domain member server
- business file server

Internal address:

`10.10.10.20`

### EHSL-CLIENT01

Role:

- managed Windows workstation

Internal addressing:

DHCP

---

## Network Model

The current network design contains:

### Internal Network

VirtualBox Host-Only:

`10.10.10.0/24`

Used for communication between EHSL systems.

### Internet Connectivity

Each VM also has a VirtualBox NAT interface for outbound Internet access.

The current diagram must not depict implemented:

- VLANs;
- DMZ networks;
- additional routed subnets;
- Linux infrastructure;
- cloud infrastructure.

These technologies are not part of the current deployed architecture.

---

## Security Relationships

Future diagrams may also visualize security relationships such as:

### Active Directory

```text
EHSL-DC01
    |
    +---- EHSL-FS01
    |
    +---- EHSL-CLIENT01
```

### File Access

```text
User
 |
 v
Global Group
 |
 v
Domain Local Group
 |
 v
NTFS / SMB Permission
 |
 v
EHSL-FS01
```

### Windows Event Forwarding

```text
EHSL-FS01 --------+
                  |
                  +----> EHSL-DC01
                  |      ForwardedEvents
EHSL-CLIENT01 ----+
```

### Credential and Data Protection

```text
Active Directory
      |
      +---- Windows LAPS
      |
      +---- BitLocker Recovery Escrow
```

---

## Diagram Requirements

Architecture diagrams should:

- use the actual EHSL hostnames;
- use the actual `ehsl.internal` domain;
- use the current `10.10.10.0/24` internal network;
- distinguish internal and NAT connectivity;
- distinguish infrastructure roles clearly;
- avoid presenting planned systems as deployed;
- remain readable when viewed directly on GitHub.

---

## Recommended Initial Diagram

The first graphical diagram should be a high-level infrastructure architecture view containing:

- EHSL-DC01;
- EHSL-FS01;
- EHSL-CLIENT01;
- Host-Only network;
- NAT / Internet path;
- Active Directory relationship;
- WEF flow toward EHSL-DC01;
- file-service role on EHSL-FS01.

More specialized diagrams can be added later if they provide additional value.

---

## File Naming

Diagram files should use descriptive lowercase names.

Recommended examples:

`ehsl-current-architecture.png`

`wef-architecture.png`

`active-directory-structure.png`

`file-access-model.png`

These are naming recommendations and do not indicate that the files currently exist.

---

## Source Files

Where practical, editable diagram source files should be retained alongside exported images.

The exported format should be easy to render directly from GitHub.

No diagram should contain:

- passwords;
- recovery secrets;
- authentication tokens;
- other sensitive information.
