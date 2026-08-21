
## What I Built

For this lab, I created the foundational Azure environment for the **Secure Hybrid Cloud Environment** project.

The lab included:

* **4 Azure Resource Groups** organized by functional area
* **Resource tags** to establish basic governance and organization
* A **Microsoft Entra ID test security group**
* **Azure RBAC role assignments**
* Exploration of **Access control (IAM)** and RBAC scope

The purpose of this lab was to establish the identity, organization, and access-control foundation before introducing networking and compute resources.

---

## Why I Organized the Resource Groups This Way

I separated the Resource Groups based on the function of the resources they will eventually contain:

```text
Azure Subscription
│
├── rg-secure-network
│
├── rg-secure-identity
│
├── rg-secure-compute
│
└── rg-secure-security
```

### `rg-secure-network`

This Resource Group will eventually contain networking components such as:

* Virtual Networks
* Subnets
* Network Security Groups
* Public IPs
* Network interfaces
* Other networking resources

Separating networking allows network-related resources to be managed independently from compute and security resources.

### `rg-secure-identity`

This Resource Group is intended for identity-related Azure resources and configurations.

The project uses **Microsoft Entra ID** as the identity foundation, while Azure RBAC controls authorization to Azure resources.

### `rg-secure-compute`

This Resource Group will eventually contain compute resources such as:

* Virtual Machines
* Disks
* Network Interfaces
* Other workloads

Keeping compute resources together makes it easier to manage their lifecycle and permissions.

### `rg-secure-security`

This Resource Group is intended for security and monitoring components such as:

* Key Vault
* Microsoft Defender for Cloud resources/configuration
* Log Analytics
* Microsoft Sentinel-related resources
* Security monitoring components

This separation reflects the eventual security architecture of the project.

### Why Use Separate Resource Groups?

The primary reason is **management and access control**, not simply organization.

Resource Groups provide a convenient boundary for:

* RBAC assignments
* Resource lifecycle management
* Tagging
* Cost tracking
* Administrative responsibilities

For example, I could give a networking administrator access to:

```text
rg-secure-network
```

without automatically giving them Contributor access to:

```text
rg-secure-compute
```

This supports the principle of **least privilege**.

---

# What I Learned About RBAC Scope

One of the most important concepts from this lab was understanding that **RBAC permissions are assigned at a specific scope**.

Azure RBAC can be applied at different levels:

```text
Management Group
       │
       ▼
Subscription
       │
       ▼
Resource Group
       │
       ▼
Resource
```

The scope determines where the assigned permissions apply.

### Subscription-Level Assignment

If a user or group receives a role at the **Subscription** scope:

```text
Subscription
│
├── Resource Group A
├── Resource Group B
├── Resource Group C
└── Resource Group D
```

the permissions can apply to resources throughout the subscription, depending on the role.

This is a broad level of access.

### Resource Group-Level Assignment

If the same role is assigned at:

```text
rg-secure-network
```

the permissions are limited to that Resource Group and its resources.

The identity would not automatically receive the same permissions over:

```text
rg-secure-compute
rg-secure-security
rg-secure-identity
```

### Resource-Level Assignment

RBAC can also be assigned directly to an individual resource.

For example:

```text
rg-secure-compute
│
└── VM01
      └── RBAC Assignment
```

This provides a much narrower permission boundary.

---

## Key RBAC Lesson

The most important takeaway from this lab was:

> **The role determines what someone can do, while the scope determines where they can do it.**

For example:

```text
Contributor
     +
rg-secure-network
     =
Contributor permissions within the network Resource Group
```

Changing the scope changes the reach of the permissions.

Therefore, when designing the environment, RBAC should generally be assigned at the **lowest practical scope** necessary to accomplish the task.

---

## Architecture Principle Learned

This lab introduced an important security principle that will carry through the rest of the project:

**Least Privilege + Appropriate Scope**

Instead of giving an administrator:

```text
Owner → Subscription
```

when they only need to manage networking, a more appropriate design could be:

```text
Network Administrator
        │
        ▼
Network-related RBAC Role
        │
        ▼
rg-secure-network
```

This limits the potential impact of an incorrect configuration or compromised account.

---

## Lab Outcome

By completing this lab, I established the organizational and access-control foundation for the Secure Hybrid Cloud Environment.

I now understand:

* How Azure Resource Groups organize resources
* Why resources can be separated by function
* How tags support governance and organization
* How Entra ID groups can be used with Azure RBAC
* How RBAC roles and scopes work together
* How permissions inherit from higher scopes
* Why assigning permissions at the lowest appropriate scope supports least privilege

The next phase of the project will build on this foundation by introducing **Azure networking, VNets, subnets, NSGs, and network security architecture**.
