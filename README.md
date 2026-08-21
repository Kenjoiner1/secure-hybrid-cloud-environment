# Secure Hybrid Cloud Environment

> **A hands-on enterprise cloud security engineering project built around Azure, hybrid identity, infrastructure security, SIEM, incident response, and Infrastructure as Code.**

## 🎯 Project Overview

The **Secure Hybrid Cloud Environment** is my flagship cloud and cybersecurity engineering project.

The goal is to design, deploy, secure, monitor, and document a realistic enterprise environment using Microsoft Azure and security-focused infrastructure practices.

This project is being developed incrementally as I build skills across:

* Cloud Administration
* Systems Administration
* Identity & Access Management
* Network Security
* Security Operations
* Threat Detection
* Incident Response
* Infrastructure as Code
* Cloud Security
* DevSecOps

The environment is intentionally designed to evolve alongside my professional development—from Azure administration and enterprise infrastructure to advanced cloud security engineering.

---

## 🏗️ Architecture

```text
                         SECURE HYBRID CLOUD
                                  │
             ┌────────────────────┴────────────────────┐
             │                                         │
          AZURE                                   SECURITY
             │                                         │
      ┌──────┴──────┐                         ┌────────┴────────┐
      │             │                         │                 │
   Entra ID       Networking              Sentinel         Defender
      │             │                         │                 │
   RBAC          VNet / NSG               KQL / SIEM       CSPM / XDR
      │             │                         │                 │
      │          Firewall                     └────────┬────────┘
      │             │                                  │
      └─────────────┼──────────────────────────────────┘
                    │
                  Compute
                    │
              ┌─────┴─────┐
              │           │
             VMs       Applications
              │
          Key Vault
              │
        Managed Identity

                    │
                    ▼
              TERRAFORM / IaC
                    │
             Secure Deployment
                    │
              CI/CD Pipeline
```

---

# 🔐 Core Objectives

### 1. Identity & Access Management

Implement secure identity and authorization using:

* Microsoft Entra ID
* RBAC
* Least privilege
* Resource-level permissions
* Managed identities
* Privileged access concepts
* Conditional Access

**Goal:** Understand how identity becomes the primary security boundary in cloud environments.

---

### 2. Azure Infrastructure

Build and manage:

* Resource Groups
* Virtual Networks
* Subnets
* Network Security Groups
* Azure Virtual Machines
* Storage
* Key Vault
* Azure Monitor
* Log Analytics

**Goal:** Develop strong Azure infrastructure administration skills while applying security principles throughout the environment.

---

### 3. Network Security

Implement and evaluate:

* VNet segmentation
* NSGs
* Azure Firewall
* Private endpoints
* Network traffic controls
* Secure administrative access
* Network monitoring

**Goal:** Understand how cloud networking and security controls work together to protect workloads.

---

### 4. Security Operations

Integrate:

* Microsoft Sentinel
* Log Analytics
* Microsoft Defender for Cloud
* Microsoft Defender security capabilities
* Sysmon
* Windows Event Logs
* KQL

Use the environment to:

* Collect telemetry
* Investigate suspicious activity
* Create detections
* Perform threat hunting
* Analyze security incidents

---

### 5. Incident Response

Simulate security incidents and document the complete response process.

Examples include:

* Brute-force attacks
* Suspicious PowerShell activity
* Privilege escalation
* Malicious logins
* RDP attacks
* Credential abuse
* Malware execution

Each investigation will include:

```text
Detection
   ↓
Triage
   ↓
Investigation
   ↓
Containment
   ↓
Remediation
   ↓
Recovery
   ↓
Lessons Learned
```

---

# 🧰 Technologies

## Cloud

* Microsoft Azure
* Microsoft Entra ID
* Azure Virtual Machines
* Azure Virtual Network
* Azure Key Vault
* Azure Monitor
* Log Analytics
* Microsoft Defender for Cloud
* Microsoft Sentinel

## Security

* Microsoft Sentinel
* KQL
* Sysmon
* Windows Event Logs
* MITRE ATT&CK
* Incident Response
* Threat Hunting
* Security Monitoring

## Systems Administration

* Windows Server
* Active Directory
* Group Policy
* DNS
* DHCP
* PowerShell
* Windows administration
* Linux

