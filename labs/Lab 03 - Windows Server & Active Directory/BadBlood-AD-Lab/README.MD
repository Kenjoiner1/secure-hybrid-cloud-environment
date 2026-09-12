# BadBlood Active Directory Security Lab

## Overview

This lab intentionally introduces realistic Active Directory objects, relationships, permissions, and security weaknesses into the **Secure Hybrid Cloud Environment**.

The purpose is to create a realistic enterprise-style Active Directory environment that can later be assessed, monitored, attacked within the isolated lab, remediated, and validated.

This lab follows the security engineering lifecycle:

```text
Build
  ↓
Document
  ↓
Introduce Misconfigurations
  ↓
Assess
  ↓
Detect
  ↓
Analyze
  ↓
Remediate
  ↓
Automate
  ↓
Validate
```

BadBlood is being used strictly within a disposable, isolated laboratory environment for security research and training.

> **Warning:** BadBlood is designed to modify Active Directory significantly. It should never be executed against a production Active Directory environment.

---

# Lab Objectives

The objectives of this phase are to:

* Populate the lab Active Directory environment with realistic enterprise objects
* Create users, groups, computers, and organizational structures
* Introduce realistic object relationships and permissions
* Create a more complex environment for security analysis
* Establish a foundation for future BloodHound analysis
* Identify potential privilege escalation and attack paths
* Map findings to MITRE ATT&CK
* Develop Microsoft Sentinel/KQL detections
* Develop PowerShell remediation procedures
* Eventually automate selected security responses
* Validate that security controls successfully reduce identified risk

---

# Environment

## Azure Architecture

The Active Directory environment is hosted inside the Azure-based Secure Hybrid Cloud Environment.

```text
                         INTERNET
                            │
                            │
                     ┌──────▼──────┐
                     │   MGMT01    │
                     │ Management  │
                     │ Jump Host   │
                     │ 10.10.1.x   │
                     └──────┬──────┘
                            │
                    Private Azure VNet
                            │
                     ┌──────▼──────┐
                     │    DC01     │
                     │ Domain Ctrl │
                     │    DNS      │
                     │ 10.10.10.4  │
                     └──────┬──────┘
                            │
                  corp.securecloud.local
                            │
             ┌──────────────┼──────────────┐
             │              │              │
          Users          Groups        Computers
```

### Azure Network

| Component          | Configuration            |
| ------------------ | ------------------------ |
| Resource Group     | `rg-securecloud-network` |
| Virtual Network    | `vnet-securecloud`       |
| VNet Address Space | `10.10.0.0/16`           |
| Management Subnet  | `10.10.1.0/24`           |
| Server Subnet      | `10.10.10.0/24`          |
| Workload Subnet    | `10.10.20.0/24`          |
| Azure Region       | East US                  |

### Active Directory

| Component         | Configuration                                 |
| ----------------- | --------------------------------------------- |
| Domain Controller | `DC01`                                        |
| Domain            | `corp.securecloud.local`                      |
| DNS Server        | `DC01`                                        |
| DC01 Private IP   | `10.10.10.4`                                  |
| Operating System  | Windows Server 2025 Datacenter: Azure Edition |
| AD DS             | Installed                                     |
| DNS               | Installed                                     |
| SYSVOL            | Verified                                      |
| NETLOGON          | Verified                                      |

### Management Host

| Component | Configuration            |
| --------- | ------------------------ |
| Host      | `MGMT01`                 |
| Purpose   | Administrative jump host |
| Subnet    | `snet-management`        |
| Domain    | `corp.securecloud.local` |
| DNS       | `10.10.10.4`             |

---

# Security Design

DC01 was intentionally **not exposed directly to the Internet**.

Administrative access follows:

```text
Administrator Workstation
        │
        │ RDP
        ▼
     MGMT01
        │
        │ Private network
        │ RDP / management
        ▼
      DC01
```

This provides an additional administrative boundary and avoids exposing the domain controller's RDP service to the public Internet.

The management and server subnets are protected using Azure Network Security Groups.

---

# Prerequisites

The following components were established before deploying BadBlood:

* Azure subscription
* Azure Virtual Network
* Management subnet
* Server subnet
* Network Security Groups
* Windows Server VM for DC01
* Windows Server VM for MGMT01
* Active Directory Domain Services
* DNS
* Functional `corp.securecloud.local` domain
* MGMT01 joined to the domain
* PowerShell Active Directory module
* BadBlood repository/files

