# Active Directory Domain Services Administration — Domain Controller Operations, User Management, Password Policies, and Security Configuration

* **Category:** Identity and Access Management
* **Primary Operational Focus:** Domain controller promotion and FSMO role transfer, Active Directory site/subnet configuration, organizational unit and user account management, security group and Protected Users administration, delegation of control, domain and fine-grained password policy configuration, Active Directory Recycle Bin enablement, NTLM restriction, advanced audit policy configuration, and user rights assignment via Group Policy
* **Operational Objectives Demonstrated:** Domain Controller Promotion, FSMO Role Transfer, AD Site and Subnet Configuration, OU and User Account Management, Security Group Administration, Protected Users Configuration, Delegation of Control, Attribute Configuration and Search, Account Disablement and Password Reset, Domain Password Policy Configuration, Fine-Grained Password Policy Configuration, AD Recycle Bin Enablement, NTLM Restriction, Advanced Audit Policy Configuration, User Rights Assignment
* **Primary Data Sources:** Active Directory Domain Services (AD DS), Server Manager, Active Directory Users and Computers (ADUC), Active Directory Administrative Center (ADAC), Active Directory Sites and Services, Group Policy Management Console (GPMC)

> **Terminology used:**
> **Workflows** refer to repeatable Active Directory administration tasks such as domain controller configuration, user lifecycle management, and policy enforcement.
> **Executions** refer to the hands-on administration performed using Server Manager, ADUC, ADAC, Active Directory Sites and Services, and the Group Policy Management Console.
> **Writeups** document how each configuration task was performed, how settings were validated, and how administrative decisions were reasoned through.

---

### Overview

This execution documents hands-on administration of Active Directory Domain Services (AD DS) within a two-domain-controller lab environment (`TAILWIND-DC1` and `TAILWIND-MBR1`, domain `tailwindtraders.internal`), based on Microsoft Learn's guided project *Administer Active Directory Domain Services*.

The workflow is organized into four operational areas:

* **Domain Controller Operations** — promoting a member server to a domain controller, transferring a Flexible Single Master Operations (FSMO) role, and creating an Active Directory site with an associated subnet.
* **User Management Operations** — creating organizational units, creating and relocating user accounts, creating a security group, configuring a Protected User, delegating administrative control over an OU, configuring and searching on a user attribute, disabling an account, and resetting a password.
* **Password Policies** — configuring the domain-wide password policy, creating a Fine-Grained Password Policy (FGPP) scoped to a privileged group, and enabling the Active Directory Recycle Bin.
* **Security Settings** — restricting NTLM authentication domain-wide, configuring advanced audit policy for account management activity on an OU, and configuring a "Deny log on as a service" user rights assignment via Group Policy.

Each operational area builds on Active Directory concepts and administrative tooling that a systems administrator or identity-focused security analyst would use to configure, harden, and maintain a Windows Server-based directory environment.

This execution is designed to demonstrate practical, tool-driven Active Directory administration — not just clicking through wizard screens, but understanding what each configuration step actually changes in the directory, why it matters operationally, and how it would be validated.

> 👉 **Follow the execution walkthrough first**
> Begin with `workflow-execution.md` to see how domain controller operations, user management, password policies, and security settings were configured and validated step by step.

> 👉 **Review analytical reasoning and conceptual notes**
> Move to `analyst-notes.md` to understand FSMO roles, AD site topology, Protected Users, Fine-Grained Password Policies, NTLM restriction, and the reasoning behind each configuration decision.

> 👉 **Review tooling and command usage details**
> See `tool-usage-notes.md` for detailed explanations of Server Manager, ADUC, ADAC, Active Directory Sites and Services, and the Group Policy Management Console.

