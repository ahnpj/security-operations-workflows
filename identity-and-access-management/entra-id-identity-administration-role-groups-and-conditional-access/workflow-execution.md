# Microsoft Entra ID — Identity Administration, Role-Assignable Groups, Security Configuration Documentation, and Conditional Access Analysis

This workflow demonstrates hands-on identity and access management operations in **Microsoft Entra ID**, including user provisioning, license assignment, directory role assignment, external guest invitation, security group management, group-based role assignment, password protection review, self-service password reset documentation, Conditional Access named location review, and Conditional Access What If analysis.

The main tools used are: the **Microsoft Entra admin center** (`entra.microsoft.com`), the **Microsoft 365 admin center** (`admin.microsoft.com`), and a lab-provided **Configuration Data Editor** desktop application used to document the tenant’s current settings.

> **Sanitization Note**
>
> All environment-specific names, user principal names, tenant domains, email addresses, company names, group names, countries, IP addresses, policy names, and configuration values in this write-up are sanitized substitutions. Official Microsoft product names, portal names, feature names, and built-in role names are retained where necessary for technical accuracy.

---

### Overview

This project focused on five operational areas of Microsoft Entra ID administration, performed in sequence against the same Entra tenant.

1. The first portion involved **User Administration** — creating a disabled internal user account, assigning that user a Microsoft 365 license, assigning an active directory role to an existing user, and inviting an external business-to-business guest account.

2. The second portion involved **Group Administration and Role Assignment** — creating a role-assignable security group, adding a member to it, assigning the built-in Reports Reader role to the group, and updating the owner and member lists of an existing security group.

3. The third portion involved **Password Protection and Self-Service Password Reset Documentation** — reviewing the tenant’s smart lockout, banned password, on-premises password protection, and authentication method settings, then recording the requested values in a local documentation utility.

4. The fourth portion involved **Conditional Access Named Location Documentation** — identifying country-based named locations, confirming their selected countries and trust settings, and recording them in the documentation utility.

5. The fifth portion involved **Conditional Access What If Analysis and Policy Assignment Documentation** — simulating a sign-in scenario, determining which Conditional Access policies would and would not apply, interpreting policy state, reviewing each policy’s user scope, and documenting the findings.

This workflow demonstrates that Entra ID administration is not limited to creating accounts. It includes identity lifecycle management, least-privilege role assignment, group-based authorization, authentication configuration review, access policy analysis, and operational documentation.

> **Workflow vs Execution vs Writeup (Terminology Used Here)**
> - **Workflows** refer to repeatable Entra ID administration tasks such as user provisioning, role assignment, group management, and Conditional Access evaluation.
> - **Executions** refer to the hands-on administration performed in the Microsoft Entra admin center, Microsoft 365 admin center, and lab-provided desktop editor.
> - **Writeups** document configuration steps, administrative reasoning, security implications, and analyst observations.

