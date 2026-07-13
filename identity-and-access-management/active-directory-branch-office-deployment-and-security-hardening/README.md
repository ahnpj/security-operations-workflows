# Active Directory Branch Office Deployment and Security Hardening

* **Category:** Identity and Access Management
* **Primary Operational Focus:** Domain controller deployment and site topology configuration, Paris office identity structure and least-privilege delegation, bulk user lifecycle management using PowerShell, domain and fine-grained password policy configuration, Active Directory Recycle Bin enablement, NTLM authentication restriction, failed-logon auditing, and user rights assignment via Group Policy
* **Operational Objectives Demonstrated:** Domain Controller Promotion, AD Site and Subnet Configuration, FSMO Role Transfer, OU and User Account Management, Security Group Administration, Protected Account Configuration, Delegation of Control, Bulk PowerShell User Administration, Domain Password Policy Configuration, Fine-Grained Password Policy Configuration, AD Recycle Bin Enablement, NTLM Restriction, Advanced Audit Policy Configuration, User Rights Assignment
* **Primary Data Sources:** Active Directory Domain Services (AD DS), Server Manager, Active Directory Users and Computers (ADUC), Active Directory Administrative Center (ADAC), Active Directory Sites and Services, Group Policy Management Console (GPMC), Windows PowerShell

> **Terminology used:**
> **Workflows** refer to repeatable Active Directory administration tasks such as domain controller deployment, user lifecycle management, and policy enforcement.
> **Executions** refer to the hands-on administration performed using Server Manager, ADUC, ADAC, Active Directory Sites and Services, the Group Policy Management Console, and Windows PowerShell.
> **Writeups** document how each configuration task was performed, how settings were validated, and how administrative decisions were reasoned through.

---

### Overview

This execution documents hands-on administration of Active Directory Domain Services (AD DS) performed in the context of extending an existing `contoso.com` domain to support a new Paris office. Acting as the IT systems administrator responsible for the Paris office rollout, I deployed a second domain controller, configured AD site and subnet topology, organized and delegated administration of Paris identities, performed bulk user lifecycle management using PowerShell, strengthened domain password controls, enabled directory recovery, and applied Group Policy security hardening.

The workflow is organized into five operational phases:

* **Domain Controller and Topology Operations** — creating a Paris AD site, associating the `172.16.1.0/24` subnet, installing the AD DS server role on `DC2`, promoting `DC2` as an additional domain controller, validating replication, and transferring the RID Master FSMO role.
* **Paris Directory Objects and Delegation** — creating the Paris OU, creating the `Paris Admins` Universal security group, creating the `ParisContractor` account with expiration and non-delegation protection, and delegating scoped password-management rights over the Paris OU.
* **Bulk User Lifecycle Administration with PowerShell** — querying users by City attribute, bulk-moving them into the Paris OU, disabling their accounts, requiring password changes at next logon, and verifying the resulting state.
* **Password Policy and Recovery Configuration** — setting the domain-wide minimum password length via the Default Domain Policy, creating a Fine-Grained Password Policy for Domain Admins, and enabling the Active Directory Recycle Bin.
* **Group Policy Security Configuration** — restricting NTLM authentication domain-wide via the Default Domain Controllers Policy, creating and linking `GPO1` to the Paris OU, configuring failed-logon auditing, and denying `Paris Admins` the right to log on as a service.

Each phase builds on the previous one, and the sequence was designed to reflect how a real branch-office deployment would be approached: establish the network and infrastructure first, then structure the directory, then manage identities, then enforce policy and harden security.

> 👉 **Follow the execution walkthrough first**
> Begin with `workflow-execution.md` to see how each phase was executed step by step, including PowerShell commands, console navigation, and validation results.

> 👉 **Review analytical reasoning and conceptual notes**
> Move to `analyst-notes.md` to understand the reasoning behind each configuration decision — why a second DC matters, how FSMO roles work, why OUs are a security decision and not just an organizational one, and how GPO scope differs from security group targeting.

> 👉 **Review tooling and command usage details**
> See `tool-usage-notes.md` for detailed explanations of every tool and command used throughout the workflow.