> 👉 **See what each execution file contains in full detail**
> Review the **[Repository Structure & Supporting Documents](#repository-structure--supporting-documents)** section below.

---

### How to Navigate This Execution

Documentation is separated into focused components to reflect how Active Directory administration work is commonly documented in IAM and systems administration contexts.

If you want to follow the execution step by step, start with:

**`workflow-execution.md`**

This file contains the structured walkthrough showing how the domain controller was promoted, how the FSMO role was transferred, how the AD site and subnet were configured, how organizational units and user accounts were created and managed, how the security group and Protected User were configured, how delegation was applied, how password policies were configured, and how the security settings were applied via Group Policy.

If you want to understand the reasoning behind the process, review:

**`analyst-notes.md`**

This file explains the major learning points behind FSMO roles, AD site and subnet design, organizational unit structure, Protected Users, delegation of control, domain versus fine-grained password policies, the Active Directory Recycle Bin, NTLM restriction, advanced audit policy, and user rights assignment.

If you want to understand tool usage, review:

**`tool-usage-notes.md`**

This file explains how Server Manager, Active Directory Users and Computers, Active Directory Administrative Center, Active Directory Sites and Services, and the Group Policy Management Console were each used throughout the workflow.

---

### Repository Structure & Supporting Documents

All execution outputs are separated into focused documents to reflect operational identity and access management documentation practices.

| File / Folder           | Purpose                                                                | Contents and Focus                                                                                                                                     |
| ------------------------- | ------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `workflow-execution.md`  | Structured administration walkthrough documenting AD DS configuration.  | Documents domain controller promotion, FSMO transfer, site/subnet configuration, user/OU/group management, password policy configuration, and security settings. |
| `images/`                | Visual evidence supporting administrative actions and validation steps. | Contains screenshots of Server Manager, ADUC, ADAC, Active Directory Sites and Services, and Group Policy Management Console/Editor throughout the workflow. |
| `README.md`              | Execution overview and operational summary.                            | Provides structured explanation of workflow objectives, IAM relevance, repository navigation, and intended use.                                          |
| `analyst-notes.md`       | Analyst observations, conceptual explanations, and learning notes.      | Documents Active Directory concepts, administrative reasoning, and configuration interpretation used throughout the workflow.                            |
| `tool-usage-notes.md`    | Technical reference for tools used throughout the workflow.             | Covers Server Manager, ADUC, ADAC, Active Directory Sites and Services, Group Policy Management Console, and common mistakes.                             |

---

### Environment, Data Sources, and Tools

The execution focuses on Active Directory Domain Services administration across a two-server lab domain.

#### Environment and Execution Scope (At a Glance)

| Area                   | Details                                                                 |
| ----------------------- | -------------------------------------------------------------------------- |
| **Environment Type**    | Virtualized Windows Server 2022 Evaluation Edition lab domain              |
| **Domain**              | `tailwindtraders.internal`                                                 |
| **Domain Controller 1** | `TAILWIND-DC1`                                                             |
| **Domain Controller 2** | `TAILWIND-MBR1` (promoted from member server during the workflow)          |
| **Execution Platform**  | Windows Server (Server Manager, ADUC, ADAC, GPMC)                          |
| **Primary Tools**       | Server Manager, Active Directory Domain Services Configuration Wizard, Active Directory Users and Computers, Active Directory Administrative Center, Active Directory Sites and Services, Group Policy Management Console |
| **Operational Focus**   | Active Directory administration, identity lifecycle management, and directory security configuration |

#### Data Sources, Evidence, and Analysis Techniques

| Area                                        | Details                                                                 |
| --------------------------------------------- | ---------------------------------------------------------------------------- |
| **Domain Controller Operations**             | Server promotion, RID FSMO role transfer, AD site (`Sydney`) and subnet (`172.16.1.0/24`) creation |
| **Organizational Units Created**             | `Sydney`, `Melbourne`, `Brisbane`                                            |
| **User Accounts Created**                    | `SydneyContractor`, `MelbourneContractor`, `BrisbaneContractor`             |
| **Security Group Created**                   | `Sydney Administrators` (Universal scope)                                    |
| **Protected Users Configuration**            | `SydneyContractor` added to the built-in Protected Users group              |
| **Delegation Configured**                    | Sydney Administrators delegated password reset/force-change over the Sydney OU |
| **Attribute Configuration**                  | City attribute set to `Sydney` on `SydneyContractor`, validated via Find     |
| **Account Lifecycle Actions**                | `MelbourneContractor` disabled; `BrisbaneContractor` password reset          |
| **Domain Password Policy**                   | Minimum password length set to `14` characters                              |
| **Fine-Grained Password Policy**             | `Domain Admin Password Policy` — precedence `1`, minimum length `16`, applied to Domain Admins |
| **AD Recycle Bin**                           | Enabled via Active Directory Administrative Center                          |
| **NTLM Restriction**                         | "Network security: Restrict NTLM: NTLM authentication in this domain" set to Deny all |
| **Advanced Audit Policy**                    | `SydneyOUPolicy` GPO — Audit User Account Management (Success and Failure) linked to the Sydney OU |
| **User Rights Assignment**                   | "Deny log on as a service" configured for the Sydney Administrators group via `SydneyOUPolicy` |

---

### Intended Use

This execution is intended to demonstrate foundational Active Directory Domain Services administration across domain controller configuration, identity lifecycle management, password policy enforcement, and directory security hardening.

The workflow reflects how an administrator or IAM-focused analyst may answer questions such as:

* How is a member server promoted to a domain controller, and why would a FSMO role need to be transferred?
* How is Active Directory site topology configured to reflect physical network locations?
* How are organizational units used to structure and delegate administration of user accounts?
* How does the Protected Users group differ from standard privileged group membership?
* How is administrative control delegated over a specific OU without granting full domain admin rights?
* How does a domain-wide password policy differ from a Fine-Grained Password Policy, and when is each used?
* Why would an organization enable the Active Directory Recycle Bin before it's needed?
* Why is restricting NTLM authentication a meaningful security hardening step?
* How does advanced audit policy configuration improve visibility into account management activity?
* Why would a security group be explicitly denied the right to log on as a service?

This process supports later work involving identity and access management, directory services administration, Group Policy design, and identity-focused security hardening.

---

### Relevance to Security Operations and Identity and Access Management

Active Directory remains the backbone of identity and access management in the majority of enterprise environments, and misconfigurations or weak defaults within AD DS are among the most common paths to privilege escalation and lateral movement in real-world incidents.

This workflow demonstrates the administrative side of identity security: promoting and managing domain controllers, structuring the directory with OUs and groups, applying least-privilege delegation instead of blanket administrative rights, enforcing strong and role-appropriate password policies, and hardening authentication and auditing settings at the domain level.

Together, these activities support:

* Active Directory domain controller administration,
* identity lifecycle management (creation, modification, disablement, deletion/recovery),
* least-privilege delegation and access control,
* password policy enforcement and privileged account hardening,
* legacy authentication protocol restriction (NTLM),
* audit policy configuration for identity-related security monitoring,
* Group Policy-driven security configuration.

This workflow demonstrates how identity and access management is not just about creating accounts — it's about structuring the directory, enforcing policy consistently, and configuring the security settings that make an Active Directory environment resilient against common attack techniques.

---

If you are reviewing this as part of my cybersecurity portfolio: this execution is intended to demonstrate foundational Active Directory Domain Services administration, identity lifecycle management, least-privilege delegation, password policy configuration, directory security hardening, and professional documentation aligned with IAM, systems administration, and identity-focused security operations workflows.