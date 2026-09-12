# Lab 02 — Azure Networking & Network Segmentation

## Overview

This lab establishes the networking foundation for the **Secure Hybrid Cloud Environment** flagship project.

The objective was to design and deploy an Azure Virtual Network using subnet segmentation and Network Security Groups (NSGs) to establish security boundaries between management, server, and workload resources.

This lab builds upon **Lab 01 — Azure Resource Organization, RBAC & IAM** and establishes the network infrastructure required for the Windows Server, Active Directory, Linux, security monitoring, and future security-testing components of the environment.

---

## Objectives

* Design an Azure Virtual Network using private address space
* Create dedicated subnets for different workload types
* Implement Network Security Groups
* Associate NSGs with appropriate subnets
* Restrict administrative access to the server subnet
* Avoid exposing administrative services directly to the public Internet
* Establish network segmentation between infrastructure roles
* Validate network connectivity and security rules
* Create the networking foundation for the Windows Server and Active Directory environment

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
          ┌────────────────┼────────────────┐
          │                │                │
          ▼                ▼                ▼
   ┌─────────────┐  ┌─────────────┐  ┌─────────────┐
   │ Management  │  │   Servers   │  │  Workloads  │
   │10.10.1.0/24 │  │10.10.10.0/24│  │10.10.20.0/24│
   └──────┬──────┘  └──────┬──────┘  └─────────────┘
          │                 │
          │                 │
 nsg-management       nsg-servers
                            │
                            ▼
                    Future Infrastructure
                    Windows / Linux / AD
```

The architecture separates administrative systems, server infrastructure, and future workload resources into dedicated network segments.

This provides a foundation for applying security controls based on system role rather than treating the VNet as a flat network.

---

# Resource Organization

The project uses separate resource groups based on function and lifecycle rather than placing every resource into a single resource group.

| Resource Group              | Purpose                                      |
| --------------------------- | -------------------------------------------- |
| `rg-securecloud-network`    | Networking resources                         |
| `rg-securecloud-compute`    | Virtual machines and compute resources       |
| `rg-securecloud-storage`    | Storage and backup resources                 |
| `rg-securecloud-monitoring` | Monitoring, logging, and security monitoring |

The networking resources for this lab are contained within:

```text
rg-securecloud-network
```

Compute resources are maintained separately while still being able to communicate through the same Azure Virtual Network.

This demonstrates the separation of **resource management scope** from **network connectivity**.

---

# 1. Virtual Network

## VNet

**Name:**

```text
vnet-securecloud
```

**Address Space:**

```text
10.10.0.0/16
```

The `/16` address space provides sufficient room for the current lab while leaving additional address space available for future subnets and workloads.

### Resource Group

```text
rg-securecloud-network
```

---

# 2. Subnet Design

Three subnets were created to provide logical network segmentation.

| Subnet            | Address Range   | Purpose                               |
| ----------------- | --------------- | ------------------------------------- |
| `snet-management` | `10.10.1.0/24`  | Administrative systems                |
| `snet-servers`    | `10.10.10.0/24` | Windows/Linux servers                 |
| `snet-workloads`  | `10.10.20.0/24` | Future application/workload resources |

## Why subnet segmentation?

Separating resources into dedicated subnets allows security controls to be applied based on the role of the system.

For example:

* Management systems can be restricted to administrative traffic.
* Servers can be isolated from direct Internet access.
* Application workloads can receive their own security boundaries.
* NSGs can be associated with individual subnets.
* Future firewall and routing controls can be applied between network segments.

This creates a foundation for implementing **defense-in-depth** rather than treating the entire VNet as a flat network.

---

# 3. Network Security Groups

Two Network Security Groups were created:

```text
nsg-management
nsg-servers
```

## NSG Associations

| NSG              | Associated Subnet |
| ---------------- | ----------------- |
| `nsg-management` | `snet-management` |
| `nsg-servers`    | `snet-servers`    |

Associating NSGs at the subnet level allows security rules to apply consistently to resources deployed within the respective network segments.

---

# 4. Server Network Security Rules

The server subnet was configured to permit administrative access from the management subnet.

## Allow RDP

```text
Name:        Allow-RDP-Management
Priority:    100
Source:      10.10.1.0/24
Destination: Any
Protocol:    TCP
Port:        3389
Action:      Allow
```

## Allow WinRM

```text
Name:        Allow-WinRM-Management
Priority:    110
Source:      10.10.1.0/24
Destination: Any
Protocol:    TCP
Port:        5985
Action:      Allow
```

## Security Design

The important security decision was that administrative services were **not opened directly to the public Internet**.

Instead, administrative traffic follows the intended network path:

```text
Management Subnet
10.10.1.0/24
       │
       │ RDP / WinRM
       ▼
Server Subnet
10.10.10.0/24
```

This establishes the principle:

> Administrative traffic should originate from a controlled management network rather than directly from the public Internet.

Additional Active Directory, DNS, and Windows infrastructure ports can be introduced at the host/network security layer when required by the actual infrastructure.

---

# 5. Network Segmentation Model

The environment uses subnet segmentation to separate systems according to function.

```text
                 vnet-securecloud
                    10.10.0.0/16
                         │
       ┌─────────────────┼─────────────────┐
       │                 │                 │
       ▼                 ▼                 ▼
 Management          Servers           Workloads
