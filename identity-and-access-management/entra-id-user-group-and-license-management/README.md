# Microsoft Entra ID — User and Group Management, License Assignment, and User Lifecycle Operations

* **Category:** Identity and Access Management
* **Primary Operational Focus:** Cloud identity user provisioning, Security and Microsoft 365 group creation and management, group-based and individual license assignment, and user lifecycle operations including deletion and restoration within Microsoft Entra ID
* **Operational Objectives Demonstrated:** Cloud Identity Provisioning, Security Group Administration, Microsoft 365 Group Administration, Group-Based License Assignment, Individual License Assignment, License Verification, User Account Deletion, Deleted User Restoration, User Lifecycle Management
* **Primary Data Sources:** Microsoft Entra admin center (`entra.microsoft.com`), Microsoft 365 admin center (`admin.microsoft.com`)

> **Terminology used:**
> **Workflows** refer to repeatable Microsoft Entra ID administration tasks such as user provisioning, group management, and license assignment.
> **Executions** refer to the hands-on administration performed using the Microsoft Entra admin center and the Microsoft 365 admin center.
> **Writeups** document how each configuration task was performed, how settings were validated, and how administrative decisions were reasoned through.

---

### Overview

This execution documents hands-on Microsoft Entra ID identity administration performed across four operational exercises: creating user accounts and a security group, assigning a license to a group, creating a Microsoft 365 group, assigning a license to an individual user, and performing user lifecycle operations including deletion and restoration.

The workflow is organized into four operational phases:

* **User and Group Creation, Group License Assignment** — creating a cloud identity user account (`Chris Green`) in Microsoft Entra ID, creating a security group (`Marketing`) with Assigned membership, and assigning an available license to the `Marketing` group through the Microsoft 365 admin center.
* **User Lifecycle Operations** — deleting `Chris Green` from Microsoft Entra ID, navigating to the Deleted Users list to understand the 30-day recovery window, restoring the account, and verifying restoration in the All Users list.
* **Microsoft 365 Group Creation** — creating a Microsoft 365 group (`Northwest Sales`) with an owner and a member, and verifying it appeared in the All Groups list.
* **Individual User License Assignment** — creating a second cloud identity user account (`Dominique Koch`), assigning an available license directly to that user through the Microsoft 365 admin center, and verifying the license assignment on the user's profile in the Entra admin center.

Each phase builds on foundational Entra ID concepts — understanding what a cloud identity is, how groups differ by type, why license assignment belongs in the Microsoft 365 admin center rather than the Entra admin center, and what the 30-day deletion window actually means for identity lifecycle management.

> 👉 **Follow the execution walkthrough first**
> Begin with `workflow-execution.md` to see how each phase was executed step by step, including portal navigation, configuration settings, and verification results.

> 👉 **Review analytical reasoning and conceptual notes**
> Move to `analyst-notes.md` to understand the reasoning behind each configuration decision — why group-based licensing is preferred over individual assignment, how the three identity types in Entra ID differ, what Microsoft 365 groups actually provision beyond membership, and why the 30-day deletion window has a hard limit.

> 👉 **Review tooling and portal usage details**
> See `tool-usage-notes.md` for detailed explanations of the Microsoft Entra admin center and Microsoft 365 admin center, including navigation paths, where each operation lives, and common points of confusion between the two portals.

