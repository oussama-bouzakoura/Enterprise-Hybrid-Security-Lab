# Enterprise Hybrid Security Lab (EHSL)

## 1. Project Vision
The Enterprise Hybrid Security Lab (EHSL) is a long-term project designed to simulate the IT infrastructure of a modern enterprise. The goal is to design, deploy, secure, automate, and document a hybrid Windows and Linux environment following industry best practices.

The project aims to strengthen practical skills in infrastructure administration, cybersecurity, networking, automation, cloud integration, and security engineering while maintaining professional documentation similar to what would be expected in a real enterprise environment.


## 2. Business Scenario
EHSL Manufacturing Ltd. is a fictional medium-sized manufacturing company headquartered in Basel, Switzerland, with approximately 80 employees distributed across two locations.

The company designs and manufactures industrial electronic components and relies heavily on its IT infrastructure to support engineering, administration, production, and remote collaboration. Due to the importance of its intellectual property and business operations, security, availability, and reliability are key priorities.

## 3. Company Overview
The company consists of the following departments:

- Management
- Human Resources
- Finance
- Engineering
- Operations
- Sales
- IT

The IT department is responsible for managing the company's infrastructure, identity services, endpoint security, monitoring, automation, and cloud integration.

## 4. Business Requirements
The IT infrastructure must provide:

- Centralized user authentication.
- Secure file sharing.
- Centralized identity management.
- Secure remote administration.
- Endpoint protection for corporate devices.
- Network segmentation.
- Security monitoring and logging.
- Backup and recovery capabilities.
- Internal web services.
- Cloud identity integration.
- Automated administrative tasks.
- Compliance with security best practices.


## 5. Security Principles
The infrastructure will follow the following security principles:

## Least Privilege
Users and administrators will only receive the permissions required to perform their tasks.

## Defense in Depth
Multiple layers of security controls will be implemented to reduce the impact of security incidents.

## Secure by Default
Systems will be deployed using secure configurations whenever possible.

## Zero Trust
Access requests will always be verified regardless of their origin.

## Logging First
Critical systems and services will generate logs that can be monitored and analyzed.

## Automation
Administrative tasks should be automated whenever possible to reduce human error and improve consistency.

## 6. High-Level Architecture
The infrastructure will be based on a hybrid Windows and Linux environment running on VirtualBox.

The network will be segmented into different logical zones:

- Management Network
- Server Network
- Client Network
- DMZ
- Cloud Services


## 7. Initial Infrastructure
| Server | Operating System | Purpose |
|----------|-----------------|---------|
| EHSL-DC01 | Windows Server 2022 | Active Directory, DNS, Group Policy |
| EHSL-LNX01 | Ubuntu Server | Linux services and automation |
| WIN11-CLIENT01 | Windows 11 | Corporate workstation |

## 8. Initial Decisions
| Decision | Justification |
|------------|---------------|
| VirtualBox | Lightweight, stable and suitable for the available hardware. |
| Windows Server 2022 | Modern enterprise operating system with long-term support and extensive documentation. |
| Ubuntu Server | Widely used in enterprise environments and ideal for learning Linux administration. |
| Hybrid Infrastructure | Reflects the reality of most enterprise environments. |
| Microsoft-focused | Aligns with current career goals and enterprise demand. |
| Domain: ad.ehsl.lab | Modern naming convention that avoids issues associated with the .local domain. |
| Active Directory in Server Network | Standard enterprise architecture and easier future scalability. |

## 9. Personal Learning Objectives
The main objective of this project is to transition from a Security Operations profile towards Security Engineering and Infrastructure Security.

Through this project I aim to:

- Improve Windows Server administration.
- Master Active Directory.
- Gain hands-on Linux administration experience.
- Improve networking knowledge.
- Learn PowerShell automation.
- Improve Python scripting for infrastructure and security.
- Develop Bash scripting skills.
- Learn Microsoft Entra ID and Microsoft 365 administration.
- Deploy and secure enterprise services.
- Implement security controls following industry best practices.
- Build a professional GitHub portfolio that demonstrates practical engineering skills.

## Current Progress

Phase 0 has been successfully completed.

The initial enterprise architecture has been documented and the first infrastructure component (EHSL-DC01) has been deployed and validated.

The project is now ready to begin identity services implementation through Active Directory.