## Infrastructure as Code

* Terraform
* Git
* GitHub
* CI/CD
* Infrastructure security automation

## Future Technologies

* AWS
* AWS IAM
* AWS VPC
* AWS CloudTrail
* AWS GuardDuty
* AWS Security Hub
* Docker
* Kubernetes
* DevSecOps

---

# 📚 Project Development Roadmap

The project is being developed in stages.

## Phase 1 — Azure Foundations

* [x] Resource Groups
* [x] Resource tagging
* [x] Entra ID test group
* [x] RBAC assignments
* [x] IAM exploration
* [ ] Azure Policy
* [ ] Resource locks
* [ ] Governance standards

**Primary Skill:** Azure Administration

---

## Phase 2 — Enterprise Infrastructure

* [ ] Virtual Network
* [ ] Subnets
* [ ] Network Security Groups
* [ ] Windows Server VM
* [ ] Linux VM
* [ ] Active Directory
* [ ] DNS
* [ ] DHCP
* [ ] Group Policy
* [ ] PowerShell administration

**Primary Skill:** Systems Administration

---

## Phase 3 — Azure Security

* [ ] Key Vault
* [ ] Managed identities
* [ ] Azure Policy
* [ ] Defender for Cloud
* [ ] Azure Firewall
* [ ] Private endpoints
* [ ] Secure VM administration
* [ ] Security baselines

**Primary Skill:** Cloud Security

---

## Phase 4 — SIEM & Security Operations

* [ ] Log Analytics
* [ ] Microsoft Sentinel
* [ ] Windows telemetry
* [ ] Sysmon
* [ ] KQL queries
* [ ] Analytics rules
* [ ] Workbooks
* [ ] Threat hunting
* [ ] MITRE ATT&CK mapping

**Primary Skill:** Security Operations

---

## Phase 5 — Incident Response & DFIR

* [ ] Simulated security incidents
* [ ] Windows forensic analysis
* [ ] Memory analysis
* [ ] Network analysis
* [ ] Evidence collection
* [ ] Timeline analysis
* [ ] Incident reports
* [ ] Lessons-learned documentation

**Primary Skill:** Incident Response / Digital Forensics

---

## Phase 6 — Infrastructure as Code

* [ ] Terraform fundamentals
* [ ] Azure provider
* [ ] Terraform resource deployment
* [ ] Variables
* [ ] Modules
* [ ] Remote state
* [ ] Secure infrastructure deployment
* [ ] Terraform security scanning

**Primary Skill:** Infrastructure Engineering

---

## Phase 7 — DevSecOps

* [ ] GitHub Actions
* [ ] CI/CD
* [ ] Infrastructure validation
* [ ] Secret management
* [ ] Security scanning
* [ ] Container security
* [ ] Docker
* [ ] Kubernetes fundamentals

**Primary Skill:** DevSecOps

---

## Phase 8 — Multi-Cloud Security

Expand the architecture into AWS.

* [ ] AWS IAM
* [ ] AWS VPC
* [ ] AWS KMS
* [ ] CloudTrail
* [ ] GuardDuty
* [ ] Security Hub
* [ ] AWS Config
* [ ] Cross-cloud security monitoring

**Primary Skill:** Multi-Cloud Security

---

# 🧪 Hands-On Labs

Each major capability will be demonstrated through a documented lab.

Examples:

### Lab 01 — Azure Foundations

Resource Groups, tagging, Entra ID, RBAC, and IAM.

### Lab 02 — Azure Networking

VNet, subnets, NSGs, and network segmentation.

### Lab 03 — Windows Server

Domain services, DNS, DHCP, Group Policy, and administration.

### Lab 04 — Azure Security

Key Vault, managed identities, Defender for Cloud, and Azure Policy.

### Lab 05 — Sentinel

Log collection, KQL, analytics rules, and workbooks.

### Lab 06 — Threat Hunting

Investigate suspicious authentication and endpoint activity.

### Lab 07 — Incident Response

Detect, investigate, contain, and document a simulated compromise.

### Lab 08 — Terraform

Deploy Azure infrastructure through Infrastructure as Code.

### Lab 09 — Secure Terraform

Implement security controls and IaC security scanning.

### Lab 10 — Multi-Cloud

