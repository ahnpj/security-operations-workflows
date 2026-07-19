# Microsoft Entra ID — Identity Administration, Role-Assignable Groups, Security Configuration Documentation, and Conditional Access Analysis

* **Category:** Identity and Access Management
* **Primary Operational Focus:** Cloud identity provisioning, directory role assignment, group-based role management, password protection and self-service password reset review, Conditional Access named location documentation, and Conditional Access What If analysis in Microsoft Entra ID
* **Operational Objectives Demonstrated:** Cloud Identity Provisioning, Disabled Account Creation, Individual License Assignment, Directory Role Assignment, Business-to-Business Guest Invitation, Role-Assignable Security Group Creation, Group-Based Directory Role Assignment, Group Owner and Member Management, Password Protection Review, Authentication Method Review, Self-Service Password Reset Documentation, Named Location Review, Conditional Access What If Analysis, Policy Scope Review, Current-State Documentation
* **Primary Data Sources:** Microsoft Entra admin center (`entra.microsoft.com`), Microsoft 365 admin center (`admin.microsoft.com`), lab-provided Configuration Data Editor

> **Terminology used:**
> **Workflows** refer to repeatable Microsoft Entra ID administration tasks such as user provisioning, role assignment, group management, and Conditional Access evaluation.
> **Executions** refer to the hands-on administration performed using the Microsoft Entra admin center, Microsoft 365 admin center, and lab-provided desktop editor.
> **Writeups** document configuration steps, analyst observations, security implications, and administrative reasoning.

---

### Overview

This execution documents hands-on Microsoft Entra ID identity administration performed across five operational phases: user and guest administration, role-assignable group management, password security documentation, Conditional Access named location documentation, and Conditional Access What If analysis.

The workflow is organized into five operational phases:

* **User Administration** — creating a disabled internal user account (`Maya Chen`) with job title, department, and usage location configured; assigning a Microsoft 365 E3 license; assigning the Billing Administrator directory role to an existing user (`Omar Rahman`); and inviting an external business-to-business guest (`Evelyn Brooks`).
* **Group and Role Administration** — creating a role-assignable security group (`InsightsOps`) with `Marcus Lee` as a member; assigning the Reports Reader directory role to the group; and adding `Marcus Lee` as owner and `Priya Nair` as member of an existing security group (`Security-Reviewers`).
* **Password Security Documentation** — reviewing smart lockout thresholds and duration, custom banned password configuration, on-premises Active Directory password protection settings, self-service password reset method requirements, and authentication method policy states; recording findings in a structured documentation utility without modifying the tenant.
* **Conditional Access Named Location Documentation** — identifying country-based named locations, confirming selected countries and trust settings, and recording findings without modifying the tenant.
* **Conditional Access What If Analysis** — configuring a hypothetical sign-in scenario, evaluating which Conditional Access policies would and would not apply, interpreting policy state and user scope, and documenting the findings.

> **Sanitization Note:** All environment-specific names, user principal names, tenant domains, email addresses, company names, group names, countries, IP addresses, policy names, and configuration values in the write-up are sanitized substitutions. Official Microsoft product names, portal names, feature names, and built-in role names are retained for technical accuracy.

> 👉 **Follow the execution walkthrough first**
> Begin with `workflow-execution.md` to see how each phase was executed step by step, including portal navigation, configuration settings, and verification results.

> 👉 **Review analytical reasoning and conceptual notes**
> Move to `analyst-notes.md` to understand the reasoning behind each decision — why accounts are disabled rather than deleted, how role-assignable groups change the privilege model, what smart lockout protects against, why named locations don't enforce access on their own, and how Report-only differs from an enforced Conditional Access policy.

> 👉 **Review tooling and portal usage details**
> See `tool-usage-notes.md` for detailed explanations of the Microsoft Entra admin center, Microsoft 365 admin center, and Configuration Data Editor, including specific navigation paths and why certain operations live in different portals.

