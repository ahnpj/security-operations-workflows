# Tool Usage Notes — Active Directory Domain Services Administration

## Overview

This document summarizes the tools, consoles, wizards, and operational relevance of the utilities used during the Active Directory Domain Services administration workflow.

The workflow focused on:

* installing the AD DS role and promoting a domain controller,
* transferring a FSMO role between domain controllers,
* creating an Active Directory site and associating a subnet,
* creating organizational units and managing user accounts,
* creating and managing a security group,
* configuring the Protected Users group,
* delegating administrative control over an OU,
* configuring and validating a custom user attribute,
* disabling an account and resetting a password,
* configuring domain-wide and fine-grained password policies,
* enabling the Active Directory Recycle Bin,
* restricting NTLM authentication,
* configuring advanced audit policy,
* configuring a user rights assignment.

---

## Server Manager

### Purpose

Server Manager is the central console used to manage roles, features, and server configuration on Windows Server. In this workflow, it was the entry point for installing the AD DS role and initiating domain controller promotion.

### How It Was Used

Used on `TAILWIND-MBR1` via **Manage → Add Roles and Features** to install Active Directory Domain Services, and subsequently via the notification flag icon to launch **Promote this server to a domain controller**.

### Operational Relevance

Server Manager doesn't perform the domain controller promotion itself — it installs the AD DS binaries and hands off to the AD DS Configuration Wizard for the actual promotion. Understanding this two-step relationship (install the role, then separately promote using the wizard) avoids the confusion of expecting the server to become a functioning domain controller immediately after role installation alone.

---

## Active Directory Domain Services Configuration Wizard

### Purpose

This wizard performs the actual promotion of a server to a domain controller (or the initial creation of a new forest/domain), handling DNS, NTDS database paths, and DSRM password configuration.

### How It Was Used

Launched from Server Manager's notification menu on `TAILWIND-MBR1`. Selected **Add a domain controller to an existing domain**, targeting `tailwindtraders.internal`, re-authenticated as `Administrator`, and accepted default settings for DNS, Additional Options, and Paths while providing a DSRM password.

### Operational Relevance

The **Directory Services Restore Mode (DSRM)** password configured during this wizard is a distinct, separate credential from the domain Administrator password — it's used specifically for recovering a domain controller when booted into Directory Services Restore Mode, independent of normal domain authentication. This is a common point of confusion since the wizard prompts for it using the same familiar "enter twice to confirm" pattern as a normal password field.

---

## Active Directory Users and Computers (ADUC)

### Purpose

ADUC is the primary graphical console for day-to-day Active Directory object management: OUs, users, groups, computers, and their attributes.

### How It Was Used

Used throughout Phase 1 and Phase 2 of the workflow for:

* transferring the RID FSMO role (**All Tasks → Operations Masters**),
* creating the `Sydney`, `Melbourne`, and `Brisbane` OUs,
* creating, copying, and relocating the `SydneyContractor`, `MelbourneContractor`, and `BrisbaneContractor` user accounts,
* creating the `Sydney Administrators` group and managing its membership,
* adding `SydneyContractor` to the Protected Users group,
* running the Delegation of Control Wizard against the Sydney OU,
* configuring the City attribute and running an Advanced Find query,
* disabling `MelbourneContractor` and resetting `BrisbaneContractor`'s password.

### Operational Relevance

ADUC's **Copy** function (used to create `MelbourneContractor` and `BrisbaneContractor` from `SydneyContractor`) carries over most account properties — including the account expiration date configured earlier — but does **not** carry over group memberships or a password, both of which must be set again on the copied account. This is a useful time-saver for provisioning similarly-configured accounts, but it's important to know exactly what does and doesn't get copied.

---

## Delegation of Control Wizard (within ADUC)

### Purpose

This wizard grants a specific, scoped set of administrative permissions over an OU (or other container) to a chosen user or group, without requiring manual ACL editing or broader group membership changes.

### How It Was Used

Launched by right-clicking the Sydney OU and selecting **Delegate Control**. Added the `Sydney Administrators` group and selected the pre-defined task **Reset user passwords and force password change at next logon**.

### Operational Relevance

The wizard offers a curated list of common delegation tasks (like password reset) specifically so administrators don't need to hand-construct Active Directory ACL permissions for routine delegation scenarios. This keeps delegated permissions predictable and auditable, compared to manually editing security descriptors via the Advanced Security Settings dialog.

---

## Active Directory Sites and Services

