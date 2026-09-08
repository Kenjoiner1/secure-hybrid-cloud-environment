# Lab 02 — Azure Networking & Network Segmentation

## Overview

This lab establishes the networking foundation for the **Secure Hybrid Cloud Environment** flagship project.

The objective was to design and deploy an Azure Virtual Network using subnet segmentation and Network Security Groups (NSGs) to establish basic security boundaries between management, server, and workload resources.

This lab builds upon **Lab 01 — Azure Resource Organization, RBAC & IAM** and begins the enterprise infrastructure portion of the project.

---

## Objectives

* Design an Azure Virtual Network using private address space
* Create dedicated subnets for different workload types
* Implement Network Security Groups
* Associate NSGs with appropriate subnets
* Restrict administrative access to the server subnet
* Avoid exposing servers directly to the public Internet
* Establish the networking foundation for future Active Directory and Linux infrastructure

---

## Architecture

```text
                    Azure
                      │
              ┌───────┴────────┐
              │ vnet-securecloud │
              │   10.10.0.0/16  │
              └───────┬────────┘
                      │
       ┌──────────────┼──────────────┐
       │              │              │
       ▼              ▼              ▼
┌─────────────┐ ┌─────────────┐ ┌─────────────┐
│ Management  │ │   Servers   │ │  Workloads  │
│10.10.1.0/24 │ │10.10.10.0/24│ │10.10.20.0/24│
└──────┬──────┘ └──────┬──────┘ └─────────────┘
       │               │
       │               │
 nsg-management    nsg-servers
                       │
                       ▼
                     DC01
```

---

# Resource Organization

The project uses separate resource groups based on function and lifecycle rather than placing every resource into a single resource group.

| Resource Group              | Purpose                                      |
| --------------------------- | -------------------------------------------- |
| `rg-securecloud-network`    | Networking resources                         |
| `rg-securecloud-compute`    | Virtual machines and compute resources       |
| `rg-securecloud-storage`    | Storage and backup resources                 |
| `rg-securecloud-monitoring` | Monitoring, logging, and security monitoring |

This approach provides logical separation between infrastructure functions and demonstrates an enterprise-oriented approach to Azure resource management.

---

# 1. Virtual Network

### VNet

**Name:** `vnet-securecloud`

**Address Space:**

```text
10.10.0.0/16
```

The `/16` address space provides sufficient room for the current lab while leaving space for additional subnets and workloads as the environment expands.

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

### Why subnet segmentation?

Separating resources into dedicated subnets allows security controls to be applied based on the role of the system.

For example:

* Management systems can be restricted to administrative traffic.
* Servers can be isolated from direct Internet access.
* Application workloads can receive their own security boundaries.
* NSGs can be associated with individual subnets.

This creates a foundation for implementing **defense-in-depth** rather than treating the entire VNet as a flat network.

---

# 3. Network Security Groups

Two NSGs were created:

```text
nsg-management
nsg-servers
```

### NSG Associations

| NSG              | Associated Subnet |
| ---------------- | ----------------- |
| `nsg-management` | `snet-management` |
| `nsg-servers`    | `snet-servers`    |

Associating the NSGs at the subnet level allows security rules to apply consistently to resources deployed within those network segments.

---

# 4. Server Network Security Rules

The server subnet was configured to permit administrative access from the management subnet.

### Allow RDP

```text
Name:        Allow-RDP-Management
Priority:    100
Source:      10.10.1.0/24
Destination: Any
Protocol:    TCP
Port:        3389
Action:      Allow
```

### Allow WinRM

```text
Name:        Allow-WinRM-Management
Priority:    110
Source:      10.10.1.0/24
Destination: Any
Protocol:    TCP
Port:        5985
Action:      Allow
```

### Security Design

The important design decision is that RDP was **not opened to the Internet**.

Instead:

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

Additional AD DS, DNS, and other Windows infrastructure ports will be introduced later when the domain controller is deployed and the actual requirements can be validated.

---

# 5. Planned Compute Infrastructure

The networking foundation will support the following systems.

### DC01

```text
Operating System:
Windows Server

Subnet:
snet-servers

Purpose:
Active Directory Domain Services
DNS
Group Policy
Windows administration
```

### Linux VM

```text
Operating System:
Linux

Subnet:
snet-servers

Purpose:
Linux administration
SSH
Network testing
Cross-platform administration
Security testing
```

Both systems will be deployed into:

```text
rg-securecloud-compute
```

while their networking infrastructure remains in:

```text
rg-securecloud-network
```

This demonstrates that Azure resources can be separated by management responsibility while still operating within the same virtual network.

---

# Security Considerations

Several security principles were incorporated into the network design.

### 1. Network Segmentation

Management, servers, and workloads are separated into dedicated subnets.

### 2. Least Privilege

Administrative access is limited to the management subnet rather than allowing unrestricted access.

### 3. Reduced Internet Exposure

Servers will not receive public IP addresses unless there is a specific requirement.

### 4. Defense in Depth

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

### 5. Expandable Architecture

The `/16` VNet and separate subnets provide room for future infrastructure such as:

* Domain Controllers
* Linux servers
* Application workloads
* Security tooling
* Monitoring infrastructure
* Additional network security controls

---

# What I Learned

### Azure Resource Organization

Resources do not necessarily need to exist in the same resource group to communicate with one another.

The VNet can exist in:

```text
rg-securecloud-network
```

while virtual machines exist in:

```text
rg-securecloud-compute
```

This separation can support different administrative responsibilities and lifecycle management.

### Network Security Groups

NSGs provide subnet- and network-interface-level traffic filtering.

Rules are evaluated according to priority, with the **lowest numerical priority evaluated first**.

### Network Segmentation

A properly designed VNet should not be treated as a single flat network.

Different system roles should receive appropriate network boundaries and access controls.

### Secure Administration

Remote administration should be restricted to controlled management paths rather than exposing administrative services such as RDP directly to the Internet.

---

# Current Environment

At this stage of the lab:

* [x] Network resource group created
* [x] Virtual network created
* [x] Management subnet created
* [x] Server subnet created
* [x] Workload subnet created
* [x] Management NSG created
* [x] Server NSG created
* [x] NSGs associated with subnets
* [x] Management-to-server RDP rule configured
* [x] Management-to-server WinRM rule configured
* [x] Windows Server DC01 deployed
* [ ] Linux VM deployed
* [ ] Active Directory Domain Services configured
* [ ] DNS configured
* [ ] Domain controller promoted
* [ ] Group Policy configured
* [ ] Network connectivity validated
* [ ] Security testing completed

---

# Next Steps

The next phase will deploy the Windows Server and Linux VM into the segmented network.

### Planned Sequence

```text
Network Foundation
        ↓
Windows Server
        ↓
Active Directory
        ↓
DNS
        ↓
Domain Controller
        ↓
Linux Server
        ↓
Group Policy
        ↓
PowerShell Administration
        ↓
Network & Security Validation
```

The completed environment will become the foundation for the security monitoring and cloud security portions of the **Secure Hybrid Cloud Environment** project.

