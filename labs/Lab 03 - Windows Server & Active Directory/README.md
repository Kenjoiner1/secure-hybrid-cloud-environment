# Lab 03 — Windows Server & Active Directory

## Overview

This lab builds the Windows Server and Active Directory infrastructure on top of the Azure networking foundation established in **Lab 02 — Azure Networking & Network Segmentation**.

The objective was to deploy a Windows Server environment, establish Active Directory Domain Services (AD DS), configure DNS and Group Policy, perform Windows/Active Directory administration, and create a realistic Active Directory environment for subsequent security assessment and attack simulation.

The lab is divided into two implementation phases:

1. **Windows Server & Active Directory Domain Controller Deployment**
2. **BadBlood Active Directory Security Lab**

The resulting environment provides the foundation for future **Active Directory security assessment, detection engineering, incident response, and automated remediation**.

---

# Architecture

```text
                         Azure
                           │
                   ┌───────┴────────┐
                   │vnet-securecloud │
                   │  10.10.0.0/16   │
                   └───────┬────────┘
                           │
              ┌────────────┴────────────┐
              │                         │
              ▼                         ▼
       Management Subnet          Server Subnet
        10.10.1.0/24             10.10.10.0/24
              │                         │
              │                         │
              │                    ┌────┴─────┐
              │                    │   DC01   │
              │                    │ Windows  │
              │                    │  Server  │
              │                    └────┬─────┘
              │                         │
              │                         ▼
              │                    Active Directory
              │                         │
              │              ┌──────────┼──────────┐
              │              │          │          │
              │             DNS        GPO       AD Objects
              │
              │
              └──── Administrative Access
                         RDP / WinRM
```

The networking architecture is documented in:

**Lab 02 — Azure Networking & Network Segmentation**

---

# Lab Structure

## 01 — Windows Server & Active Directory Domain Controller Deployment

This phase establishes the Windows infrastructure required for the project.

### Key Components

* Windows Server VM
* Active Directory Domain Services
* Domain Controller
* DNS
* Organizational Unit
* Active Directory users
* Security groups
* Computer objects
* Group Policy
* PowerShell administration
* Active Directory Users and Computers (ADUC)
* Network connectivity validation

### Objectives

* Deploy Windows Server within the segmented Azure environment
* Configure the server for Active Directory Domain Services
* Promote the server to a Domain Controller
* Establish DNS for the domain
* Create the initial Active Directory structure
* Configure Group Policy
* Validate domain functionality
* Perform administrative tasks using PowerShell and ADUC

### Documentation

The detailed deployment and configuration procedures are located in:

```text
01 — AD-DC Deployment/
```

This folder contains the step-by-step implementation documentation and supporting evidence for the Windows Server and Active Directory deployment.

---

# 02 — BadBlood Active Directory Lab

After establishing the functional Active Directory environment, BadBlood was deployed to populate the domain with a larger and more realistic collection of Active Directory objects.

BadBlood generates synthetic Active Directory users, groups, computers, and relationships that can be used to create a more representative environment for security testing.

### Objectives

* Deploy BadBlood into the Active Directory environment
* Generate realistic AD objects and relationships
* Increase the complexity of the domain environment
* Practice Active Directory enumeration
* Identify potential security weaknesses
* Establish a realistic environment for future attack simulation
* Prepare the environment for Active Directory security assessment

### Environment Result

The BadBlood deployment successfully populated the Active Directory environment with:

```text
Users:       3
Computers:   5
Groups:      51
OUs:         1
```

The generated environment provides additional relationships and objects that can be analyzed during the next stage of the project.

### Documentation

The detailed BadBlood deployment procedures and implementation notes are located in:

```text
02 — BadBlood AD Lab/
```

---

# Active Directory Environment

The completed environment currently consists of a Windows Server Domain Controller and an Active Directory domain populated with both manually configured and BadBlood-generated objects.

```text
Active Directory
       │
       ├── Domain
       │
       ├── Organizational Units
       │
       ├── Users
       │
       ├── Security Groups
       │
       ├── Computers
       │
       ├── Group Policy
       │
       └── DNS
```

The environment is intentionally being developed as a **security testing environment**, rather than simply as a functional Windows domain.

This allows the project to progress from infrastructure administration into security operations and defensive engineering.

---

# Skills Demonstrated

This lab demonstrates practical experience across several areas of Windows and security administration.

### Windows Server Administration

* Windows Server deployment
* Server configuration
* Remote administration
* PowerShell administration
* Windows networking

### Active Directory

* AD DS deployment
* Domain Controller promotion
* DNS integration
* Users and groups
* Computer objects
* Organizational Units
* Group Policy
* ADUC administration

### Security Administration