> 👉 **See what each file contains in full detail**
> Review the **[Repository Structure & Supporting Documents](#repository-structure--supporting-documents)** section below.

---

### How to Navigate This Execution

Documentation is separated into focused components to reflect how cloud identity administration work is commonly documented in IAM and identity operations contexts.

If you want to follow the execution step by step, start with:

**`workflow-execution.md`**

This file contains the structured walkthrough showing how each user account was created, how the security and Microsoft 365 groups were provisioned, how licenses were assigned at both the group and individual levels, and how the user deletion and restoration lifecycle was performed and verified.

If you want to understand the reasoning behind the process, review:

**`analyst-notes.md`**

This file explains the major decision points and conceptual takeaways across all four phases — why cloud identities differ from synced and guest identities, why group-based licensing exists and when individual assignment is still appropriate, what the three membership types mean, what Microsoft 365 groups actually create behind the scenes, and what the 30-day deletion window means in operational terms.

If you want to understand tool and portal usage, review:

**`tool-usage-notes.md`**

This file explains how the Microsoft Entra admin center and the Microsoft 365 admin center were each used throughout the workflow, including specific navigation paths, what each portal is responsible for, and why certain operations (like license assignment) require switching between portals.

---

### Repository Structure & Supporting Documents

| File / Folder | Purpose | Contents and Focus |
| --- | --- | --- |
| `workflow-execution.md` | Structured administration walkthrough documenting all four operational phases. | Documents user account creation, group creation, group-based and individual license assignment, user deletion, restoration, and verification. |
| `images/` | Visual evidence supporting administrative actions and validation steps. | Contains screenshots of the Microsoft Entra admin center and Microsoft 365 admin center throughout the workflow. |
| `README.md` | Execution overview and operational summary. | Provides structured explanation of workflow objectives, IAM relevance, repository navigation, and intended use. |
| `analyst-notes.md` | Analyst observations, conceptual explanations, and learning notes. | Documents administrative reasoning, decision-making, and configuration interpretation used throughout the workflow. |
| `tool-usage-notes.md` | Technical reference for portals and navigation paths used throughout the workflow. | Covers the Microsoft Entra admin center and Microsoft 365 admin center, including where each operation lives and why. |

---

### Environment, Data Sources, and Tools

#### Environment and Execution Scope (At a Glance)

| Area | Details |
| --- | --- |
| **Environment Type** | Microsoft Entra ID tenant (free trial subscription) |
| **Portals Used** | Microsoft Entra admin center (`entra.microsoft.com`), Microsoft 365 admin center (`admin.microsoft.com`) |
| **Required Role** | User Administrator (minimum) |
| **Execution Platform** | Web-based admin portals |
| **Primary Tools** | Microsoft Entra admin center, Microsoft 365 admin center |
| **Operational Focus** | Cloud identity provisioning, group administration, license lifecycle management, user restoration |

#### Data Sources, Evidence, and Analysis Techniques

| Area | Details |
| --- | --- |
| **User Accounts Created** | `Chris Green` (ChrisG), `Dominique Koch` (DominiqueK) — cloud identities |
| **Security Group Created** | `Marketing` (Assigned membership) |
| **Microsoft 365 Group Created** | `Northwest Sales` (Assigned membership, with owner and member) |
| **Group License Assignment** | Available license → `Marketing` group via M365 admin center |
| **Individual License Assignment** | Available license → `Dominique Koch` directly via M365 admin center |
| **License Verified** | Confirmed on `Dominique Koch`'s Licenses profile page in Entra admin center |
| **User Deleted** | `Chris Green` — moved to 30-day suspended state |
| **User Restored** | `Chris Green` — restored from Deleted Users list with original attributes |
| **Restoration Verified** | `Chris Green` confirmed in All Users list after restoration |

---

### Intended Use

This execution is intended to demonstrate foundational Microsoft Entra ID identity administration across user provisioning, group management, license lifecycle management, and user account recovery.

The workflow reflects how an IAM analyst, identity administrator, or systems administrator may answer questions such as:

* What are the three types of user identities in Microsoft Entra ID, and why does the distinction between cloud, synced, and guest identities matter?
* What is the difference between a Security group and a Microsoft 365 group, and when is each type appropriate?
* What does Membership type (Assigned vs. Dynamic) mean, and when would Dynamic membership be used?
* Why is group-based license assignment preferred over individual assignment at organizational scale?
* Why is license assignment managed in the Microsoft 365 admin center rather than the Microsoft Entra admin center?
* What actually happens to a user account when it is deleted — and what is and is not recoverable?
* What is the 30-day deletion window, and what happens after it expires?
* Why can't a permanently deleted user be restored, even by a Global Administrator?

This process supports later work involving identity lifecycle management, license governance, group-based access control, cloud IAM operations, and identity-focused security hardening in Microsoft environments.

---

### Relevance to Security Operations and Identity and Access Management

Microsoft Entra ID is the identity foundation for Microsoft 365, Azure, and a growing number of enterprise SaaS applications. Every permission grant, license assignment, and access policy in a Microsoft cloud environment is ultimately anchored to a user object or group in Entra ID — making the administration of those objects directly relevant to both operational efficiency and security posture.

This workflow demonstrates the administrative side of cloud identity management: provisioning user accounts with the correct attributes, organizing identities into groups appropriate to their purpose, assigning licenses at a scale that's maintainable without ongoing manual intervention, and managing the full lifecycle of an identity from creation through deletion and potential restoration.

Together, these activities support:

* cloud identity provisioning and attribute configuration (usage location, display name, UPN),
* Security group and Microsoft 365 group administration,
* group-based licensing as a scalable, maintainable alternative to individual assignment,
* individual license assignment for exception or edge-case scenarios,
* understanding and managing the user deletion lifecycle (suspended state, 30-day window, restoration),
* cross-portal administration between the Microsoft Entra admin center and the Microsoft 365 admin center,
* verification of provisioning outcomes across identity and licensing systems.

This workflow demonstrates that cloud identity administration is not limited to creating accounts — it spans group design, license governance, lifecycle management, and the recovery operations that protect against the consequences of accidental or unauthorized deletions.

---

If you are reviewing this as part of my cybersecurity portfolio: this execution is intended to demonstrate Microsoft Entra ID identity administration, cloud identity provisioning, group-based license management, user lifecycle operations, and professional documentation aligned with IAM, cloud security, and identity-focused security operations workflows.