> 👉 **See what each file contains in full detail**
> Review the **[Repository Structure & Supporting Documents](#repository-structure--supporting-documents)** section below.

---

### How to Navigate This Execution

If you want to follow the execution step by step, start with:

**`workflow-execution.md`**

This file contains the structured walkthrough showing how each identity was provisioned, how roles were assigned directly and through groups, how authentication and password security settings were reviewed and documented, how named locations were confirmed, and how Conditional Access What If was used to evaluate policy applicability.

If you want to understand the reasoning behind the process, review:

**`analyst-notes.md`**

This file explains the major decision points and conceptual takeaways across all five phases — the difference between a license, a directory role, a group membership, and a Conditional Access policy; why role assignability must be set at group creation time; what smart lockout actually prevents and what it doesn't; the limitations of country-based named location detection; and how Report-only policies behave during What If evaluation.

If you want to understand tool and portal usage, review:

**`tool-usage-notes.md`**

This file explains how the Microsoft Entra admin center and Microsoft 365 admin center were each used throughout the workflow, including where specific operations live, why licensing lives outside the Entra portal, and how to navigate to password protection, named locations, and Conditional Access What If.

---

### Repository Structure & Supporting Documents

| File / Folder | Purpose | Contents and Focus |
| --- | --- | --- |
| `workflow-execution.md` | Structured administration walkthrough documenting all five operational phases. | Documents user and guest provisioning, role assignment, group-based role management, password security review, named location documentation, and Conditional Access What If analysis. |
| `images/` | Visual evidence supporting administrative actions and documentation steps. | Contains screenshots of the Microsoft Entra admin center, Microsoft 365 admin center, and Configuration Data Editor throughout the workflow. |
| `README.md` | Execution overview and operational summary. | Provides structured explanation of workflow objectives, IAM relevance, repository navigation, and intended use. |
| `analyst-notes.md` | Analyst observations, conceptual explanations, and learning notes. | Documents administrative reasoning, decision-making, and configuration interpretation used throughout the workflow. |
| `tool-usage-notes.md` | Technical reference for portals and navigation paths used throughout the workflow. | Covers the Microsoft Entra admin center, Microsoft 365 admin center, and Configuration Data Editor, including where each operation lives and why. |

---

### Environment, Data Sources, and Tools

#### Environment and Execution Scope (At a Glance)

| Area | Details |
| --- | --- |
| **Environment Type** | Guided lab environment with a Microsoft Entra ID tenant |
| **Portals Used** | Microsoft Entra admin center (`entra.microsoft.com`), Microsoft 365 admin center (`admin.microsoft.com`), Configuration Data Editor (lab-provided desktop application) |
| **Required Role** | Privileged role (lab-provided; more permissive than production minimum) |
| **Execution Platform** | Web-based admin portals and local documentation utility |
| **Primary Tools** | Microsoft Entra admin center, Microsoft 365 admin center, Configuration Data Editor |
| **Operational Focus** | Identity lifecycle management, group-based role assignment, authentication security review, Conditional Access analysis, and current-state documentation |

#### Data Sources, Evidence, and Analysis Techniques (Sanitized)

| Area | Details |
| --- | --- |
| **Tenant Domain** | `lakeshorelabs.onmicrosoft.com` |
| **Internal User Created** | `Maya Chen` (MayaC) — disabled, cloud-only identity |
| **Profile Configured** | Job title: Financial Analyst; Department: Treasury; Usage location: Canada |
| **License Assigned** | Microsoft 365 E3 → `Maya Chen` via Microsoft 365 admin center |
| **Directory Role Assigned** | Billing Administrator → `Omar Rahman` (active, permanent) |
| **External Guest Invited** | `Evelyn Brooks` (e.brooks@woodgrovebank.com) — Advisor, Woodgrove Bank |
| **Role-Assignable Group Created** | `InsightsOps` (Assigned membership; role assignment enabled) |
| **Group Member Added** | `Marcus Lee` → `InsightsOps` |
| **Directory Role Assigned to Group** | Reports Reader → `InsightsOps` |
| **Existing Group Updated** | `Security-Reviewers`: `Marcus Lee` added as owner; `Priya Nair` added as member |
| **Password Protection Reviewed** | Lockout threshold: 12; Duration: 180s; Custom banned word: Orchid; On-premises: Audit mode |
| **Password Reset Reviewed** | 2 methods required; Microsoft Authenticator, SMS, Email OTP enabled |
| **Named Locations Reviewed** | Portugal, Chile — no unknown countries included; not marked trusted |
| **What If Scenario** | Noah Bennett → SharePoint Online; iOS; Browser; Medium risk; Canada |
| **Policies That Apply** | CA-02-Protect-Mobile (On), CA-03-Compliant-Devices (Report-only), CA-04-Session-Protection (Report-only) |
| **Policy That Does Not Apply** | CA-01-MFA-Contractors (On) — user/group scope mismatch |
| **Documentation Utility** | Password Protection, Password Reset, Named Locations, Conditional Access Users, What If sections saved |

---

### Intended Use

This execution is intended to demonstrate Microsoft Entra ID identity and access management administration across user lifecycle management, guest collaboration, group-based role assignment, authentication security configuration review, and Conditional Access policy analysis.

The workflow reflects how an IAM analyst, cloud identity administrator, or identity-focused security operations professional may answer questions such as:

* Why might a user account exist in Entra ID but be unable to authenticate?
* Why is usage location required before a Microsoft 365 license can be assigned?
* What is the difference between a license, a directory role, a group membership, and a Conditional Access policy?
* What is the difference between Active and Eligible role assignment?
* Why must role assignability be enabled during group creation rather than added later?
* How does a directory role assigned to a group propagate to the group's members?
* What does smart lockout actually prevent, and what doesn't it prevent?
* Why is on-premises Active Directory password protection set to Audit before Enforced?
* What is the difference between an authentication method being enabled and a user having registered it?
* What does a named location do on its own, and what requires a Conditional Access policy?
* What does the Conditional Access What If tool evaluate, and what doesn't it test?
* What is the difference between a policy that is On and one that is Report-only?
* Why might a Conditional Access policy not apply to a specific user?

This process supports later work involving identity lifecycle management, least-privilege role administration, group-based access governance, authentication security hardening, Conditional Access policy design, and identity-focused security operations in Microsoft cloud environments.

---

### Relevance to Security Operations and Identity and Access Management

Microsoft Entra ID is the identity control plane for Microsoft 365, Azure, and enterprise SaaS applications. How it is configured — who holds administrative roles, how groups are structured, what authentication methods are enabled, and how Conditional Access policies are scoped — directly determines the organization's exposure to unauthorized access, privilege escalation, and identity-based attacks.

This workflow demonstrates that Entra ID administration is not limited to creating user accounts. It spans identity lifecycle management, group-based authorization, least-privilege role delegation, authentication security review, reusable location condition management, and sign-in policy analysis.

Together, these activities support:

* cloud identity provisioning with correct attribute configuration,
* disabled-account management for pre-provisioning and suspension scenarios,
* least-privilege directory role assignment using active and permanent assignments,
* business-to-business guest collaboration with profile enrichment,
* group-based role assignment as a scalable alternative to direct user role assignment,
* owner and member separation for controlled group administration,
* smart lockout and custom banned password configuration review,
* on-premises Active Directory password protection assessment,
* self-service password reset and authentication method policy review,
* Conditional Access named location identification and trust configuration review,
* Conditional Access What If analysis for policy validation and troubleshooting,
* read-only current-state documentation without introducing tenant changes.

This workflow demonstrates that strong identity security depends not only on creating accounts correctly, but on understanding the full configuration that governs how those accounts authenticate, what they can administer, and which policies evaluate their sign-in context.

---

If you are reviewing this as part of my cybersecurity portfolio: this execution is intended to demonstrate Microsoft Entra ID identity and access management administration, group-based role assignment, authentication security configuration review, Conditional Access analysis, and professional documentation aligned with cloud IAM, identity security operations, and least-privilege governance workflows.