> 👉 **See what each file contains in full detail**
> Review the **[Repository Structure & Supporting Documents](#repository-structure--supporting-documents)** section below.

---

### How to Navigate This Execution

Documentation is separated into focused components to reflect how Active Directory administration work is commonly documented in IAM and systems administration contexts.

If you want to follow the execution step by step, start with:

**`workflow-execution.md`**

This file contains the structured walkthrough showing how the domain controller was deployed, how the Paris site and subnet were configured, how the Paris OU and identity objects were created and delegated, how bulk PowerShell user administration was performed and verified, how password policies and the Recycle Bin were configured, and how Group Policy security settings were applied.

If you want to understand the reasoning behind the process, review:

**`analyst-notes.md`**

This file explains the major decision points and conceptual takeaways across all five phases — FSMO transfer vs. seizure, why OUs scope delegation, how bulk PowerShell operations should be previewed before execution, why FGPP precedence works in reverse, why the Recycle Bin must be enabled proactively, why NTLM restriction matters, and how GPO Computer Configuration settings interact with security group targeting.

If you want to understand tool usage, review:

**`tool-usage-notes.md`**

This file explains how Server Manager, the AD DS Configuration Wizard, Active Directory Users and Computers, Active Directory Administrative Center, Active Directory Sites and Services, the Group Policy Management Console, Windows PowerShell, repadmin, and netdom were each used throughout the workflow.

---

### Repository Structure & Supporting Documents

All execution outputs are separated into focused documents to reflect operational identity and access management documentation practices.

| File / Folder | Purpose | Contents and Focus |
| --- | --- | --- |
| `workflow-execution.md` | Structured administration walkthrough documenting all five operational phases. | Documents DC promotion, site/subnet configuration, FSMO transfer, Paris OU and identity management, bulk PowerShell user administration, password policy configuration, and GPO security hardening. |
| `images/` | Visual evidence supporting administrative actions and validation steps. | Contains screenshots of Server Manager, ADUC, ADAC, Active Directory Sites and Services, Group Policy Management Console/Editor, and PowerShell output throughout the workflow. |
| `README.md` | Execution overview and operational summary. | Provides structured explanation of workflow objectives, IAM relevance, repository navigation, and intended use. |
| `analyst-notes.md` | Analyst observations, conceptual explanations, and learning notes. | Documents administrative reasoning, decision-making, and configuration interpretation used throughout the workflow. |
| `tool-usage-notes.md` | Technical reference for tools and commands used throughout the workflow. | Covers Server Manager, ADUC, ADAC, Active Directory Sites and Services, GPMC, PowerShell, repadmin, netdom, and common mistakes. |

---

### Environment, Data Sources, and Tools

The execution focuses on Active Directory Domain Services administration against an existing domain extended for a Paris office rollout.

#### Environment and Execution Scope (At a Glance)

| Area | Details |
| --- | --- |
| **Environment Type** | Hosted Windows Server lab environment |
| **Domain** | `contoso.com` |
| **Existing Domain Controller** | `DC1` (initial RID Master holder) |
| **New Domain Controller** | `DC2` (promoted during the workflow; assigned to Paris site) |
| **Paris Site Subnet** | `172.16.1.0/24` |
| **Execution Platform** | Windows Server (Server Manager, ADUC, ADAC, GPMC, PowerShell) |
| **Primary Tools** | Server Manager, Active Directory Domain Services Configuration Wizard, Active Directory Users and Computers, Active Directory Administrative Center, Active Directory Sites and Services, Group Policy Management Console, Windows PowerShell, repadmin, netdom |
| **Operational Focus** | AD DS administration, identity lifecycle management, bulk PowerShell automation, and directory security hardening |

#### Data Sources, Evidence, and Analysis Techniques

| Area | Details |
| --- | --- |
| **Domain Controller Operations** | `DC2` promoted as additional writable DC; assigned to Paris site; RID Master transferred from `DC1` to `DC2` |
| **AD Site and Subnet** | `Paris` site created; `172.16.1.0/24` subnet associated with Paris site |
| **Organizational Unit Created** | `Paris` |
| **User Account Created** | `ParisContractor` (expiration: December 31, 2027; non-delegable credential flag enabled) |
| **Security Group Created** | `Paris Admins` (Universal scope) |
| **Delegation Configured** | `Paris Admins` delegated password reset and force-change rights over the Paris OU |
| **Bulk User Administration** | Paris users (City = `Paris`) moved to Paris OU, disabled, and flagged for password change at next logon via PowerShell |
| **Domain Password Policy** | Default Domain Policy — minimum password length set to `12` characters |
| **Fine-Grained Password Policy** | `Domain Admins 16 Character Policy` — precedence `1`, minimum length `16`, applied to Domain Admins |
| **AD Recycle Bin** | Enabled via Active Directory Administrative Center |
| **NTLM Restriction** | Default Domain Controllers Policy — "Restrict NTLM: NTLM authentication in this domain" set to Deny all |
| **Advanced Audit Policy** | `GPO1` linked to Paris OU — Audit Logon (Failure) configured |
| **User Rights Assignment** | `GPO1` — "Deny log on as a service" configured for `Paris Admins` |

---

### Intended Use

This execution is intended to demonstrate Active Directory Domain Services administration across domain controller deployment, identity lifecycle management, scoped delegation, bulk PowerShell automation, password policy enforcement, and Group Policy security hardening.

The workflow reflects how an administrator or IAM-focused analyst may answer questions such as:

* How is a member server promoted to a domain controller, and what distinguishes role installation from promotion?
* How are AD sites and subnets used to represent physical office locations and influence authentication behavior?
* Why would a FSMO role transfer fail through the graphical interface, and how should it be handled correctly?
* How are OUs used to scope delegation, and why is that preferable to granting broad administrative rights?
* How is a temporary contractor account properly configured to limit credential exposure?
* How can PowerShell be used to safely perform bulk identity lifecycle changes, and what does proper verification look like?
* How does a domain-wide password policy differ from a Fine-Grained Password Policy, and when is each appropriate?
* Why must the Active Directory Recycle Bin be enabled before a deletion occurs?
* Why is NTLM restriction considered a meaningful authentication hardening step?
* How does GPO Computer Configuration targeting work, and how does it differ from the security principals named inside a policy setting?

This process supports later work involving identity and access management, directory services administration, Group Policy design, PowerShell-based identity automation, and identity-focused security hardening.

---

### Relevance to Security Operations and Identity and Access Management

Active Directory remains the backbone of identity and access management in the majority of enterprise Windows environments. Domain controller configuration, OU design, delegation, and Group Policy security settings are directly relevant to how identity is managed, protected, and audited in production environments.

This workflow demonstrates the administrative side of identity security: deploying domain controllers for redundancy and branch-office authentication, structuring the directory with OUs and groups, applying least-privilege delegation instead of blanket administrative rights, automating bulk identity changes safely with PowerShell, enforcing strong and role-appropriate password policies, and hardening authentication and auditing settings through Group Policy.

Together, these activities support:

* Active Directory domain controller administration and FSMO role management,
* AD site topology configuration and replication validation,
* identity lifecycle management (creation, delegation, bulk modification, disablement),
* least-privilege delegation and access control,
* PowerShell-based identity automation with preview-before-modify practices,
* password policy enforcement and privileged account hardening,
* legacy authentication protocol restriction (NTLM),
* audit policy configuration for identity-related security monitoring,
* Group Policy-driven security configuration scoped to a specific OU.

This workflow demonstrates that Active Directory administration is not limited to creating accounts — it spans infrastructure operations, directory structure, delegated administration, automated lifecycle management, policy enforcement, and the layered security controls that make an AD environment resilient against common attack techniques.

---

If you are reviewing this as part of my cybersecurity portfolio: this execution is intended to demonstrate Active Directory Domain Services administration, identity lifecycle management, least-privilege delegation, PowerShell-based bulk identity automation, password policy configuration, directory security hardening, and professional documentation aligned with IAM, systems administration, and identity-focused security operations workflows.