Extend security monitoring into AWS.

---

# 📊 Security Engineering Approach

The project follows principles derived from established security frameworks and practices, including:

* Least Privilege
* Defense in Depth
* Zero Trust
* Secure Configuration
* Identity-Centric Security
* Continuous Monitoring
* Infrastructure as Code
* Incident Response
* Security Automation

Security decisions will be documented rather than simply implemented.

For example:

> **What was configured?**

> **Why was it configured this way?**

> **What security risk does it address?**

> **How was the control tested?**

> **What happens if the control fails?**

---

# 🎓 Certification Alignment

This project is designed to reinforce skills associated with:

| Certification / Skill    | Project Alignment                   |
| ------------------------ | ----------------------------------- |
| **AZ-104**               | Azure administration                |
| **SC-200**               | Sentinel, KQL, detection & response |
| **Azure Cloud Security** | Azure security architecture         |
| **Terraform**            | Infrastructure as Code              |
| **AWS Security**         | Multi-cloud security                |
| **Security+**            | Security fundamentals               |
| **CySA+**                | Threat detection & analysis         |
| **PenTest+**             | Understanding attacker behavior     |

The goal is not simply to collect certifications, but to demonstrate the ability to **apply the knowledge in a realistic environment**.

---

# 💼 Career Objective

This project is intended to demonstrate practical capabilities relevant to positions such as:

* Systems Administrator
* Cloud Administrator
* Cybersecurity Analyst
* Security Operations Analyst
* Incident Response Analyst
* Cybersecurity Specialist
* Cloud Security Engineer
* Cybersecurity Engineer
* DevSecOps Engineer
* Cloud Security Architect

The long-term objective is to develop from **enterprise infrastructure and security operations into cloud security engineering**.

---

# 📁 Repository Structure

```text
secure-hybrid-cloud-environment/
│
├── README.md
│
├── docs/
│   └── architecture/
│
├── azure/
│   ├── networking/
│   ├── compute/
│   ├── security/
│   └── entra-id/
│
├── labs/
│   ├── 01-azure-foundations/
│   ├── 02-networking/
│   ├── 03-windows-server/
│   ├── 04-azure-security/
│   ├── 05-sentinel/
│   ├── 06-threat-hunting/
│   ├── 07-incident-response/
│   └── 08-terraform/
│
├── terraform/
│   ├── modules/
│   ├── environments/
│   └── policies/
│
├── security/
│   ├── detections/
│   ├── kql/
│   ├── sigma/
│   ├── yara/
│   └── incident-response/
│
├── automation/
│   ├── powershell/
│   └── python/
│
└── aws/
    ├── iam/
    ├── networking/
    └── security/
```

---

# 📈 Project Philosophy

This is a **living project**.

The environment will become progressively more complex as new technologies, certifications, and security concepts are introduced.

Rather than creating isolated certification labs, the objective is to continuously improve the same environment.

```text
Azure Administration
        ↓
Systems Administration
        ↓
Security Operations
        ↓
Cloud Security
        ↓
Infrastructure as Code
        ↓
DevSecOps
        ↓
Multi-Cloud Security
        ↓
Cloud Security Engineering
```

---

## ⚠️ Cost Management

This project is designed with Azure cost control in mind.

Resources will be:

* Created only when needed
* Deallocated when not in use
* Deleted after temporary labs
* Monitored for unexpected costs
* Tagged for identification and cost tracking

The goal is to maximize hands-on learning while minimizing unnecessary cloud expenses.

---

## 🚀 Current Status

**Current Phase:** Azure Foundations

**Current Focus:**

* Resource Groups
* Azure Tags
* Entra ID
* RBAC
* IAM
* Azure governance

Future phases will progressively introduce networking, compute, security monitoring, incident response, Terraform, and multi-cloud security.

---

## 👤 Author

**Kenneth D. Joiner II**

IT / Cybersecurity Professional

Focus Areas:

* Azure
* Cloud Security
* Cybersecurity
* Systems Administration
* Identity & Access Management
* Security Operations
* Infrastructure as Code
* Incident Response

---

> **This project is built to demonstrate the ability to design, deploy, secure, monitor, investigate, and automate enterprise cloud infrastructure—not simply complete certification labs.**