10.10.1.0/24      10.10.10.0/24     10.10.20.0/24
       │                 │                 │
       │                 │                 │
       ▼                 ▼                 ▼
 Management          Windows/Linux      Future
 Systems             Infrastructure     Applications
```

This design allows the project to expand without redesigning the entire network architecture.

Future security controls can build on this segmentation model, including:

* Azure Firewall
* Route tables
* Private endpoints
* Additional NSGs
* Network monitoring
* Defender for Cloud
* Microsoft Sentinel
* Security testing infrastructure
* Honeypot infrastructure

---

# Security Considerations

Several security principles were incorporated into the network design.

## 1. Network Segmentation

Management, servers, and workloads are separated into dedicated subnets.

This reduces the likelihood that a compromise of one network segment automatically provides unrestricted access to other resources.

---

## 2. Least Privilege

Administrative access is limited to traffic originating from the management subnet rather than allowing unrestricted administrative access.

The server subnet therefore has a more restrictive trust boundary than the management subnet.

---

## 3. Reduced Internet Exposure

Administrative services such as RDP and WinRM were not intentionally exposed to the public Internet.

The intended administrative path is:

```text
Management Network
        ↓
NSG
        ↓
Server Network
        ↓
Server
```

This reduces unnecessary exposure of administrative services.

---

## 4. Defense in Depth

Security controls are being applied at multiple layers:

```text
Azure RBAC
    ↓
Resource Groups
    ↓
Virtual Network
    ↓
Subnets
    ↓
Network Security Groups
    ↓
Host Firewall
    ↓
Identity / Authentication
```

The networking layer therefore becomes one component of a broader defense-in-depth architecture.

---

## 5. Expandable Architecture

The `/16` VNet and dedicated `/24` subnets provide room for additional infrastructure.

Potential future components include:

* Domain Controllers
* Linux servers
* Application workloads
* Security monitoring systems
* Honeypot systems
* Security testing systems
* SIEM infrastructure
* Additional network security controls

---

# Validation

The networking environment was validated by confirming:

* Virtual network configuration
* Subnet configuration
* NSG associations
* NSG rule configuration
* Management-to-server connectivity
* Administrative connectivity through the intended network path
* Communication between deployed infrastructure and the VNet

Network connectivity was validated before moving into the Windows Server and Active Directory portion of the project.

---

# Current Environment

The Azure networking foundation has been successfully deployed and validated.

### Completed

* [x] Network resource group created
* [x] Virtual network created
* [x] Management subnet created
* [x] Server subnet created
* [x] Workload subnet created
* [x] Management NSG created
* [x] Server NSG created
* [x] NSGs associated with appropriate subnets
* [x] Management-to-server RDP rule configured
* [x] Management-to-server WinRM rule configured
* [x] Network connectivity validated
* [x] Network segmentation validated

### Result

The networking layer is now ready to support the Windows Server and Active Directory infrastructure developed in **Lab 03**.

---

# What I Learned

## Azure Resource Organization

Resources do not necessarily need to exist in the same resource group to communicate with one another.

For example, networking resources can exist in:

```text
rg-securecloud-network
```

while virtual machines can exist in:

```text
rg-securecloud-compute
```

This separation supports different administrative responsibilities, lifecycle management, and resource organization.

---

## Network Security Groups

NSGs provide network traffic filtering at the subnet and network interface levels.

Rules are evaluated according to priority, with the **lowest numerical priority evaluated first**.

This makes rule ordering an important consideration when designing access controls.

---

## Network Segmentation

A properly designed VNet should not be treated as a single flat network.

Different system roles should receive appropriate network boundaries and access controls.

Subnet segmentation provides a foundation for additional security controls as the environment becomes more complex.

---

## Secure Administration

Remote administration should be restricted to controlled management paths rather than exposing administrative services such as RDP directly to the Internet.

In this environment, administrative traffic is designed to originate from the management subnet.

---

# Lab Outcome

Lab 02 successfully established the Azure networking foundation for the **Secure Hybrid Cloud Environment**.

The completed networking architecture provides:

* Segmented network zones
* Controlled administrative access
* Subnet-level security boundaries
* NSG-based traffic filtering
* Expandable private address space
* Separation between networking and compute resource management

The environment is now ready for the Windows Server and Active Directory infrastructure implemented in **Lab 03 — Windows Server & Active Directory**.

---

# Next Lab

## Lab 03 — Windows Server & Active Directory

The next phase builds the Windows infrastructure on top of the network foundation established here.

```text
Lab 02
Azure Networking
        │
        ▼
Windows Server
        │
        ▼
Active Directory Domain Services
        │
        ▼
DNS
        │
        ▼
Group Policy
        │
        ▼
PowerShell / AD Administration
        │
        ▼
BadBlood AD Environment
```

The completed Active Directory environment will subsequently be used for security assessment, attack simulation, detection engineering, and remediation activities.

---

## Project Progression

```text
Lab 01 — Azure Identity & Governance
        │
        ▼
Lab 02 — Azure Networking & Segmentation
        │
        ▼
Lab 03 — Windows Server & Active Directory
        │
        ▼
Lab 04 — Active Directory Security Assessment
        │
        ▼
Lab 05 — Cloud Detection & Response
        │
        ▼
Lab 06 — Automated Security Remediation
```