### Purpose

This console manages Active Directory's physical topology representation — sites, subnets, and site links — which governs authentication locality and replication scheduling.

### How It Was Used

Used on `TAILWIND-DC1` to create the `Sydney` site (right-click **Sites → New Site**, linked via `DEFAULTIPSITELINK`), and to associate the `172.16.1.0/24` subnet with that site (right-click **Subnets → New Subnet**).

### Operational Relevance

A site with no subnet associated with it provides no real benefit — clients and domain controllers are matched to a site based on their IP address falling within a subnet mapped to that site. Creating the site without also mapping the subnet (a step that's easy to forget) would leave the site definition effectively unused.

---

## Active Directory Administrative Center (ADAC)

### Purpose

ADAC is a more modern, task-oriented management console (compared to ADUC) that also exposes functionality not available in ADUC at all, including Fine-Grained Password Policies and the AD Recycle Bin.

### How It Was Used

Used in Phase 3 to:

* navigate to the **System → Password Settings Container** and create the `Domain Admin Password Policy` Password Settings Object (precedence `1`, minimum password length `16`), applied directly to the Domain Admins group,
* select the domain node (`Tailwindtraders (local)`) and enable the **Recycle Bin** from the Tasks pane.

### Operational Relevance

Fine-Grained Password Policies and the AD Recycle Bin are both configured through ADAC (or PowerShell) rather than ADUC or the Group Policy Management Console, which can be a source of confusion for administrators who default to GPMC for anything password-policy-related. FGPPs specifically are **not** Group Policy objects at all, despite being conceptually similar to a password policy GPO — they're a distinct AD object type (Password Settings Objects) that only apply to users and groups, not OUs.

---

## Group Policy Management Console (GPMC) and Group Policy Management Editor

### Purpose

GPMC is used to create, link, and manage Group Policy Objects (GPOs) across the domain; the Group Policy Management Editor is used to actually configure the settings within a given GPO.

### How It Was Used

Used throughout Phases 3 and 4 to:

* edit the **Default Domain Policy** to set the domain-wide minimum password length to `14`,
* edit the **Default Domain Controller Policy** to set NTLM authentication to **Deny all**,
* create and link a new GPO, `SydneyOUPolicy`, directly to the Sydney OU,
* configure **Audit User Account Management** (Success and Failure) within `SydneyOUPolicy`,
* configure **Deny log on as a service** for the Sydney Administrators group within `SydneyOUPolicy`.

### Operational Relevance

GPO linking location matters as much as GPO content. The **Default Domain Policy** and **Default Domain Controller Policy** are linked at the domain and Domain Controllers OU level respectively by default, meaning changes there apply broadly. `SydneyOUPolicy`, by contrast, was deliberately created and linked only to the Sydney OU, scoping its (in this case, more restrictive) settings to just that OU rather than the whole domain. Understanding GPO link scope is essential — editing the wrong GPO, or linking a new one at the wrong level, can apply settings far more broadly (or narrowly) than intended.

---

## Evidence and Tool Mapping

| Task | Tool/Console | Object(s) Affected |
| --- | --- | --- |
| Install AD DS role | Server Manager | `TAILWIND-MBR1` |
| Promote to domain controller | AD DS Configuration Wizard | `TAILWIND-MBR1` |
| Transfer RID FSMO role | ADUC (Operations Masters) | `TAILWIND-DC1` → `TAILWIND-MBR1` |
| Create AD site and subnet | Active Directory Sites and Services | `Sydney`, `172.16.1.0/24` |
| Create OUs | ADUC | `Sydney`, `Melbourne`, `Brisbane` |
| Create/copy/relocate users | ADUC | `SydneyContractor`, `MelbourneContractor`, `BrisbaneContractor` |
| Create security group | ADUC | `Sydney Administrators` |
| Configure Protected User | ADUC | `SydneyContractor` |
| Delegate control | ADUC (Delegation of Control Wizard) | Sydney OU |
| Configure/validate attribute | ADUC (Properties + Find) | `SydneyContractor` |
| Disable account | ADUC | `MelbourneContractor` |
| Reset password | ADUC | `BrisbaneContractor` |
| Configure domain password policy | GPMC (Default Domain Policy) | Domain-wide |
| Configure Fine-Grained Password Policy | ADAC | Domain Admins |
| Enable AD Recycle Bin | ADAC | Domain-wide |
| Restrict NTLM | GPMC (Default Domain Controller Policy) | Domain-wide |
| Configure advanced auditing | GPMC (SydneyOUPolicy) | Sydney OU |
| Configure user rights assignment | GPMC (SydneyOUPolicy) | Sydney Administrators |

---

## Common Mistakes to Avoid

A common mistake is confusing the DSRM password prompted during domain controller promotion with the domain Administrator password — they are separate credentials serving different purposes.

Another mistake is creating an Active Directory site without also creating and associating a subnet with it, which leaves the site definition without any practical effect on client/DC locality.

Another mistake is granting broad group membership (such as adding a group to Domain Admins) when a narrowly scoped delegation via the Delegation of Control Wizard would achieve the specific administrative need with far less risk.

Another mistake is assuming the Protected Users group changes visible permissions or group-based access — its effects are entirely about authentication mechanics (blocking NTLM/weak Kerberos encryption, disabling credential caching, limiting ticket lifetime) rather than object permissions.

Another mistake is misunderstanding Fine-Grained Password Policy precedence — a **lower** precedence number takes priority when multiple FGPPs could apply to the same account, which is the opposite of what "precedence" might intuitively suggest.

Another mistake is assuming the AD Recycle Bin is enabled by default; it must be explicitly and irreversibly enabled, and doing so proactively (rather than after an accidental deletion has already occurred) is the only way it provides value.

Another mistake is editing or linking a GPO at the wrong scope — for example, accidentally editing the Default Domain Policy when a change was only meant to apply to a single OU, or linking a new GPO at the domain level when it was intended to apply only to a specific OU.

---

## Suggested Screenshot Documentation

| Screenshot | Purpose |
| --- | --- |
| Add Roles and Features — AD DS role selection | Supports domain controller role installation |
| Server Manager notification — promote to domain controller | Supports promotion initiation |
| AD DS Configuration Wizard — Deployment Configuration page | Supports domain controller promotion evidence |
| Post-promotion sign-in confirmation | Supports successful domain controller promotion |
| Operations Masters dialog — RID tab | Supports FSMO role transfer evidence |
| New Site dialog — Sydney site creation | Supports AD site configuration |
| New Subnet dialog — 172.16.1.0/24 association | Supports subnet-to-site mapping |
| New Organizational Unit dialogs | Supports OU creation evidence |
| New User dialog — SydneyContractor | Supports user account creation |
| Account tab — expiration date configuration | Supports account expiration configuration |
| Copy Object-User wizard | Supports account provisioning via copy |
| Drag-and-drop OU relocation confirmation | Supports user relocation evidence |
| New Group dialog — Sydney Administrators | Supports security group creation |
| Member Of tab — Sydney Administrators and Protected Users additions | Supports group membership evidence |
| Delegation of Control Wizard — task selection | Supports delegation evidence |
| Address tab — City attribute configuration | Supports attribute configuration |
| Advanced Find — City query and results | Supports attribute validation |
| Disable Account confirmation | Supports account lifecycle action |
| Reset Password dialog | Supports password reset evidence |
| Group Policy Management Editor — Password Policy settings | Supports domain password policy configuration |
| ADAC — Password Settings Object creation | Supports Fine-Grained Password Policy evidence |
| ADAC — Enable Recycle Bin task | Supports Recycle Bin enablement |
| Group Policy Management Editor — NTLM restriction setting | Supports NTLM restriction evidence |
| New GPO dialog — SydneyOUPolicy creation and linking | Supports scoped GPO creation |
| Advanced Audit Policy Configuration — Account Management setting | Supports audit policy configuration |
| User Rights Assignment — Deny log on as a service | Supports user rights assignment evidence |

---

## Summary

Server Manager and the AD DS Configuration Wizard handled the infrastructure-level task of promoting a second domain controller. Active Directory Users and Computers served as the primary console for identity lifecycle management — OUs, users, groups, delegation, attributes, and account lifecycle actions. Active Directory Sites and Services handled physical topology configuration. Active Directory Administrative Center provided access to functionality not available elsewhere, specifically Fine-Grained Password Policies and the Recycle Bin. The Group Policy Management Console and Editor tied together both broad, domain-wide policy (password policy, NTLM restriction) and narrowly scoped, OU-level policy (auditing and user rights assignment via a dedicated GPO).

Together, these tools demonstrated that Active Directory administration relies on a specific console for the right job — no single tool handles every aspect of directory management, and understanding which console exposes which capability (and at what scope) is itself an important operational skill.