> 👉 For a **detailed, step-by-step walkthrough of how this workflow was executed**, see the **[Step-by-Step Execution](#step-by-step-execution)** section below.

---

### Purpose and Analyst Focus

#### ▶ Purpose

The purpose of this workflow is to demonstrate practical Microsoft Entra ID administration across user management, license management, directory role assignment, guest collaboration, group management, authentication configuration review, Conditional Access documentation, and policy simulation.

The user administration portion focused on creating a cloud-only internal identity with the correct profile attributes and a disabled sign-in state. This matters because organizations frequently pre-provision accounts before a person’s start date, suspend accounts during investigations, or create identities that should exist in the directory without being allowed to authenticate yet.

The license assignment portion focused on assigning a Microsoft 365 subscription license through the Microsoft 365 admin center. This matters because the identity object is managed in Entra ID, while the subscription product that enables Microsoft 365 services is managed through Microsoft 365 licensing. Understanding where each responsibility lives prevents administrators from searching the wrong portal or assuming the account automatically receives service access when it is created.

The directory role assignment portion focused on granting an existing user the **Billing Administrator** role as an active, permanent assignment. This matters because a directory role authorizes administrative actions within the tenant. It is not the same as a group membership, application permission, or Azure resource role. Assigning a role changes what the identity can administer and therefore requires careful attention to scope, assignment type, and duration.

The external user invitation portion focused on creating a business-to-business guest identity. This matters because organizations routinely collaborate with consultants, vendors, auditors, and partner organizations. Entra ID can represent those external people as guest objects without creating a normal internal employee identity for them.

The group administration portion focused on creating a security group that could receive a Microsoft Entra role. This matters because group-based role assignment allows administrators to manage privileged access by changing group membership rather than assigning the same directory role repeatedly to individual users.

The password protection and self-service password reset portion focused on reading the tenant’s current security configuration and documenting it without changing it. This matters because administrators and auditors frequently need to gather configuration evidence, compare current settings against standards, or populate control documentation without modifying production settings.

The Conditional Access portion focused on understanding how Microsoft Entra evaluates sign-in context. Conditional Access combines signals such as the user, application, device platform, network location, client type, and risk level, then evaluates policies to decide whether access should be allowed, blocked, challenged, or restricted.

#### ▶ Analyst Focus

The analyst focus is on understanding not only which buttons were selected, but what each object and setting represents inside the identity platform.

This includes:

- understanding the difference between an internal member identity and an external guest identity,
- understanding why a user can exist in Entra ID while sign-in is disabled,
- understanding why a usage location is required before many Microsoft 365 licenses can be assigned,
- understanding the difference between a license, a directory role, a group membership, and a Conditional Access policy,
- understanding active versus eligible role assignment,
- understanding why role-assignable groups must be intentionally created and why the setting cannot be added later,
- understanding how owners and members have different responsibilities in a group,
- understanding smart lockout, banned passwords, and password protection for on-premises Active Directory Domain Services,
- understanding how self-service password reset depends on enabled authentication methods,
- understanding that a named location is a reusable condition and does not enforce access by itself,
- understanding how the Conditional Access What If tool evaluates a hypothetical sign-in,
- understanding the difference between policies that are On, Report-only, and not applicable,
- understanding why documentation must reflect the current configuration rather than assumptions or default values.

The goal is to understand each concept well enough to explain why an organization would perform the task, what security or operational need it addresses, and what could go wrong if it is performed incorrectly.

---

### What This Workflow Demonstrates

This workflow demonstrates how to:

- Create a cloud-only member user in Microsoft Entra ID.
- Configure the user’s user principal name, display name, job title, department, usage location, and account-enabled state.
- Assign a Microsoft 365 license to an individual through the Microsoft 365 admin center.
- Assign the Billing Administrator directory role as an active and permanently assigned role.
- Provide a required business justification when assigning a privileged role.
- Invite an external guest user and populate business profile attributes.
- Create an Assigned-membership security group.
- Enable Microsoft Entra role assignment on a security group during creation.
- Add a member to a role-assignable group.
- Assign the Reports Reader role to a security group.
- Understand how group membership grants the role to the group’s members.
- Add an owner and a member to an existing security group.
- Review smart lockout and custom banned password settings.
- Review password protection settings for Windows Server Active Directory.
- Review self-service password reset requirements.
- Review enabled and disabled authentication method policies.
- Identify country-based Conditional Access named locations.
- Confirm whether named locations include unknown countries or are marked trusted.
- Use Conditional Access What If to simulate a sign-in.
- Interpret policies that will apply, policies that will not apply, and the reason for non-application.
- Distinguish active enforcement from Report-only evaluation.
- Review each Conditional Access policy’s included users.
- Record configuration evidence in a structured local editor without changing the tenant.

This workflow also demonstrates the relationship between three administrative surfaces:

- the **Microsoft Entra admin center**, where identities, groups, roles, authentication settings, named locations, and Conditional Access policies are managed;
- the **Microsoft 365 admin center**, where subscription licenses are assigned;
- the **Configuration Data Editor**, where current-state findings are documented for the assessment.

---

### Investigation and Security Operations Relevance

Microsoft Entra ID is the identity control plane for Microsoft 365, Azure, software-as-a-service applications, and many enterprise authentication workflows. Misconfiguration can result in excessive privilege, unauthorized access, weak password defenses, incomplete recovery options, unmanaged guest access, or Conditional Access policies that do not protect the users administrators expect them to protect.

This workflow can help answer questions such as:

- Was the user account intentionally disabled, or is a sign-in failure caused by another issue?
- Does the user have the required Microsoft 365 license?
- Which administrative role was assigned to the user, and is it active or merely eligible?
- Is a role inherited through a group rather than assigned directly?
- Which users receive a directory role because they belong to a role-assignable group?
- Who owns an existing group, and who is only a member?
- Are custom banned passwords enforced?
- What smart lockout threshold and duration are configured?
- Is on-premises Active Directory password protection running in Audit or Enforced mode?
- How many authentication methods must a user provide for self-service password reset?
- Which authentication methods are available to users?
- Which country-based named locations exist?
- Are any named locations trusted?
- Which Conditional Access policies would evaluate for a given user, application, device, location, and risk level?
- Why did a policy fail to apply?
- Is a matching policy actively enforced or only collecting Report-only results?
- Is the policy assigned to one user, selected users and groups, or all users?

The table below summarizes the role of each configuration area in this workflow:

| Area | What Was Configured or Reviewed | Why It Matters |
|---|---|---|
| Internal User | `Maya Chen` created with sign-in disabled | Pre-provisions a directory identity without allowing authentication |
| Individual License | Microsoft 365 E3 assigned to `Maya Chen` | Enables licensed Microsoft 365 services for the user |
| Directory Role | Billing Administrator assigned to `Omar Rahman` | Grants tenant-level billing administration capability |
| External Guest | `Evelyn Brooks` invited from a partner organization | Supports controlled business-to-business collaboration |
| Role-Assignable Group | `InsightsOps` created with `Marcus Lee` as a member | Allows a directory role to be managed through group membership |
| Group Role Assignment | Reports Reader assigned to `InsightsOps` | Grants report-reading permissions to current and future members |
| Existing Group | `Marcus Lee` added as owner; `Priya Nair` added as member | Separates group management responsibility from ordinary membership |
| Password Protection | Smart lockout and banned password settings reviewed | Reduces password guessing and weak-password use |
| Password Reset | Reset requirement and authentication methods reviewed | Determines how users prove identity during password recovery |
| Named Locations | `Portugal` and `Chile` reviewed | Creates reusable geography conditions for Conditional Access |
| Conditional Access What If | Hypothetical sign-in evaluated | Predicts policy applicability without performing a real sign-in |
| Policy Assignments | User scope reviewed for four policies | Confirms which identities each policy actually targets |
| Documentation Utility | Current values recorded and saved | Produces evidence without altering the tenant |

---

### Environment and Execution Context

This section documents the tools, tenant, objects, and execution boundaries used during the workflow.

**Note:** Each section is collapsible. Click the ▶ arrow to expand and view details.

<details>
<summary><strong>▶ Environment & Platform</strong><br>
</summary><br>

The workflow was performed against a Microsoft Entra tenant in a guided lab environment. Administrative actions were performed through the following interfaces:

```text
Microsoft Entra admin center    — entra.microsoft.com
Microsoft 365 admin center      — admin.microsoft.com
Configuration Data Editor       — lab-provided Windows desktop application
```

The lab account had more administrative privilege than every individual task required. This is common in training environments so the learner is not blocked by role limitations.

In a production environment, administrators should use the least-privileged role that allows the required task. For example, a person who only manages users should not automatically receive Global Administrator.

</details>

<details>
<summary><strong>▶ Sanitized Tenant and Identity Values</strong><br>
</summary><br>

The write-up uses the following sanitized tenant and identity values:

| Object Type | Sanitized Value |
|---|---|
| Tenant domain | `lakeshorelabs.onmicrosoft.com` |
| New internal user | `Maya Chen` |
| New user UPN | `MayaC@lakeshorelabs.onmicrosoft.com` |
| Existing role recipient | `Omar Rahman` |
| External guest | `Evelyn Brooks` |
| External email | `e.brooks@woodgrovebank.com` |
| Role-assignable group | `InsightsOps` |
| Existing security group | `Security-Reviewers` |
| Group member / owner | `Marcus Lee` |
| Existing group member added | `Priya Nair` |
| Conditional Access test user | `Noah Bennett` |

These values are intentionally different from the lab’s original identifiers.

</details>

<details>
<summary><strong>▶ Security Configuration Values Documented</strong><br>
</summary><br>

| Configuration Area | Sanitized Current-State Values |
|---|---|
| Smart lockout threshold | `12` failed attempts |
| Smart lockout duration | `180` seconds |
| Custom banned password list | `Orchid` |
| Enforce custom banned list | Yes |
| On-premises password protection | Enabled |
| On-premises mode | Audit |
| Password reset methods required | `2` |
| Passkey (FIDO2) | No |
| Microsoft Authenticator | Yes |
| SMS | Yes |
| Temporary Access Pass | No |
| Voice call | No |
| Email OTP | Yes |
| Country-based named locations | `Portugal`, `Chile` |
| Include unknown countries/regions | No |
| Trusted named locations | No |

</details>

<details>
<summary><strong>▶ Conditional Access Scenario</strong><br>
</summary><br>

The Conditional Access What If simulation used the following sanitized scenario:

| Signal | Value |
|---|---|
| User | `Noah Bennett` |
| Target resource | Microsoft SharePoint Online |
| Device platform | iOS |
| Client application | Browser |
| Sign-in risk | Medium |
| IP address | `203.0.113.25` |
| Country/region | Canada |

The IP address `203.0.113.25` is from an address range reserved for documentation and examples.

</details>

<details>
<summary><strong>▶ Conditional Access Policies Reviewed</strong><br>
</summary><br>

| Policy | State | User Scope | What If Result |
|---|---|---|---|
| `CA-01-MFA-Contractors` | On | Marcus Lee | Does not apply |
| `CA-02-Protect-Mobile` | On | Noah Bennett | Applies |
| `CA-03-Compliant-Devices` | Report-only | All users | Applies |
| `CA-04-Session-Protection` | Report-only | All users | Applies |

The exact policy names are sanitized. The important concepts are the policy state, user scope, matching conditions, and evaluation result.

</details>

<details>
<summary><strong>▶ Tooling Used</strong><br>
</summary><br>

The tools and portals used during execution included:

- **Microsoft Entra admin center** (`entra.microsoft.com`) — used for users, guest invitations, groups, directory roles, authentication settings, password protection, password reset, named locations, Conditional Access policies, and What If analysis.
- **Microsoft 365 admin center** (`admin.microsoft.com`) — used for Microsoft 365 license assignment.
- **Configuration Data Editor** — used to record password protection, password reset, named location, Conditional Access user assignment, and What If findings.

</details>

<details>
<summary><strong>▶ Workflow Scope</strong><br>
</summary><br>

This workflow focused on graphical administration and current-state documentation.

In-scope activities included:

- cloud-only user creation,
- disabled-account provisioning,
- individual license assignment,
- directory role assignment,
- guest invitation,
- Assigned-membership security group creation,
- role-assignable group creation,
- group-based directory role assignment,
- owner and member management,
- password protection review,
- password reset and authentication method review,
- named location review,
- Conditional Access What If analysis,
- Conditional Access user-scope review,
- structured documentation of findings.

Out-of-scope activities included:

- writing or deploying PowerShell scripts,
- using Microsoft Graph,
- changing the observed password protection settings,
- changing self-service password reset policy,
- modifying named locations,
- creating or modifying Conditional Access policies,
- testing a real user sign-in,
- reviewing sign-in logs after the simulation,
- configuring Privileged Identity Management approval workflows,
- configuring dynamic group membership,
- synchronizing identities from on-premises Active Directory,
- assigning Azure resource roles.

</details>

<details>
<summary><strong>▶ Workflow Map (High-Level)</strong><br>
</summary><br>

**Phase 1 — User Administration**
1. Create `Maya Chen` as a disabled internal user.
2. Assign a Microsoft 365 E3 license.
3. Assign Billing Administrator to `Omar Rahman`.
4. Invite external guest `Evelyn Brooks`.

**Phase 2 — Group and Role Administration**
5. Create role-assignable security group `InsightsOps`.
6. Add `Marcus Lee` as a member.
7. Assign Reports Reader to `InsightsOps`.
8. Add `Marcus Lee` as owner of `Security-Reviewers`.
9. Add `Priya Nair` as a member of `Security-Reviewers`.

**Phase 3 — Password Security Documentation**
10. Review password protection settings.
11. Review password reset requirements.
12. Review authentication method policies.
13. Record and save the findings in the Configuration Data Editor.

**Phase 4 — Named Location Documentation**
14. Identify country-based named locations.
15. Confirm selected countries and trust settings.
16. Record and save the findings.

**Phase 5 — Conditional Access Analysis**
17. Run the What If simulation.
18. Record policies that apply and do not apply.
19. Review each policy’s user assignment.
20. Record and save Conditional Access findings.

</details>

---

### Step-by-Step Execution

This section documents the workflow in the order the tasks were performed.

**Note:** Each section is collapsible. Click the ▶ arrow to expand and view the detailed steps.

<details>
<summary><strong>▶ Phase 1 — Manage Internal Users, Licenses, Roles, and External Guests</strong><br>
→ provisioning identities, enabling service access, delegating administration, and supporting external collaboration
</summary><br>

This phase focused on four different identity operations:

- creating an internal user,
- assigning a Microsoft 365 license,
- assigning a directory role,
- inviting an external guest.

These actions may look similar because they all involve users, but they change different parts of the environment.

```text
User account        = who the identity is
License             = which Microsoft services the identity can use
Directory role      = what the identity can administer
Guest invitation    = how an external person is represented in the tenant
```

##### 🔷 Phase 1.1 — Create the Maya Chen internal user account

In the **Microsoft Entra admin center**, I navigated to:

```text
Entra ID → Users → All users → + New user → Create new user
```

I configured the account with the following sanitized values:

| Setting | Value |
|---|---|
| User principal name | `MayaC@lakeshorelabs.onmicrosoft.com` |
| Display name | `Maya Chen` |
| Account enabled | No |
| Job title | Financial Analyst |
| Department | Treasury |
| Usage location | Canada |

**What this does:** This creates a cloud-only user object directly inside the Entra tenant. The account is not synchronized from Active Directory Domain Services and is not an external guest.

**What a user object is:** A user object is the directory record that represents a person or service identity. It stores identifiers, profile attributes, group memberships, assigned roles, license relationships, and authentication-related information.

**Why the user principal name matters:** The user principal name, commonly abbreviated as UPN, is the sign-in identifier. It usually resembles an email address, but its primary function is authentication. The UPN prefix and domain together must be unique in the tenant.

**Display name vs. UPN:** The display name is the human-readable label shown in portals and address lists. It does not have to be unique. The UPN is the unique sign-in identity.

**Why the account was disabled:** Setting **Account enabled** to No allows the object to exist while preventing interactive authentication. Common uses include:

- creating an account before a new employee’s start date,
- staging access before onboarding is complete,
- suspending a user during an investigation,
- retaining an object for application or workflow dependencies,
- preparing a service identity before credentials are released.

A disabled account is not the same as a deleted account. The object remains active in the directory and can still hold attributes, group memberships, licenses, and role assignments. It simply cannot successfully authenticate while disabled.

**Why job title and department matter:** These fields improve directory search, reporting, and automation. In larger environments, they may also support dynamic group rules. For example, a dynamic rule could automatically add every user whose Department equals Treasury to a finance group.

**Why usage location matters:** Microsoft uses usage location to determine service availability and licensing eligibility. A license assignment can fail when a user has no usage location because Microsoft cannot determine which regional service components may be provisioned.

After entering the values, I selected:

```text
Review + create → Create
```

I then confirmed that the account appeared in the users list.

##### 🔷 Phase 1.2 — Assign a Microsoft 365 license to Maya Chen

From the user’s Entra profile, the Licenses page displayed a notice that license changes were managed through the Microsoft 365 admin center.

I followed the portal link and navigated to:

```text
Microsoft 365 admin center
Billing → Licenses
```

I selected the sanitized subscription:

```text
Microsoft 365 E3
```

I selected **Assign licenses**, searched for `Maya Chen`, accepted the default service-plan selection, and completed the assignment.

**What this does:** A Microsoft 365 license links the user identity to a purchased subscription entitlement. The user object exists independently from the license, but licensed services such as Exchange Online, SharePoint Online, or Office applications may require the appropriate license before the user can use them.

**Why the Entra and Microsoft 365 portals are different:** Entra ID is the identity directory. Microsoft 365 licensing is part of subscription and product administration. The two systems are integrated, but the administrative ownership is different:

```text
Entra admin center      = manage identity objects and access configuration
Microsoft 365 admin     = manage product subscriptions and license assignment
```

**Why default service plans were left enabled:** A Microsoft 365 license can contain multiple service plans. Unless the task requires disabling a specific service, leaving the defaults avoids creating an incomplete or unexpected license configuration.

**Why no welcome email was sent:** The account was intentionally disabled. Sending a service welcome email would not help the user authenticate and was not required by the task.

**Verification:** The assigned-license count increased, confirming that the product entitlement had been allocated.

##### 🔷 Phase 1.3 — Assign Billing Administrator to Omar Rahman

I returned to the Microsoft Entra admin center and navigated to the existing user:

```text
Entra ID → Users → Omar Rahman → Assigned roles → + Add assignments
```

I selected:

| Setting | Value |
|---|---|
| Role | Billing Administrator |
| Assignment type | Active |
| Permanently assigned | Yes |
| Justification | Required to manage organizational billing operations |

**What a directory role is:** A Microsoft Entra directory role grants permission to administer part of the Entra or Microsoft 365 environment. It is different from an Azure role assigned to a subscription or resource group.

**What Billing Administrator allows:** Billing Administrator is intended for billing-related administration. It provides more limited authority than Global Administrator and demonstrates role-based delegation.

**Why least privilege matters:** Administrative roles should match the person’s responsibilities. Giving a billing employee Global Administrator would provide far more authority than necessary. A specialized role limits the potential impact of compromised credentials or administrator error.

**Active vs. Eligible:**

| Assignment Type | Meaning |
|---|---|
| Active | The role is usable immediately |
| Eligible | The user can activate the role when needed, typically through Privileged Identity Management |

An eligible assignment supports just-in-time privilege. An active assignment keeps the role continuously available.

**Permanent vs. time-bound:** A permanent assignment has no configured expiration. A time-bound assignment ends automatically. Permanent assignments should be used only when the role is continuously required.

**Why justification was required:** Privileged Identity Management can require a justification for privileged assignments. The lab did not prescribe exact wording, so a clear business explanation was entered.

After confirming the settings, I selected **Assign**.

##### 🔷 Phase 1.4 — Invite Evelyn Brooks as an external guest

I navigated to:

```text
Entra ID → Users → + New user → Invite external user
```

I configured the invitation with the following sanitized values:

| Setting | Value |
|---|---|
| Email address | `e.brooks@woodgrovebank.com` |
| Display name | Evelyn Brooks |
| Job title | Advisor |
| Company name | Woodgrove Bank |

I left optional invitation settings at their defaults and completed the invitation.

**What this does:** Entra ID creates a guest object representing an external person. The guest can authenticate using an identity from their home organization or another supported identity provider and then access resources shared from the host tenant.

**Member vs. Guest:**

| User Type | Typical Use |
|---|---|
| Member | Internal employee or internal organizational identity |
| Guest | External consultant, vendor, partner, auditor, or collaborator |

**Why not create a normal internal account:** Creating a separate internal credential for an outside collaborator increases password management and offboarding responsibility. A guest invitation allows the partner to use their existing identity while the host tenant controls access.

**Why profile attributes were populated:** Job title and company name help administrators understand who the guest is, why they are present, and which external organization they represent. This is useful during access reviews.

**What the invitation does not do automatically:** Inviting a guest does not automatically grant application access, group membership, administrative roles, or licenses. Those must be assigned separately when required.

##### 🔷 Phase 1.5 — Phase 1 findings

| Task | Outcome |
|---|---|
| Internal user created | `Maya Chen` created as a cloud-only member |
| Sign-in state | Account created disabled |
| Profile configured | Job title, department, and usage location populated |
| License assigned | Microsoft 365 E3 assigned through Microsoft 365 admin center |
| Role assigned | Billing Administrator assigned actively and permanently to `Omar Rahman` |
| Guest invited | `Evelyn Brooks` invited from an external organization |
| Guest profile enriched | Advisor / Woodgrove Bank attributes recorded |

</details>

<details>
<summary><strong>▶ Phase 2 — Create a Role-Assignable Group and Update Existing Group Membership</strong><br>
→ managing access through groups, assigning a directory role to a group, and distinguishing owners from members
</summary><br>

This phase focused on using groups as access-management objects.

A security group can be used to:

- grant application access,
- grant resource access,
- assign licenses,
- scope Conditional Access,
- receive certain Microsoft Entra directory roles when created as role-assignable.

##### 🔷 Phase 2.1 — Create the InsightsOps role-assignable security group

I navigated to:

```text
Entra ID → Groups → All groups → New group
```

I configured:

| Setting | Value |
|---|---|
| Group type | Security |
| Group name | InsightsOps |
| Microsoft Entra roles can be assigned to the group | Yes |
| Membership type | Assigned |
| Member | Marcus Lee |
| Owners | None required for the task |

**Why Security group was selected:** A Security group is designed for access control. A Microsoft 365 group also provides collaboration resources such as a shared mailbox and SharePoint site, which were not needed.

**What Assigned membership means:** Assigned membership means administrators explicitly add or remove members. Membership does not change automatically based on user attributes.

**Assigned vs. Dynamic membership:**

| Membership Type | Behavior |
|---|---|
| Assigned | Administrators manually manage members |
| Dynamic User | Rules automatically evaluate user attributes |
| Dynamic Device | Rules automatically evaluate device attributes |

**What “Microsoft Entra roles can be assigned to the group” means:** Enabling this setting creates a special role-assignable group. Microsoft Entra can then assign supported directory roles to the group rather than directly to each user.

**Why the confirmation appeared:** The portal warned that role assignability cannot be enabled later for an ordinary group. This is a security boundary. Role-assignable groups receive additional protections because changing their membership can change administrative privilege.

**Why the setting is important:** If the group had been created without role assignment enabled, it would not appear as an eligible principal when assigning the Reports Reader role. The group would have to be recreated correctly.

I confirmed the warning and created the group.

##### 🔷 Phase 2.2 — Assign Reports Reader to InsightsOps

After the group was created, I navigated to:

```text
Entra ID → Roles & admins → Reports Reader → Add assignments
```

I selected `InsightsOps` as the member receiving the role.

I configured:

| Setting | Value |
|---|---|
| Role | Reports Reader |
| Assigned principal | InsightsOps |
| Assignment type | Active |
| Permanently assigned | Yes |
| Justification | Required for centralized reporting access |

**What Reports Reader is:** Reports Reader is a built-in Microsoft Entra role that provides read access to selected reporting information without granting broader administrative control.

**What group-based role assignment does:** The role is attached to the group. Members receive the role through membership.

```text
Reports Reader role
        ↓
InsightsOps group
        ↓
Marcus Lee
```

**Why this is better than repeated direct assignment:** If multiple analysts need the same role, administrators can manage one group instead of maintaining many individual role assignments.

**Operational benefit:** Adding a user to the group grants the role. Removing the user removes the inherited access. The group becomes the administrative control point.

**Security consideration:** Membership in a role-assignable group is privileged. A person who can change the group’s membership may be able to grant or remove administrative access indirectly. Ownership and membership management should therefore be restricted and audited.

**Why Marcus Lee was not selected directly:** The task required the group to receive the role. Selecting the individual user would have created a direct role assignment and defeated the group-based model.

##### 🔷 Phase 2.3 — Add an owner and member to Security-Reviewers

I opened the existing security group:

```text
Entra ID → Groups → All groups → Security-Reviewers
```

I performed two separate actions:

1. Opened **Owners** and added `Marcus Lee`.
2. Opened **Members** and added `Priya Nair`.

**What an owner is:** An owner can manage the group, including membership, depending on tenant configuration and group type.

**What a member is:** A member belongs to the group and receives whatever access, licensing, application assignment, or policy scope is attached to the group.

**Owner vs. Member:**

| Relationship | Meaning |
|---|---|
| Owner | Manages the group |
| Member | Receives the group’s access or services |

A user can be both an owner and a member, but ownership does not always automatically make the person a member.

**Why existing entries were not removed:** The task required additions only. Removing unrelated owners or members would create unnecessary environmental change and could reduce the assessment score or disrupt access.

##### 🔷 Phase 2.4 — Phase 2 findings

| Task | Outcome |
|---|---|
| Role-assignable group created | `InsightsOps` |
| Membership type | Assigned |
| Initial member | `Marcus Lee` |
| Directory role assigned | Reports Reader assigned to the group |
| Role inheritance | Marcus Lee receives Reports Reader through group membership |
| Existing group owner added | Marcus Lee added as owner of `Security-Reviewers` |
| Existing group member added | Priya Nair added as member of `Security-Reviewers` |

</details>

<details>
<summary><strong>▶ Phase 3 — Review and Document Password Protection and Password Reset Configuration</strong><br>
→ gathering current-state authentication security settings without changing the tenant
</summary><br>

This phase was a documentation task rather than a configuration-change task.

The objective was to:

1. inspect current Microsoft Entra settings,
2. identify the values required by the documentation utility,
3. enter only those values,
4. save each documentation section,
5. avoid modifying the tenant.

##### 🔷 Phase 3.1 — Review Password Protection

I navigated to:

```text
Entra ID → Authentication methods → Password protection
```

The sanitized observed configuration was:

| Setting | Value |
|---|---|
| Lockout threshold | 12 |
| Lockout duration in seconds | 180 |
| Enforce custom banned password list | Yes |
| Custom banned password list | Orchid |
| Enable password protection on Windows Server Active Directory | Yes |
| Mode | Audit |

**What smart lockout is:** Smart lockout helps protect accounts from password-guessing attacks. After enough failed attempts, Entra ID temporarily blocks further authentication attempts associated with the suspicious activity.

**Lockout threshold:** The threshold is the number of failed attempts that can trigger lockout behavior.

**Lockout duration:** The duration controls how long the lockout remains in effect before another attempt may be allowed.

**Why lockout is not a complete defense:** Lockout reduces repeated guessing, but attackers may distribute attempts across many accounts or IP addresses. Strong authentication, multi-factor authentication, risk-based policies, and password protection remain necessary.

**What the custom banned password list is:** The custom list contains organization-specific words that users should not use in passwords. Common examples include:

- company names,
- product names,
- office locations,
- sports team names,
- internal project names,
- common seasonal terms.

The sanitized word `Orchid` represents a tenant-specific banned term.

**Why organization-specific bans matter:** Microsoft already blocks globally weak passwords, but attackers often guess words connected to the target organization. A custom list addresses this local risk.

**What on-premises password protection means:** Microsoft Entra Password Protection can extend banned-password checks to Windows Server Active Directory Domain Services. This helps organizations maintain similar password quality rules across cloud and on-premises identities.

**Audit vs. Enforced mode:**

| Mode | Behavior |
|---|---|
| Audit | Records whether a password would be rejected without blocking it |
| Enforced | Rejects passwords that violate the policy |

Audit mode is useful before enforcement because administrators can measure impact and identify compatibility or user-experience issues.

##### 🔷 Phase 3.2 — Record Password Protection in the Configuration Data Editor

I opened the lab-provided desktop editor and selected the Password Protection section.

The editor requested only:

| Editor Field | Recorded Value |
|---|---|
| Lockout threshold | 12 |
| Lockout duration in seconds | 180 |
| Custom banned password list | Orchid |

**Why only three values were entered:** The documentation form did not contain fields for every setting visible in the Entra portal. The correct action was to populate the requested fields only, not invent new rows or alter unrelated data.

I selected **Save** and confirmed the success message.

##### 🔷 Phase 3.3 — Review Password Reset Authentication Requirements

I navigated to:

```text
Entra ID → Password reset → Authentication methods
```

The sanitized observed value was:

| Setting | Value |
|---|---|
| Number of methods required to reset | 2 |
| Security questions | Not enabled |

**What self-service password reset is:** Self-service password reset allows users to reset or unlock their account without contacting the help desk, after proving their identity with approved authentication methods.

**Why multiple methods may be required:** Requiring two methods provides stronger identity verification than relying on one method. It reduces the risk that compromise of a single phone number, email inbox, or device is enough to reset the password.

**Why security questions are weaker:** Security-question answers may be guessable, discoverable through social media, reused, or shared. Organizations increasingly prefer stronger authentication methods.

##### 🔷 Phase 3.4 — Review Authentication Method Policies

The Password Reset page did not show every method requested by the editor. I therefore navigated to:

```text
Entra ID → Authentication methods → Policies
```

The sanitized method states were:

| Authentication Method | Enabled |
|---|---|
| Passkey (FIDO2) | No |
| Microsoft Authenticator | Yes |
| SMS | Yes |
| Temporary Access Pass | No |
| Voice call | No |
| Email OTP | Yes |

**Why two pages were required:** The password reset page showed the number of methods required, while the authentication method policy page showed whether modern methods were enabled in the tenant.

**What Passkey (FIDO2) is:** A passkey is a phishing-resistant authentication method based on public-key cryptography. The private key remains on the user’s device or security key.

**What Microsoft Authenticator provides:** Microsoft Authenticator can support passwordless sign-in, push notifications, number matching, and time-based codes, depending on configuration.

**What SMS provides:** SMS sends a code to a registered phone number. It is widely supported but generally considered less phishing-resistant and less secure than passkeys or authenticator-based passwordless methods.

**What Temporary Access Pass is:** Temporary Access Pass is a time-limited credential that can help bootstrap a user into stronger authentication registration.

**What voice call provides:** Voice call delivers verification through an automated phone call. It is a legacy-friendly option but is less resistant to telephony-related attacks.

**What Email OTP provides:** Email one-time passcode can be used in supported external collaboration scenarios to verify a user through email.

**Important distinction:** An authentication method being enabled in policy does not guarantee every user has registered it. Enablement controls availability; registration determines whether a specific user can use it.

##### 🔷 Phase 3.5 — Record Password Reset Findings

I opened the Password Reset section of the Configuration Data Editor and recorded:

| Editor Field | Value |
|---|---|
| Number of methods required to reset | 2 |
| Passkey (FIDO2) | No |
| Microsoft Authenticator | Yes |
| SMS | Yes |
| Temporary Access Pass | No |
| Voice Call | No |
| Email OTP | Yes |

I selected **Save** and confirmed the success message.

##### 🔷 Phase 3.6 — Phase 3 findings

| Task | Outcome |
|---|---|
| Password protection reviewed | Current smart lockout and banned-password settings identified |
| On-premises protection reviewed | Enabled in Audit mode |
| Password reset requirement reviewed | Two methods required |
| Authentication policies reviewed | Current enabled and disabled methods identified |
| Documentation updated | Password Protection and Password Reset sections saved |
| Tenant changes | None |

</details>

<details>
<summary><strong>▶ Phase 4 — Review and Document Conditional Access Named Locations</strong><br>
→ identifying reusable geographic conditions and confirming trust configuration
</summary><br>

This phase focused on country-based named locations.

I navigated to:

```text
Entra ID → Conditional Access → Named locations
```

The list contained both:

- country-based locations,
- IP-range locations.

The documentation request specifically required country-based locations.

##### 🔷 Phase 4.1 — Distinguish country locations from IP-range locations

A named location is a reusable network or geography object used by Conditional Access policies.

Two common types are:

| Location Type | Definition |
|---|---|
| Countries/Regions | Uses IP-to-country mapping |
| IP ranges | Uses administrator-defined public IP address ranges |

**Why the distinction matters:** A task asking for country-based named locations should not include IP-range objects. The two object types represent different conditions.

The sanitized country-based entries were:

- `Portugal`
- `Chile`

Other IP-range entries were ignored because they were outside the documentation requirement.

##### 🔷 Phase 4.2 — Review Portugal

I opened the `Portugal` named location and confirmed:

| Setting | Value |
|---|---|
| Name | Portugal |
| Country lookup method | Determine location by IP address |
| Selected country | Portugal |
| Include unknown countries/regions | No |
| Mark as trusted location | No |

I used the country search field to verify that Portugal was actually selected.

I then selected **Cancel**, not Save.

**Why Cancel was used:** The task was to inspect and document the current configuration. Selecting Save after opening an edit panel could create an unintended modification or update timestamp even when no visible field was changed.

##### 🔷 Phase 4.3 — Review Chile

I opened the `Chile` named location and confirmed:

| Setting | Value |
|---|---|
| Name | Chile |
| Country lookup method | Determine location by IP address |
| Selected country | Chile |
| Include unknown countries/regions | No |
| Mark as trusted location | No |

I confirmed Chile was selected and then selected **Cancel**.

##### 🔷 Phase 4.4 — Understand Named Location Behavior

**What a named location does:** A named location labels a network or geography condition. By itself, it does not allow, block, or challenge access.

A Conditional Access policy must reference the named location before it affects sign-ins.

```text
Named location
        +
Conditional Access policy
        =
Access decision based on location
```

**What “Include unknown countries/regions” means:** Some IP addresses cannot be mapped confidently to a country. This option determines whether those unknown results are included in the named location.

**What “Trusted” means:** Marking a location trusted identifies it as a network the organization considers known or controlled. Trust can affect policy logic, but it does not make the location inherently safe. Trusted locations should be used carefully because an attacker operating from that network may receive the same policy treatment.

**Limitations of country detection:** Country-based locations rely on IP geolocation. Virtual private networks, cloud proxies, mobile carriers, and geolocation inaccuracies can affect the result. Country conditions should not be treated as proof of a user’s physical presence.

##### 🔷 Phase 4.5 — Record Named Locations

I opened the Named Locations section of the Configuration Data Editor and entered:

| Row | Value |
|---|---|
| 1 | Portugal |
| 2 | Chile |

I selected **Save** and confirmed the success message.

##### 🔷 Phase 4.6 — Phase 4 findings

| Task | Outcome |
|---|---|
| Named location list reviewed | Country and IP-range objects distinguished |
| Country location reviewed | Portugal confirmed |
| Country location reviewed | Chile confirmed |
| Unknown countries included | No |
| Trusted state | Neither country location marked trusted |
| Documentation updated | Both country-based locations saved |
| Tenant changes | None |

</details>

<details>
<summary><strong>▶ Phase 5 — Run Conditional Access What If Analysis and Document Policy Scope</strong><br>
→ simulating a sign-in, interpreting policy evaluation, and confirming who each policy targets
</summary><br>

This phase focused on Conditional Access.

##### 🔷 Phase 5.1 — Understand Conditional Access

Conditional Access is Microsoft Entra’s policy engine for evaluating sign-ins.

It answers a question similar to:

```text
When this identity tries to access this resource
from this device, client, network, and risk context,
what controls should be required?
```

A Conditional Access policy contains two major parts:

1. **Assignments and conditions** — who, what resource, which device, location, client, or risk.
2. **Access controls** — what should happen when the assignments and conditions match.

Examples of access controls include:

- require multi-factor authentication,
- require a compliant device,
- require an approved client application,
- require an app protection policy,
- require a specific authentication strength,
- block access,
- apply session restrictions.

Conditional Access is evaluated during sign-in. It does not replace authentication; it adds contextual authorization requirements after the identity is recognized.

##### 🔷 Phase 5.2 — Configure the What If scenario

I navigated to:

```text
Entra ID → Conditional Access → Policies → What If
```

I configured the sanitized scenario:

| Input | Value |
|---|---|
| Identity type | Users |
| User | Noah Bennett |
| Target type | Cloud apps |
| Target resource | Microsoft SharePoint Online |
| Device platform | iOS |
| Client app | Browser |
| Sign-in risk | Medium |
| IP address | 203.0.113.25 |
| Country | Canada |

I left unrelated conditions blank:

- authentication flow,
- insider risk,
- user risk,
- device filters.

**Why unspecified fields were left blank:** The scenario should include only the required signals. Adding extra conditions would change the simulated context and could change the result.

**What the What If tool does:** What If evaluates existing Conditional Access policies against a hypothetical sign-in. It does not perform a real sign-in and does not change policy configuration.

**Why What If is useful:**

- validates whether a policy targets the expected user,
- helps troubleshoot unexpected access,
- identifies exclusions,
- distinguishes non-matching conditions,
- evaluates Report-only policies,
- reduces the need to repeatedly test with live accounts.

I selected **What If** to run the evaluation.

##### 🔷 Phase 5.3 — Review policies that will apply

The sanitized result showed:

| Policy | State | Evaluation |
|---|---|---|
| CA-02-Protect-Mobile | On | Applies |
| CA-03-Compliant-Devices | Report-only | Applies |
| CA-04-Session-Protection | Report-only | Applies |

**What “Applies” means:** The user, resource, and provided sign-in conditions matched the policy’s assignments and conditions.

**What an On policy means:** The access controls are actively enforceable during a real matching sign-in.

**What Report-only means:** Microsoft Entra evaluates the policy and records what would have happened, but does not enforce the access controls.

Report-only is useful for:

- testing policy scope,
- measuring impact,
- finding unexpected matches,
- reviewing sign-in logs before enforcement,
- avoiding accidental lockout.

**Important interpretation:** A Report-only policy can appear under “will apply” because its conditions match. “Apply” describes policy matching; “Report-only” describes enforcement state.

##### 🔷 Phase 5.4 — Review policies that will not apply

The sanitized result showed:

| Policy | State | Evaluation | Reason |
|---|---|---|---|
| CA-01-MFA-Contractors | On | Does not apply | Users and groups |

**What this means:** The policy did not include the simulated user in its assignment scope, or the user was excluded through user/group targeting.

**Why the reason matters:** A policy can fail to apply for many reasons:

- user not included,
- user excluded,
- target application not included,
- device platform does not match,
- location does not match,
- client application does not match,
- risk level does not match,
- device filter does not match.

The What If result identifies the category that prevented the policy from matching.

##### 🔷 Phase 5.5 — Review each policy’s user assignment

The next task was to determine which users each policy targeted.

For each policy, I navigated to:

```text
Conditional Access → Policies → [Policy] → View or Edit
Assignments → Users or agents
```

I reviewed the Include configuration without saving changes.

The sanitized findings were:

| Policy | Included User Scope |
|---|---|
| CA-01-MFA-Contractors | Marcus Lee |
| CA-02-Protect-Mobile | Noah Bennett |
| CA-03-Compliant-Devices | All users |
| CA-04-Session-Protection | All users |

**Specific users included:** A policy assigned to selected users and groups applies only to those principals unless other exclusions or conditions prevent it.

**All users:** A policy assigned to All users has broad tenant scope. These policies require especially careful testing and emergency-account exclusions to avoid tenant-wide lockout.

**Why opening edit mode was necessary:** The policy summary showed a user count but did not always display the actual user name. Opening the assignments panel exposed the selected principal.

**Why Save was not selected:** The task was read-only. Opening a policy for inspection did not require saving.

##### 🔷 Phase 5.6 — Understand the policy differences

The sanitized policy set represented different enforcement and scope patterns:

| Policy | Scope Pattern | State Pattern |
|---|---|---|
| CA-01-MFA-Contractors | One specific user | On |
| CA-02-Protect-Mobile | One specific user | On |
| CA-03-Compliant-Devices | All users | Report-only |
| CA-04-Session-Protection | All users | Report-only |

This demonstrates that policy names alone are not sufficient to understand behavior.

To understand a Conditional Access policy, review:

1. user and workload identity assignments,
2. target resources,
3. network conditions,
4. device platform,
5. client app,
6. risk conditions,
7. grant controls,
8. session controls,
9. exclusions,
10. policy state.

##### 🔷 Phase 5.7 — Update Conditional Access Users documentation

I opened the Conditional Access Users section in the Configuration Data Editor.

I recorded:

| Policy Name | Users |
|---|---|
| CA-01-MFA-Contractors | Marcus Lee |
| CA-02-Protect-Mobile | Noah Bennett |
| CA-03-Compliant-Devices | All users |
| CA-04-Session-Protection | All users |

I selected **Save** and confirmed the success message.

##### 🔷 Phase 5.8 — Update What If documentation

I opened the What If section in the Configuration Data Editor.

I recorded:

| Policy Name | Applies | State |
|---|---|---|
| CA-01-MFA-Contractors | No | On |
| CA-02-Protect-Mobile | Yes | On |
| CA-03-Compliant-Devices | Yes | Report-only |
| CA-04-Session-Protection | Yes | Report-only |

I selected **Save** and confirmed the success message.

##### 🔷 Phase 5.9 — Phase 5 findings

| Task | Outcome |
|---|---|
| What If scenario configured | User, app, platform, client, risk, IP, and country entered |
| Policies that apply | Three policies matched |
| Enforced matching policy | One matching policy was On |
| Report-only matching policies | Two matching policies were Report-only |
| Policy that did not apply | One policy failed user/group scope |
| User assignments documented | Four policies reviewed |
| What If results documented | Applies and State values saved |
| Tenant changes | None |

</details>

---

### Evidence Examination Summary

| Task | Portal/Tool | Object(s) Affected or Reviewed | Outcome |
|---|---|---|---|
| Create internal user | Entra admin center | `Maya Chen` | Cloud-only member identity created |
| Disable sign-in | Entra admin center | `Maya Chen` | Account exists but cannot authenticate |
| Configure profile | Entra admin center | `Maya Chen` | Job title, department, and usage location populated |
| Assign individual license | Microsoft 365 admin center | `Maya Chen` | Microsoft 365 E3 assigned |
| Assign directory role | Entra admin center | `Omar Rahman` | Billing Administrator assigned active and permanent |
| Invite external user | Entra admin center | `Evelyn Brooks` | Guest identity created |
| Create role-assignable group | Entra admin center | `InsightsOps` | Security group created with role assignment enabled |
| Add group member | Entra admin center | `Marcus Lee` | Added to InsightsOps |
| Assign role to group | Entra admin center | `InsightsOps` | Reports Reader assigned |
| Add group owner | Entra admin center | `Security-Reviewers` / `Marcus Lee` | Owner added |
| Add group member | Entra admin center | `Security-Reviewers` / `Priya Nair` | Member added |
| Review password protection | Entra admin center | Tenant configuration | Current smart lockout and banned-password values identified |
| Review password reset | Entra admin center | Tenant configuration | Required reset methods identified |
| Review authentication methods | Entra admin center | Tenant method policies | Current method states identified |
| Document password settings | Configuration Data Editor | Password Protection / Password Reset | Values saved |
| Review named locations | Entra admin center | Portugal / Chile | Country and trust settings confirmed |
| Document named locations | Configuration Data Editor | Named Locations | Values saved |
| Run What If | Entra admin center | Conditional Access policies | Matching and non-matching policies identified |
| Review policy user scope | Entra admin center | Four policies | Specific users and All users scopes confirmed |
| Document policy assignments | Configuration Data Editor | Conditional Access Users | Values saved |
| Document What If result | Configuration Data Editor | What If | Applies and State values saved |

---

### What I Learned (Skills Demonstrated)

Through this workflow, I learned how to:

- Create a cloud-only internal identity in Microsoft Entra ID.
- Understand the difference between a user object existing and the user being allowed to sign in.
- Configure a user principal name, display name, job title, department, and usage location.
- Understand why usage location affects Microsoft 365 license assignment.
- Navigate from Entra ID to the Microsoft 365 admin center for licensing.
- Assign a Microsoft 365 license to an individual user.
- Distinguish identity administration from subscription administration.
- Assign a built-in Microsoft Entra directory role.
- Understand active versus eligible privilege.
- Understand permanent versus time-bound role assignment.
- Provide a business justification for privileged access.
- Invite a business-to-business guest user.
- Understand the difference between Member and Guest user types.
- Create an Assigned-membership security group.
- Create a group that is eligible for Microsoft Entra role assignment.
- Understand why role assignability must be selected during group creation.
- Assign Reports Reader to a group.
- Understand how directory role inheritance works through group membership.
- Distinguish a group owner from a group member.
- Add owners and members without removing existing relationships.
- Navigate Authentication methods and Password reset settings.
- Explain smart lockout threshold and duration.
- Explain global and custom banned password protection.
- Explain Audit versus Enforced on-premises password protection.
- Understand self-service password reset verification requirements.
- Review modern authentication method enablement.
- Explain Passkey, Microsoft Authenticator, SMS, Temporary Access Pass, voice call, and Email OTP.
- Distinguish method availability from user registration.
- Identify country-based named locations.
- Distinguish country named locations from IP-range named locations.
- Explain unknown-country handling and trusted-location status.
- Understand that named locations require a Conditional Access policy before they affect access.
- Configure a Conditional Access What If simulation.
- Explain how user, application, device, client, network, and risk signals affect policy evaluation.
- Interpret policies that apply and do not apply.
- Interpret the reason a policy does not apply.
- Distinguish On from Report-only policy state.
- Review the user scope of Conditional Access policies.
- Document current-state settings without changing the environment.
- Verify save confirmations in a structured documentation application.

---

### Key Takeaways

This workflow showed that Microsoft Entra ID administration is a connected sequence of identity, authorization, authentication, and policy operations.

```text
Create or invite the identity
        ↓
Populate the attributes that other systems depend on
        ↓
Assign service entitlement through licensing
        ↓
Delegate administrative access through least-privileged roles
        ↓
Use groups to scale access and role assignment
        ↓
Review authentication defenses and recovery methods
        ↓
Define reusable location conditions
        ↓
Evaluate Conditional Access against a sign-in context
        ↓
Document the current state without introducing changes
```

The most important lesson is that similar-looking tasks change different layers of the identity system:

```text
Identity object      = represents the user
License              = enables products and services
Group                = organizes identities and grants inherited access
Directory role       = grants administrative authority
Authentication method= proves identity
Named location       = describes network or geography
Conditional Access   = evaluates context and requires controls
Documentation        = records evidence of current configuration
```

A second major lesson is that **policy scope is as important as policy controls**. A policy requiring strong authentication does nothing for a user who is outside its assignment scope. Conversely, a policy assigned to All users can have a tenant-wide impact even when it is intended for a narrow use case.

A third major lesson is that **Report-only does not mean ignored**. A Report-only policy still evaluates and appears as applicable. It simply does not enforce its controls. This makes Report-only an essential deployment stage for measuring effect before turning a policy On.

A fourth major lesson is that **group-based administration changes the access model**. When a role is assigned to a role-assignable group, membership becomes privilege management. Group membership changes must therefore be treated with the same care as direct role assignments.

Finally, this workflow reinforced the difference between making a change and gathering evidence. During the documentation phases, the correct action was to inspect, record, and cancel out of edit panels rather than save tenant settings. Careful read-only review is a core operational skill because unnecessary changes can create risk, invalidate evidence, or affect assessment results.

Understanding these distinctions separates someone who can follow portal instructions from someone who understands how identity, privilege, authentication, and access policy work together in Microsoft Entra ID.