---

# BadBlood Source

BadBlood was obtained from the official GitHub repository:

[BadBlood — GitHub](https://github.com/davidprowe/BadBlood?utm_source=chatgpt.com)

The project is intended for research and training environments and can make extensive modifications to an Active Directory domain.

---

# Deployment Process

## 1. Download BadBlood

BadBlood was downloaded to **MGMT01**, which has controlled Internet access.

The files were not downloaded directly onto DC01.

This keeps the domain controller from requiring direct Internet access simply to obtain lab tooling.

---

## 2. Transfer BadBlood to DC01

The BadBlood files were transferred from MGMT01 to DC01 over the private Azure network.

Connectivity was validated before the transfer:

```powershell
Test-NetConnection 10.10.10.4 -Port 445
```

Expected result:

```text
TcpTestSucceeded : True
```

The BadBlood directory was copied to:

```text
C:\LabTools\BadBlood
```

### SMB Security Consideration

A narrowly scoped SMB rule was temporarily created to permit the management subnet to communicate with the server subnet over TCP/445.

The rule was scoped to:

```text
Source:      10.10.1.0/24
Destination: 10.10.10.0/24
Protocol:    TCP
Port:        445
```

This was used only for transferring the lab tooling.

The rule should be removed when SMB is no longer required for administrative file transfer.

---

# 3. Verify BadBlood

On DC01:

```powershell
Get-ChildItem C:\LabTools\BadBlood
```

Verify that the main script exists:

```powershell
Get-ChildItem C:\LabTools\BadBlood -Filter "Invoke-BadBlood.ps1"
```

The primary execution script is:

```text
Invoke-BadBlood.ps1
```

---

# 4. Verify PowerShell Execution Policy

The PowerShell execution policy was checked before execution:

```powershell
Get-ExecutionPolicy
```

Result:

```text
Unrestricted
```

No execution-policy modification was required.

---

# 5. Establish Clean Active Directory Baseline

Before running BadBlood, the existing Active Directory environment was measured.

Domain information was verified using:

```powershell
Get-ADDomain | Select-Object DNSRoot,NetBIOSName
```

The lab domain:

```text
DNS Root:    corp.securecloud.local
NetBIOS:     CORP
```

The initial Active Directory object counts were recorded.

### Pre-BadBlood Baseline

| Object Type          |                  Count |
| -------------------- | ---------------------: |
| Users                |                      3 |
| Groups               |                     51 |
| Organizational Units |                      1 |
| Computers            | Existing lab computers |

The baseline provides a comparison point for documenting the changes introduced by BadBlood.

---

# 6. Execute BadBlood

The BadBlood script was executed from:

```powershell
C:\LabTools\BadBlood
```

The controlled configuration used for this lab was:

```powershell
.\Invoke-BadBlood.ps1 `
    -UserCount 100 `
    -GroupCount 30 `
    -ComputerCount 10 `
    -SkipLapsInstall
```

### Configuration

| Parameter         |   Value | Purpose                                                           |
| ----------------- | ------: | ----------------------------------------------------------------- |
| `UserCount`       |     100 | Create realistic user population                                  |
| `GroupCount`      |      30 | Create additional security/group relationships                    |
| `ComputerCount`   |      10 | Create additional computer objects                                |
| `SkipLapsInstall` | Enabled | Defer LAPS implementation to a separate security-control exercise |

BadBlood was allowed to complete without interrupting the process.

---

# Why a Controlled Object Count Was Used

BadBlood supports substantially larger object populations.

For this Azure lab, the initial deployment intentionally used a smaller dataset:

```text
100 Users
30 Groups
10 Computers
```

The objective was to create enough complexity for security analysis without unnecessarily increasing administrative overhead or lab resource consumption.

The environment can be expanded later if additional scale is required.

---

# Post-Deployment State

After BadBlood completed, the Active Directory environment was intentionally left in its modified state.

No security remediation was performed immediately after execution.

This is intentional.

The purpose is to preserve the environment so that subsequent phases can identify and investigate the resulting security weaknesses.

The next assessment will establish the actual post-BadBlood object counts rather than assuming that the requested object counts exactly match the final domain totals.

---

# Security Areas for Future Assessment

The BadBlood environment will eventually be evaluated for several categories of Active Directory security issues.

## Identity and Authentication

Potential areas of investigation include:

* Weak authentication configurations
* Kerberos configuration issues
* Accounts that do not require Kerberos pre-authentication
* Service Principal Names
* Service account exposure
* Excessive account privileges
* Stale accounts

Example investigation:

```powershell
Get-ADUser -Filter * -Properties DoesNotRequirePreAuth |
Where-Object {$_.DoesNotRequirePreAuth -eq $true}
```

---

## Group and Privilege Relationships

Future analysis will investigate:

* Nested groups
* Excessive group membership
* Privileged groups
* Domain Admin membership
* Indirect privilege escalation
* Excessive administrative rights
* Users controlling privileged groups

Example:

```powershell
Get-ADGroupMember "Domain Admins"
```

---

## Active Directory ACLs

The environment will eventually be assessed for:

* Excessive permissions
* Object ownership
* Write permissions
* Group modification rights
* User modification rights
* Computer object permissions
* Delegation issues
* Potential privilege escalation paths

---

## Kerberos / SPN Analysis

Service Principal Names will be reviewed for potentially exposed service accounts.

Example:

```powershell
Get-ADUser -Filter * -Properties ServicePrincipalName |
Where-Object {$_.ServicePrincipalName} |
Select-Object SamAccountName,ServicePrincipalName
```

---

# Planned BloodHound Analysis

A future phase will introduce **BloodHound** to visualize relationships within the Active Directory environment.

The objective is not simply to collect data, but to understand how individual permissions and group memberships combine into potential attack paths.

Example conceptual path:

```text
Low-Privilege User
       │
       ▼
Group Membership
       │
       ▼
Write Permission
       │
       ▼
Privileged Group
       │
       ▼
Domain Administrator
```

The resulting attack paths will be documented and mapped to appropriate security controls.

---

# Planned MITRE ATT&CK Mapping

Findings from the AD assessment will eventually be mapped to the **MITRE ATT&CK** framework.

Potential areas may include:

| Area                   | Example ATT&CK Category  |
| ---------------------- | ------------------------ |
| Valid Accounts         | Credential/Account Abuse |
| Kerberos weaknesses    | Credential Access        |
| Group manipulation     | Privilege Escalation     |
| Permission abuse       | Privilege Escalation     |
| Service account abuse  | Credential Access        |
| Account discovery      | Discovery                |
| Domain/group discovery | Discovery                |

The exact ATT&CK technique mapping will be determined from the actual findings discovered during assessment rather than assigning techniques in advance.

---

# Planned Detection Engineering

After the environment is assessed, security telemetry will be integrated into the broader Azure security architecture.

Planned technologies include:

* Microsoft Defender for Cloud
* Microsoft Sentinel
* Log Analytics
* KQL
* Windows security event logs
* Active Directory auditing
* PowerShell logging

The goal is to develop detections based on realistic activity rather than relying exclusively on prebuilt demonstrations.

Example future workflow:

```text
AD Activity
    │
    ▼
Windows Security Logs
    │
    ▼
Log Analytics
    │
    ▼
Microsoft Sentinel
    │
    ▼
KQL Detection
    │
    ▼
Incident / Alert
```

---

# Planned Remediation

Once vulnerabilities and misconfigurations are identified, remediation will be performed systematically.

Potential remediation areas include:

* Removing excessive privileges
* Correcting group membership
* Reducing unnecessary ACL permissions
* Securing service accounts
* Implementing Windows LAPS
* Improving authentication controls
* Implementing stronger administrative boundaries
* Improving auditing
* Applying Group Policy security controls

---

# Planned Automation

Selected remediation tasks will eventually be automated using PowerShell and Azure security automation.

Example architecture:

```text
Security Finding
       │
       ▼
Detection
       │
       ▼
Assessment / Validation
       │
       ▼
Automated Playbook
       │
       ▼
PowerShell / Azure Automation
       │
       ▼
Remediation
       │
       ▼
Validation
```

The objective is to demonstrate the transition from:

> **Finding a security problem**

to:

> **Detecting, responding to, remediating, and validating a security problem programmatically.**

---

# Lab Safety and Security Controls

This environment is intentionally designed for security experimentation.

### Controls used during this phase

* DC01 is not directly exposed to the public Internet
* Administrative access occurs through MGMT01
* Internal Azure networking is used for server communication
* SMB access was narrowly scoped for file transfer
* BadBlood was executed only against the dedicated lab domain
* Production Active Directory environments are not connected to this lab
* No real credentials or production identities should be introduced
* The environment is treated as disposable

---

# Recovery Strategy

A clean recovery point was created for DC01 before introducing BadBlood-related changes.

This allows the environment to be reverted to the known-good AD baseline if required.

Conceptually:

```text
                  Clean AD
                     │
                     ▼
             Recovery Point
                     │
                     ▼
               BadBlood Run
                     │
          ┌──────────┴──────────┐
          │                     │
      Keep Lab              Revert
          │                     │
          ▼                     ▼
     Security Work        Clean Baseline
```

This makes it possible to repeat the security assessment and remediation exercises from a known starting point.

---

# Current Status

### Completed

* [x] Azure VNet deployed
* [x] Management subnet created
* [x] Server subnet created
* [x] NSGs configured
* [x] DC01 deployed
* [x] Active Directory Domain Services installed
* [x] DNS configured
* [x] `corp.securecloud.local` domain created
* [x] DC01 health verified
* [x] MGMT01 deployed
* [x] MGMT01 configured for DC01 DNS
* [x] MGMT01 joined to the domain
* [x] BadBlood transferred to DC01
* [x] BadBlood files verified
* [x] PowerShell execution policy verified
* [x] Clean AD baseline established
* [x] BadBlood executed
* [x] Intentionally modified AD environment established

### Not Yet Completed

* [ ] Post-BadBlood object inventory
* [ ] Active Directory security assessment
* [ ] BloodHound collection
* [ ] Attack-path analysis
* [ ] MITRE ATT&CK mapping
* [ ] Microsoft Sentinel integration
* [ ] KQL detections
* [ ] Security incident simulation
* [ ] Remediation
* [ ] PowerShell remediation scripts
* [ ] Automated response
* [ ] Validation testing

---

# Skills Demonstrated

This phase demonstrates practical experience with:

### Azure

* Azure Virtual Networks
* Subnet segmentation
* Network Security Groups
* Private IP addressing
* Azure VM administration
* Secure administrative access

### Windows Server

* Windows Server administration
* Active Directory Domain Services
* Domain Controller deployment
* DNS
* Domain membership
* PowerShell administration

### Identity and Access Management

* Active Directory users
* Groups
* Organizational Units
* Group relationships
* Privileged access
* Authentication architecture

### Cybersecurity

* Security lab design
* Adversarial environment creation
* Active Directory security assessment preparation
* Attack-path analysis
* MITRE ATT&CK
* Detection engineering
* Incident response
* Security remediation
* Security automation

---

# Key Takeaways

This phase transformed the original Active Directory environment from a small functional domain into a more realistic enterprise-style security testing environment.

The important distinction is that the resulting misconfigurations are **intentional**.

The environment will now be treated as a security engineering test bed where the following lifecycle can be demonstrated:

```text
                 SECURE HYBRID CLOUD
                         │
                         ▼
                  Build Environment
                         │
                         ▼
                   Create Baseline
                         │
                         ▼
                     BadBlood
                         │
                         ▼
                 Security Assessment
                         │
                         ▼
                  Attack Path Analysis
                         │
                         ▼
                    Detection
                         │
                         ▼
                    Response
                         │
                         ▼
                   Remediation
                         │
                         ▼
                    Automation
                         │
                         ▼
                    Validation
```

The ultimate objective is to demonstrate not just the ability to **deploy cloud infrastructure**, but the ability to **secure, monitor, investigate, remediate, and automate security controls across a hybrid environment**.

---

# Next Phase

The next phase will begin with a **post-BadBlood inventory and security assessment**.

The environment will remain intentionally unremediated until the assessment is complete.

Future work will focus on:

1. AD object inventory
2. Privileged account analysis
3. Group relationship analysis
4. ACL analysis
5. Kerberos/SPN analysis
6. BloodHound attack-path discovery
7. MITRE ATT&CK mapping
8. Sentinel/KQL detection engineering
9. Remediation
10. Automated security response
11. Validation and before/after comparison