* Identity and access management
* Privileged access considerations
* Group and permission management
* Network-based administrative controls
* Active Directory enumeration
* Security testing

### Security Testing

* BadBlood deployment
* Synthetic AD object generation
* Active Directory enumeration
* Security environment preparation
* Attack-path analysis preparation

---

# Security Design

The Windows environment builds upon the network segmentation implemented in Lab 02.

Administrative traffic is intended to follow a controlled management path:

```text
Management Subnet
10.10.1.0/24
        │
        │ RDP / WinRM
        ▼
Server Subnet
10.10.10.0/24
        │
        ▼
Windows Server / DC01
        │
        ├── AD DS
        ├── DNS
        └── Group Policy
```

This provides multiple security layers:

```text
Azure RBAC
      ↓
Resource Groups
      ↓
VNet
      ↓
Subnets
      ↓
NSGs
      ↓
Windows Firewall
      ↓
Active Directory
      ↓
Group Policy
      ↓
Identity & Access Controls
```

The architecture is designed to demonstrate how **network security, system administration, and identity security work together**.

---

# Validation

The Windows and Active Directory environment was validated through:

* Windows Server deployment
* Domain Controller promotion
* DNS configuration
* Domain connectivity
* Active Directory object creation
* Group Policy configuration
* PowerShell administration
* ADUC administration
* Domain object enumeration
* BadBlood deployment
* Validation of generated AD objects

The environment is functioning as the intended foundation for the security testing phase.

---

# Current Environment

### Azure Infrastructure

* [x] Azure VNet
* [x] Management subnet
* [x] Server subnet
* [x] Workload subnet
* [x] Server NSG
* [x] Administrative access controls

### Windows Infrastructure

* [x] Windows Server VM
* [x] Domain Controller
* [x] Active Directory Domain Services
* [x] DNS
* [x] Organizational Unit
* [x] Users
* [x] Security groups
* [x] Computer objects
* [x] Group Policy
* [x] PowerShell administration
* [x] ADUC administration

### Security Testing Environment

* [x] BadBlood deployed
* [x] AD objects generated
* [x] Domain enumeration performed
* [x] Security testing environment established

---

# Lab Outcome

Lab 03 successfully transformed the Azure networking foundation from **Lab 02** into a functional Windows and Active Directory environment.

The completed environment now provides:

* Windows Server administration experience
* Active Directory administration experience
* DNS and Group Policy experience
* PowerShell-based administration
* Identity and access management experience
* Active Directory security-testing experience
* A realistic environment for future security assessment

More importantly, the environment can now be used to demonstrate the complete security lifecycle:

```text
Build
  ↓
Configure
  ↓
Secure
  ↓
Attack / Simulate
  ↓
Detect
  ↓
Investigate
  ↓
Remediate
  ↓
Automate
```

This moves the project beyond basic cloud administration and toward **cloud security engineering and security operations**.

---

# Lessons Learned

## Infrastructure and Security Are Interdependent

Deploying a functional server is only the beginning.

Network segmentation, identity controls, Group Policy, host security, and monitoring all contribute to the overall security posture.

---

## Active Directory Is a Major Security Boundary

Active Directory controls authentication, authorization, group membership, computer identities, and administrative privileges across the Windows environment.

Because of this, weaknesses within AD can have significant security implications.

---

## Realistic Environments Are More Valuable for Security Testing

A simple domain with a few manually created objects provides limited opportunities for security analysis.

BadBlood increases the number and complexity of relationships within the domain, creating a more useful environment for studying:

* Enumeration
* Privilege relationships
* Group membership
* Attack paths
* Misconfigurations
* Detection opportunities
* Remediation

---

# Project Progression

The project currently follows this progression:

```text
Lab 01
Azure Identity & Governance
        │
        ▼
Lab 02
Azure Networking & Segmentation
        │
        ▼
Lab 03
Windows Server & Active Directory
        │
        ├── AD/DC Deployment
        │
        └── BadBlood AD Lab
                │
                ▼
Lab 04
Active Directory Security Assessment
        │
        ▼
Lab 05
Cloud Detection & Response
        │
        ▼
Lab 06
Automated Security Remediation
```

---

# Next Lab

## Lab 04 — Active Directory Security Assessment

The next phase will use the BadBlood environment created in this lab to perform a structured security assessment.

Planned activities include:

* Active Directory enumeration
* Identify excessive privileges
* Analyze group memberships
* Identify insecure configurations
* Examine potential attack paths
* Map findings to security concepts
* Develop remediation recommendations
* Apply security hardening
* Validate remediation

The goal is to move from:

> **Building Active Directory**

to:

> **Assessing and securing Active Directory.**

This will establish the foundation for subsequent detection, incident response, and automated remediation capabilities within the **Secure Hybrid Cloud Environment**.
