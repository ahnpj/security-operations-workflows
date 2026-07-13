# Active Directory Domain Services Administration — Paris Office Domain Controller Deployment, Identity Management, Password Policies, and Group Policy Security

Acting as the **Information Technology (IT) systems administrator** responsible for supporting a new Paris office, I needed to extend the existing `contoso.com` **Active Directory Domain Services (AD DS)** environment from one domain controller (`DC1`) to two (`DC1` and `DC2`). Based on the operational and security requirements, I configured the Paris network location, promoted `DC2` as an additional domain controller, organized and delegated administration of Paris identities, performed a controlled bulk user update with Windows PowerShell, strengthened password and recovery controls, and applied targeted Group Policy security settings.

The main tools used were **Server Manager**, the **Active Directory Domain Services Configuration Wizard**, **Active Directory Users and Computers (ADUC)**, the **Active Directory Administrative Center (ADAC)**, **Active Directory Sites and Services**, the **Group Policy Management Console (GPMC)**, and **Windows PowerShell**. See the **[Environment and Execution Context](#environment-and-execution-context)** section below.

---

### Overview

The work was completed as a connected systems-administration project against the existing `contoso.com` domain. Each requirement depended on earlier infrastructure or directory work, so the sequence moved from network and domain-controller preparation to identity administration, policy enforcement, and security hardening.

1. The first portion involved **Domain Controller and Topology Operations** — creating a Paris Active Directory site, associating the `172.16.1.0/24` subnet with that site, installing the Active Directory Domain Services server role on `DC2`, promoting `DC2` as an additional domain controller, validating communication between `DC1` and `DC2`, and transferring the Relative Identifier Master role to `DC2`.

2. The second portion involved **Paris Directory Object and Delegation Management** — creating the Paris organizational unit, creating the `Paris Admins` universal security group, creating the temporary `ParisContractor` account, configuring account expiration and protection, adding the contractor to the administrative group, and delegating only password-management rights over users in the Paris organizational unit.

3. The third portion involved **Bulk User Lifecycle Administration with Windows PowerShell** — finding every user whose City attribute was set to `Paris`, moving those accounts into the Paris organizational unit, disabling them, requiring a password change at the next sign-in, and verifying each result.

4. The fourth portion involved **Password Policy and Recovery Configuration** — increasing the domain-wide minimum password length to 12 characters, creating a stricter Fine-Grained Password Policy requiring 16 characters for members of Domain Admins, and enabling the Active Directory Recycle Bin.

5. The fifth portion involved **Group Policy Security Configuration** — denying New Technology LAN Manager authentication requests through the Default Domain Controllers Policy, creating a new Group Policy Object named `GPO1` linked only to the Paris organizational unit, auditing failed logon events, and denying members of `Paris Admins` the ability to log on as a service on computers that process the Paris policy.

This workflow demonstrates that Active Directory administration is not limited to creating user accounts. It includes server-role deployment, directory replication, physical-network topology, administrative delegation, identity lifecycle management, password enforcement, recovery planning, authentication hardening, audit visibility, and user-right restrictions.

> **Workflow vs. Execution vs. Writeup (Terminology Used Here)**
> - **Workflows** are repeatable Active Directory administration processes, such as promoting a domain controller or moving location-based users into an organizational unit.
> - **Executions** are the actual actions performed through graphical consoles and Windows PowerShell.
> - **Writeups** document the steps, tools, commands, validation, administrative reasoning, and security relevance of the execution.

> 👉 For a detailed walkthrough of the work in the order it was performed, see the **[Step-by-Step Execution](#step-by-step-execution)** section below.

---

### Purpose and Systems Administrator Focus

#### ▶ Purpose

The purpose of this workflow is to demonstrate practical administration of Active Directory Domain Services across domain-controller operations, directory organization, delegated identity administration, bulk account management, password policy, recovery readiness, and security hardening.

The domain-controller portion focused on placing an additional domain controller in the Paris site and transferring the Relative Identifier Master role. This matters because a branch office can use a nearby domain controller for authentication and directory queries instead of depending entirely on a distant server across a wide area network. An additional domain controller also provides redundancy if another controller becomes unavailable.

The directory-object portion focused on creating a Paris organizational unit, a dedicated administrative security group, and a temporary contractor account. This matters because organizational units provide a boundary for administration and Group Policy, while security groups allow permissions to be assigned to a reusable set of members instead of directly to individual accounts.

The delegation portion focused on granting `Paris Admins` one narrow capability: managing passwords for users inside the Paris organizational unit. This demonstrates the principle of least privilege because the group did not receive broad Domain Admins membership or unrestricted control of the domain.

The PowerShell portion focused on finding accounts by an Active Directory attribute and performing the same set of changes on every matching user. This matters because manually locating and modifying a large number of accounts is slow and error-prone. Attribute-based automation provides a repeatable and auditable way to manage accounts at scale.

The password-policy portion focused on applying a 12-character minimum to the domain while using a stricter 16-character policy for Domain Admins. This demonstrates that a domain can have one broad default password policy while higher-risk users receive a more restrictive Fine-Grained Password Policy.

The security-policy portion focused on blocking legacy New Technology LAN Manager authentication, auditing failed logons, and preventing a delegated administrative group from being used to run Windows services. Together, these settings reduce credential-based attack paths, improve investigation visibility, and limit the ways a compromised administrative account could be abused.

#### ▶ Systems Administrator Focus

The systems-administrator focus is on understanding not only which console or command performs a requirement, but also:

- what object is being changed,
- why that object exists,
- where the configuration applies,
- which users or computers are affected,
- how the change can be verified,
- and what security or operational problem the change addresses.

This includes understanding:

- the difference between installing the Active Directory Domain Services role and promoting a server to a domain controller,
- the difference between a domain, site, subnet, organizational unit, and security group,
- why a second domain controller improves availability and branch-office authentication,
- what the Relative Identifier Master does,
- why a Flexible Single Master Operations role should be transferred rather than seized when the current holder is still available,
- how organizational units scope delegation and Group Policy,
- how security groups act as permission and policy subjects,
- how the **Account is sensitive and cannot be delegated** control protects a user without granting permissions,
- how the City attribute can be used to identify a population of users,
- how PowerShell variables, filters, loops, pipelines, and calculated properties support bulk administration,
- how a Fine-Grained Password Policy differs from the Default Domain Policy,
- why the Active Directory Recycle Bin must be enabled before a deletion occurs,
- why New Technology LAN Manager is restricted,
- and how the scope of a Group Policy Object differs from the accounts named inside one of its settings.

The goal is not simply to reproduce menu clicks. The goal is to understand the Active Directory model well enough to explain why each configuration was appropriate and what would happen if it were applied incorrectly.

---

### What This Workflow Demonstrates

This workflow demonstrates how to:

- Create an Active Directory site representing a new physical office.
- Associate an Internet Protocol subnet with an Active Directory site.
- Install the Active Directory Domain Services server role.
- Promote a domain-joined server as an additional domain controller.
- Assign a domain controller to the correct Active Directory site.
- Verify domain-controller discovery and replication.
- Transfer and verify the Relative Identifier Flexible Single Master Operations role.
- Create an organizational unit for location-specific administration.
- Create a Universal security group.
- Create a generic contractor account and configure its expiration date.
- Configure the contractor account as a protected account.
- Add a user to a security group.
- Delegate password-reset and force-change rights over one organizational unit.
- Query users according to the City attribute.
- Move multiple user objects through Windows PowerShell.
- Disable multiple accounts.
- Require multiple users to change their passwords at the next sign-in.
- Verify organizational-unit placement and account settings.
- Configure the domain-wide minimum password length.
- Create a Password Settings Object for a Fine-Grained Password Policy.
- Apply a stricter password policy to Domain Admins.
- Enable the Active Directory Recycle Bin.
- Modify the Default Domain Controllers Policy.
- Deny New Technology LAN Manager authentication requests.
- Create and link a Group Policy Object to one organizational unit.
- Audit unsuccessful logon events.
- Configure a user-right assignment that denies logon as a service.
- Distinguish Group Policy scope, security filtering, and the security principals named inside a policy setting.

This workflow also demonstrates the layered nature of Active Directory security:

```text
Domain-wide default policy
        +
Stricter policy for privileged accounts
        +
Delegated administration limited to one organizational unit
        +
Authentication and logon restrictions
        +
Audit visibility
        =
Defense in depth
```

---

### Identity and Security Operations Relevance

Active Directory Domain Services is the central identity system in many enterprise Windows environments. Its configuration directly affects authentication, authorization, privileged access, account lifecycle management, recovery, and the ability to investigate suspicious activity.

This workflow can help answer questions such as:

- Why should a branch office have a local domain controller?
- How does a subnet tell Active Directory which site a computer belongs to?
- Why is installing the Active Directory Domain Services role not the same as promoting a domain controller?
- What does the Relative Identifier Master control?
- Why should a role be transferred instead of seized during a normal migration?
- Why is an organizational unit useful for both delegation and Group Policy?
- Why grant password-reset rights to a dedicated group instead of adding a contractor to Domain Admins?
- How can Active Directory attributes be used for bulk administration?
- Why should moved or inactive accounts be disabled rather than immediately deleted?
- Why might Domain Admins require a stronger password policy than standard users?
- Why must the Active Directory Recycle Bin be enabled proactively?
- Why is New Technology LAN Manager considered a legacy authentication risk?
- How does auditing failed logons support investigations?
- Why can a security group be named inside a user-right assignment even when the group is not located in the organizational unit receiving the Group Policy Object?

The table below summarizes the relationship between each major task and its operational or security purpose.

| Area | What Was Configured | Why It Matters |
|---|---|---|
| Active Directory Site | `Paris` site created | Represents a physical or network location in the directory |
| Subnet Mapping | `172.16.1.0/24` associated with Paris | Allows devices to be mapped to the Paris site by Internet Protocol address |
| Domain Controller Promotion | `DC2` promoted in `contoso.com` | Adds redundancy and supports site-local authentication |
| Relative Identifier Master | Role moved from `DC1` to `DC2` | Assigns the domain controller responsible for allocating unique identifier blocks |
| Organizational Unit | `Paris` created | Provides an administrative and Group Policy scope |
| Universal Security Group | `Paris Admins` created | Provides a reusable principal for delegated rights and policy restrictions |
| Protected Account | `ParisContractor` protected | Reduces exposure of credentials used by a delegated administrative account |
| Delegation | Password management delegated to `Paris Admins` | Applies least privilege instead of broad domain administration |
| Bulk Account Management | Paris users moved, disabled, and flagged for password change | Applies consistent lifecycle actions to all matching users |
| Domain Password Policy | Minimum length set to 12 | Establishes a baseline for domain accounts |
| Fine-Grained Password Policy | Domain Admins minimum set to 16 | Applies stricter controls to higher-risk privileged accounts |
| Active Directory Recycle Bin | Enabled | Improves recovery from accidental object deletion |
| New Technology LAN Manager Restriction | Deny all configured on domain controllers | Reduces exposure to legacy authentication attacks |
| Failed-Logon Auditing | Failure auditing enabled through `GPO1` | Creates evidence for troubleshooting and investigation |
| User Rights Assignment | `Paris Admins` denied logon as a service | Limits an unnecessary method of using delegated administrative credentials |


---

### Foundational Active Directory Concepts

The execution steps below use several Active Directory and networking terms that sound similar but control very different parts of the environment. This section provides a beginner-level mental model before the detailed administration begins.

<details>
<summary><strong>▶ Active Directory, the Domain, and Domain Controllers</strong><br>
</summary><br>

**Active Directory Domain Services (AD DS)** is Microsoft's on-premises directory and identity system for Windows environments. It stores objects such as users, computers, groups, and organizational units and provides centralized authentication and administration.

The **domain** in this workflow is:

```text
contoso.com
```

A domain is a shared administrative and authentication boundary. A user account created in `contoso.com` can be recognized by computers and services that trust that domain.

A **domain controller (DC)** is a Windows Server running Active Directory Domain Services and holding a copy of the directory database. When a domain user signs in, a domain controller helps answer the question:

```text
Are these credentials valid for this identity?
```

That is **authentication**. After the identity is authenticated, group memberships, access-control entries, and other permissions help answer:

```text
What is this identity allowed to access or do?
```

That is **authorization**.

`DC1` was the existing controller. `DC2` began as a domain-joined Windows Server and was promoted to provide a second writable copy of the directory.

> **Beginner note — member server vs. domain controller:** A **member server** belongs to the domain and can use domain accounts, but it does not store the directory database or authenticate the rest of the domain. Installing the Active Directory Domain Services role gives the server the required software; **promotion** is the separate step that actually turns it into a domain controller.

</details>

<details>
<summary><strong>▶ Forest, Domain, Site, Subnet, Organizational Unit, and Group</strong><br>
</summary><br>

These objects solve different problems:

| Concept | Plain-Language Meaning | Primary Purpose |
|---|---|---|
| Forest | Highest Active Directory structure | Contains one or more trusting domains and the shared schema/configuration |
| Domain | Main identity and administration boundary | Stores users, computers, groups, and domain policy |
| Site | Representation of a physical/network location | Guides domain-controller discovery and replication traffic |
| Subnet | Internet Protocol address range | Maps a device's address to an Active Directory site |
| Organizational Unit | Folder-like directory container | Organizes objects and scopes delegation or Group Policy |
| Security Group | Membership-based security principal | Receives permissions, rights, or restrictions for its members |

A **site** and an **organizational unit (OU)** are not substitutes:

```text
Site
→ Where is the device on the network?
→ Determined primarily by Internet Protocol subnet
→ Affects authentication locality and replication

Organizational Unit
→ Where is the object arranged in the directory?
→ Determined by the object's directory path
→ Affects Group Policy and delegated administration
```

The same computer can therefore be associated with the Paris **site** because of its network address while its computer object is stored in a particular **organizational unit** for administration.

An organizational unit and a security group are also different:

```text
OU = where the object lives
Group = which memberships the object has
```

A user object normally has one parent container or organizational unit at a time, but the user can be a member of many groups. Group Policy Objects are linked to sites, domains, or organizational units; access permissions are commonly assigned to security groups.

</details>

<details>
<summary><strong>▶ Group Policy: GPOs, Links, Computer/User Configuration, and Policies/Preferences</strong><br>
</summary><br>

**Group Policy** is the Windows feature used to centrally configure users and computers. The settings are stored inside a **Group Policy Object (GPO)**.

A useful mental model is:

```text
GPO = the rulebook
GPO link = where the rulebook is attached
```

A GPO does not affect an organizational unit merely because the GPO exists. It must be **linked** to a site, domain, or organizational unit, and the target object must be allowed to process it. A single GPO can have more than one link, although `GPO1` in this workflow was linked only to Paris.

Every GPO is divided into two major configuration halves:

```text
Computer Configuration
→ follows the computer object
→ normally processes during startup and policy refresh
→ applies regardless of which user signs in

User Configuration
→ follows the user object
→ normally processes during sign-in and policy refresh
→ applies regardless of which computer the user uses
```

This distinction is essential for `GPO1`. Its settings were created under **Computer Configuration**, so the computers in the Paris organizational-unit scope process them. Moving a user into the Paris organizational unit does not, by itself, make that user process the Computer Configuration half of `GPO1`.

Inside both Computer Configuration and User Configuration are two top-level branches:

```text
Policies
Preferences
```

- **Policies** are managed configuration settings intended for centralized enforcement. They commonly remove, replace, or revert their managed value when the GPO no longer applies, and applications may disable the related user interface so a user cannot override the setting.
- **Preferences** configure a desired value in many of the same locations an administrator could configure manually. They provide flexible options such as item-level targeting, but their values commonly remain after the GPO falls out of scope unless **Remove this item when it is no longer applied** is enabled for that preference item.

All Group Policy settings configured in this workflow were under the **Policies** branch.

> **Beginner note — multiple GPOs:** Windows generally processes policy in **Local → Site → Domain → Organizational Unit** order, with child organizational units processed after parent containers. A later applicable setting commonly wins when settings conflict, although options such as **Enforced**, **Block Inheritance**, security filtering, and loopback processing can change the result. Those exceptions were not required for this workflow, but they explain why GPO link location matters.

</details>

<details>
<summary><strong>▶ GPO Scope, Security Filtering, and Groups Named Inside Settings</strong><br>
</summary><br>

Three ideas can look similar in the interface but answer different questions:

1. **GPO link** — At which site, domain, or organizational unit can the GPO apply?
2. **Security filtering** — Which users or computers within that linked scope are permitted to process the GPO?
3. **A user or group named inside a setting** — Which security principal is the subject of that one configured rule?

For example, `GPO1` was linked to the Paris organizational unit, and `Paris Admins` was entered inside **Deny log on as a service**.

That does **not** mean `Paris Admins` was used as the GPO's security filter. It means computers processing `GPO1` receive a local security rule that denies members of `Paris Admins` the service-logon right.

</details>

<details>
<summary><strong>▶ Why Several Different Administrative Consoles Were Needed</strong><br>
</summary><br>

Active Directory is one connected system, but Microsoft exposes different object types and functions through different management consoles:

| Console | Main Use in This Workflow |
|---|---|
| Active Directory Users and Computers | Users, groups, organizational units, delegation, and the account-protection flag |
| Active Directory Sites and Services | Network sites, subnets, domain-controller site placement, and replication topology |
| Active Directory Administrative Center | Fine-Grained Password Policies and Active Directory Recycle Bin |
| Group Policy Management | Creating, linking, and selecting Group Policy Objects |
| Group Policy Management Editor | Configuring the settings inside a selected Group Policy Object |
| Server Manager | Installing server roles and starting domain-controller promotion |

> **Beginner note — console switching is expected:** The Fine-Grained Password Policy was not created in Group Policy Management because it is a **Password Settings Object**, not a GPO. The Active Directory Recycle Bin was also enabled through Active Directory Administrative Center. The need to move between consoles reflects how Windows separates directory objects, network topology, and policy management; it does not mean these are separate identity systems.

</details>

---

### Environment and Execution Context

This section documents the administrative environment, tools, directory objects, workflow boundaries, and high-level execution order.

**Note:** Each section is collapsible. Select the ▶ arrow to expand it.

<details>
<summary><strong>▶ Environment & Platform</strong><br>
</summary><br>

The work was performed in an existing `contoso.com` Active Directory environment containing one established domain controller and one Windows Server prepared for promotion.

```text
DC1 — existing domain controller and initial Relative Identifier Master
DC2 — domain-joined Windows Server promoted to an additional domain controller
```

As the IT systems administrator for the Paris office rollout, I received a sequence of infrastructure, identity, password-policy, recovery, and Group Policy requirements. I completed the changes directly through the appropriate Windows Server administrative consoles and Windows PowerShell.

The exact underlying hardware and virtualization platform were managed by the hosted environment and were outside the scope of this workflow.

</details>

<details>
<summary><strong>▶ Directory Objects Created or Modified</strong><br>
</summary><br>

| Object Type | Name or Value |
|---|---|
| Active Directory Site | `Paris` |
| Subnet | `172.16.1.0/24` |
| Additional Domain Controller | `DC2` |
| Operations Master Role | Relative Identifier Master moved to `DC2` |
| Organizational Unit | `Paris` |
| User Account | `ParisContractor` |
| Security Group | `Paris Admins` — Universal, Security |
| Password Settings Object | Domain Admins 16-character policy |
| Group Policy Objects | Default Domain Policy, Default Domain Controllers Policy, `GPO1` |

</details>

<details>
<summary><strong>▶ Tooling Used</strong><br>
</summary><br>

- **Server Manager** — installed the Active Directory Domain Services server role and launched domain-controller promotion.
- **Active Directory Domain Services Configuration Wizard** — added `DC2` as a domain controller in the existing `contoso.com` domain.
- **Active Directory Sites and Services** — created the Paris site, associated the Paris subnet, and validated the domain controller's site placement.
- **Active Directory Users and Computers (ADUC)** — created the Paris organizational unit, user, and group; managed group membership; delegated password-management rights; and accessed the operations-master interface.
- **Active Directory Administrative Center (ADAC)** — created the Fine-Grained Password Policy and enabled the Active Directory Recycle Bin.
- **Group Policy Management Console (GPMC)** — edited built-in Group Policy Objects and created and linked `GPO1`.
- **Group Policy Management Editor** — configured password, authentication, audit, and user-right settings inside Group Policy Objects.
- **Windows PowerShell** — located and modified multiple users, transferred the Relative Identifier Master when the graphical transfer encountered a directory-service error, and verified the environment.
- **Replication Administration (`repadmin`)** — checked and synchronized replication between domain controllers.
- **Network Domain (`netdom`)** — displayed the current Flexible Single Master Operations role holders.

</details>

<details>
<summary><strong>▶ Workflow Scope</strong><br>
</summary><br>

This workflow focused on the administrative requirements for the Paris office rollout:

- deployment of one additional domain controller,
- Paris site and subnet configuration,
- one Flexible Single Master Operations role transfer,
- Paris-specific directory objects and delegation,
- bulk management of Paris users,
- domain and fine-grained password policies,
- Active Directory Recycle Bin enablement,
- New Technology LAN Manager restriction,
- failed-logon auditing,
- and a user-right restriction.

Out-of-scope activities included:

- building the original `contoso.com` forest,
- configuring the underlying hosted virtual network,
- adding additional domains or forests,
- performing deep replication performance analysis,
- testing an actual New Technology LAN Manager-dependent legacy application,
- testing attacks such as pass-the-hash or New Technology LAN Manager relay,
- and creating production change-control or rollback documentation.

</details>

<details>
<summary><strong>▶ Workflow Map (High-Level)</strong><br>
</summary><br>

**Domain Controller and Topology Operations**

1. Create the `Paris` Active Directory site.
2. Map `172.16.1.0/24` to the Paris site.
3. Install the Active Directory Domain Services role on `DC2`.
4. Promote `DC2` as an additional domain controller.
5. Confirm `DC2` is assigned to the Paris site.
6. Validate domain-controller communication and replication.
7. Transfer the Relative Identifier Master role from `DC1` to `DC2`.
8. Verify the new role holder.

**Paris Directory Objects and Delegation**

9. Create the `Paris` organizational unit.
10. Create the `Paris Admins` Universal security group.
11. Create `ParisContractor`.
12. Configure account expiration for December 31, 2027.
13. Configure `ParisContractor` as a protected account.
14. Add `ParisContractor` to `Paris Admins`.
15. Delegate password-reset and force-change rights over the Paris organizational unit.

**Bulk Paris User Administration**

16. Preview all users whose City attribute equals `Paris`.
17. Store the Paris organizational unit path and matching users in PowerShell variables.
18. Move each matching user into the Paris organizational unit.
19. Require a password change at next logon.
20. Disable each moved account.
21. Verify location, enablement state, and password-change state.

**Password Policy and Recovery**

22. Configure a 12-character domain-wide minimum password length.
23. Create a 16-character Fine-Grained Password Policy for Domain Admins.
24. Enable the Active Directory Recycle Bin.

**Group Policy Security**

25. Set the Default Domain Controllers Policy to deny all New Technology LAN Manager authentication requests.
26. Create `GPO1` and link it only to the Paris organizational unit.
27. Configure failed-logon auditing.
28. Deny `Paris Admins` the right to log on as a service.

</details>

---

### Step-by-Step Execution

This section documents the work in the same dependency order I followed as the IT systems administrator: establish the Paris network and domain-controller infrastructure first, create the administrative identity structure second, perform the user migration third, and then apply password, recovery, authentication, audit, and user-right controls. Each phase begins with the operational requirement and explains both the resulting change and the reason it was needed.

**Note:** Each phase is collapsible. Select the ▶ arrow to view its detailed steps.

<details>
<summary><strong>▶ Phase 1 — Configure Domain Controller and Site Operations</strong><br>
→ creating the Paris site, promoting DC2, validating replication, and transferring the Relative Identifier Master role
</summary><br>

As the administrator responsible for extending `contoso.com` to the new office, I first needed Active Directory to recognize Paris as a network location and provide a local directory service there. This phase established Paris as a defined Active Directory site, added `DC2` as a second writable domain controller, and moved one domain-level operations-master responsibility to the new controller.

##### 🔷 Phase 1.1 — Create the Paris Active Directory site

Based on the requirement to support a new physical office in Paris, I first needed to represent that network location inside Active Directory. On `DC1`, I opened **Server Manager → Tools → Active Directory Sites and Services**.

I right-clicked **Sites**, selected **New Site**, entered `Paris`, and associated it with the available site link.

**What this does:** An Active Directory **site** is a logical representation of a physical network location, such as an office, campus, or data center. The site does not create a real network. It records the network location inside Active Directory so that authentication and replication can be managed according to physical connectivity.

**Why this matters:** Without a Paris site, Active Directory has no location object representing the new office. A client at the Paris office could still use the domain, but Active Directory would not have the topology information needed to prefer a nearby Paris domain controller or manage intersite replication appropriately.

##### 🔷 Phase 1.2 — Associate the Paris subnet

Creating the site established the location name, but Active Directory still needed a rule for recognizing which devices belong to it. I therefore remained in **Active Directory Sites and Services**, right-clicked **Subnets**, selected **New Subnet**, and entered:

```text
172.16.1.0/24
```

I then selected the `Paris` site as the site associated with that subnet.

**What this does:** A subnet is a range of Internet Protocol addresses. The `/24` prefix means that the first 24 bits identify the network, providing the address range normally represented as `172.16.1.0` through `172.16.1.255`.

By associating the subnet with Paris, Active Directory can evaluate a computer's Internet Protocol address and determine that the computer belongs to the Paris site.

**Why this matters:** The site name alone cannot identify a client location. The subnet mapping provides the actual rule:

```text
A device using an address in 172.16.1.0/24
        ↓
belongs to the Paris Active Directory site
```

This supports site-local domain-controller discovery and location-aware replication behavior.

##### 🔷 Phase 1.3 — Install the Active Directory Domain Services role on DC2

With the Paris network location defined, the next requirement was to provide a domain controller for that office. On `DC2`, I opened **Server Manager → Manage → Add Roles and Features**.

I selected a role-based installation, selected `DC2`, chose **Active Directory Domain Services**, accepted the required management features, and completed the role installation.

**What this does:** Installing the Active Directory Domain Services role copies the server components and management tools required for the machine to operate as a domain controller.

**Why this matters:** Installing the role does **not** automatically turn `DC2` into a domain controller. The process has two separate stages:

```text
Install the Active Directory Domain Services role
        ↓
Promote the server to a domain controller
```

After the first stage, the server has the necessary software. The second stage creates or replicates the directory database and makes the server an active participant in the domain.

> **Beginner note — Domain Name System dependency:** Active Directory relies heavily on **Domain Name System (DNS)** records to locate domain controllers and services. Before promotion, `DC2` must be able to resolve the `contoso.com` domain through the organization's Active Directory-aware DNS configuration; public DNS alone does not contain the internal service records needed for domain discovery.

##### 🔷 Phase 1.4 — Promote DC2 as an additional domain controller

After the role installation completed, I performed the separate promotion step required to turn the server into a working domain controller. I selected the Server Manager notification flag and chose **Promote this server to a domain controller**.

In the Active Directory Domain Services Configuration Wizard, I selected:

```text
Add a domain controller to an existing domain
```

I confirmed the domain:

```text
contoso.com
```

I configured the domain-controller options so that `DC2` was a writable domain controller, selected the `Paris` site, and configured the required **Directory Services Restore Mode (DSRM)** password.

The server restarted automatically after promotion.

**What this does:** Promotion changes `DC2` from a normal domain-joined server into an additional domain controller. It installs a local copy of the Active Directory database, configures domain-controller services, and replicates the existing `contoso.com` directory data from another domain controller.

**Why this matters:** Once promotion completes, both `DC1` and `DC2` can:

- authenticate domain users and computers,
- answer directory queries,
- process changes to directory objects,
- and replicate changes to each other.

This provides directory-service redundancy. Assigning `DC2` to Paris also positions it to serve devices associated with the Paris subnet.

> **Beginner note — Directory Services Restore Mode:** The **Directory Services Restore Mode (DSRM)** password is a recovery credential used when a domain controller starts in a special offline repair mode. It is not the normal password used for everyday domain administration.

> **Beginner note — one server, two administrative views:** In **Active Directory Sites and Services**, the `DC2` server object appears under the Paris site because that console shows network and replication topology. In **Active Directory Users and Computers**, the domain controller's computer account remains in the **Domain Controllers** organizational unit because that console shows directory-object placement and policy scope. These are different views of the same server, not duplicate machines.

##### 🔷 Phase 1.5 — Validate domain-controller discovery and replication

Before assigning an operations-master responsibility to the new controller, I needed to confirm that promotion and replication had completed successfully. After `DC2` restarted, I used Windows PowerShell to confirm that the domain recognized both controllers:

```powershell
Get-ADDomainController -Filter *
```

**Command breakdown:**

- `Get-ADDomainController` retrieves information about Active Directory domain controllers.
- `-Filter` specifies which controllers should be returned.
- `*` is a wildcard meaning **all domain controllers** that match the request.

**What this verifies:** The result should include both `DC1` and `DC2`. If `DC2` is missing, promotion or directory discovery did not complete successfully.

I checked replication health with:

```powershell
repadmin /replsummary
```

**Command breakdown:**

- `repadmin` is the Replication Administration command-line tool.
- `/replsummary` produces a summary of replication successes, failures, and delays between domain controllers.

**What this verifies:** A healthy summary should not show persistent replication failures between `DC1` and `DC2`.

When synchronization was needed, I used:

```powershell
repadmin /syncall /AdeP
```

**Command breakdown:**

- `repadmin /syncall` requests synchronization of directory partitions.
- `/A` includes all naming contexts held by the domain controller.
- `/d` displays servers using their distinguished names in the output.
- `/e` extends synchronization across sites in the enterprise.
- `/P` pushes changes outward to replication partners instead of relying only on the normal pull cycle.

**Why this matters:** The new domain controller must receive current directory data before it can reliably take on additional responsibilities. Replication health is especially important before transferring an operations-master role.

> **Beginner note — replication and multi-master administration:** Most Active Directory changes are **multi-master**, which means an administrator can create or change many objects on either writable domain controller and replication distributes the change. The five Flexible Single Master Operations roles are exceptions: each role has one current holder responsible for a specialized operation.

##### 🔷 Phase 1.6 — Transfer the Relative Identifier Master role to DC2

Based on the domain-controller requirements, I needed to move the **Relative Identifier (RID) Master** role to `DC2` after the new controller was online and replicating.

A Relative Identifier is the unique portion added to the domain Security Identifier to create the Security Identifier of a new user, computer, or group. The RID Master allocates blocks of Relative Identifiers to the domain controllers so that two controllers do not create objects with duplicate identifiers.

I first attempted the transfer through **Active Directory Users and Computers → Operations Masters → RID**. The graphical transfer initially reported that the current role holder could not be contacted.

I confirmed basic name resolution and connectivity:

```powershell
ping DC1.contoso.com
```

**Command breakdown:**

- `ping` sends Internet Control Message Protocol echo requests to a destination.
- `DC1.contoso.com` is the fully qualified domain name of the existing controller.

**What this verifies:** Successful responses confirm that `DC1` can be resolved through Domain Name System and reached at the network layer. A successful ping does not by itself prove that Active Directory services or replication are healthy, but it rules out a basic connectivity failure.

Because `DC1` was online, I did **not** seize the role. Instead, after replication was available, I ran the transfer from `DC1`:

```powershell
Move-ADDirectoryServerOperationMasterRole -Identity "DC2" -OperationMasterRole RIDMaster
```

**Command breakdown:**

- `Move-ADDirectoryServerOperationMasterRole` is the Active Directory PowerShell command used to move one or more Flexible Single Master Operations roles.
- `-Identity "DC2"` identifies the domain controller that should receive the role.
- `-OperationMasterRole RIDMaster` specifies that only the Relative Identifier Master role should move.
- No `-Force` parameter was used because this was a normal **transfer**, not an emergency **seizure**.

**Transfer vs. seizure:**

```text
Transfer
→ planned move
→ current holder is online
→ both domain controllers communicate

Seizure
→ emergency recovery
→ current holder is permanently unavailable
→ used only as a last resort
```

**Why this matters:** A role seizure when the current holder is still available can create unnecessary risk and cleanup work. The correct response to a temporary communication problem is to restore communication or replication and perform a normal transfer.

##### 🔷 Phase 1.7 — Verify the Relative Identifier Master role

After the transfer completed, I verified the resulting state rather than assuming the command succeeded. I verified the Flexible Single Master Operations role holders with:

```powershell
netdom query fsmo
```

**Command breakdown:**

- `netdom` is a Windows domain-management command.
- `query` requests information rather than changing configuration.
- `fsmo` asks specifically for the current Flexible Single Master Operations role holders.

The expected result for the relevant line was:

```text
RID pool manager    DC2.contoso.com
```

I could also verify only the domain's Relative Identifier Master property with:

```powershell
Get-ADDomain | Select-Object RIDMaster
```

**Command breakdown:**

- `Get-ADDomain` retrieves information about the current Active Directory domain.
- The pipeline symbol `|` sends the retrieved domain object to the next command.
- `Select-Object RIDMaster` displays only the `RIDMaster` property instead of every domain property.

##### 🔷 Phase 1.8 — Phase 1 findings

| Task | Outcome |
|---|---|
| Active Directory site created | `Paris` |
| Subnet mapped | `172.16.1.0/24` → Paris |
| Server role installed | Active Directory Domain Services on `DC2` |
| Additional domain controller | `DC2` promoted in `contoso.com` |
| Site assignment | `DC2` associated with Paris |
| Replication | Validated between `DC1` and `DC2` |
| Relative Identifier Master | Transferred from `DC1` to `DC2` |
| Role verification | `netdom query fsmo` confirmed `DC2.contoso.com` |

</details>

<details>
<summary><strong>▶ Phase 2 — Create Paris Directory Objects and Delegate Administration</strong><br>
→ creating the organizational unit, contractor account, Universal security group, protected-account configuration, and scoped password-management rights
</summary><br>

After the infrastructure was available, I needed to create a manageable identity boundary for the Paris office. This phase created the directory structure and administrative identities for Paris and applied least privilege by granting one group a specific capability over only one organizational unit.

##### 🔷 Phase 2.1 — Create the Paris organizational unit

With the Paris network and domain-controller infrastructure in place, I next needed an administrative container for Paris identities and computers. In **Active Directory Users and Computers**, I right-clicked the `contoso.com` domain, selected **New → Organizational Unit**, and created:

```text
Paris
```

**What this does:** An **Organizational Unit (OU)** is an Active Directory container used to organize objects such as users, groups, and computers. It is also a boundary for administrative delegation and Group Policy links.

**Why this matters:** The Paris organizational unit creates a clearly defined scope for:

- storing Paris-related objects,
- delegating password-management authority,
- and linking Paris-specific Group Policy settings.

An organizational unit does not automatically give its contents permission to anything. It controls organization and administrative scope.

> **Beginner note — an organizational unit is not a permissions group:** Moving a user into the Paris organizational unit changes the user's directory location and which OU-linked policies or delegated permissions can reach the object. It does not automatically make the user a Paris administrator. Administrative membership is handled separately through the `Paris Admins` security group.

##### 🔷 Phase 2.2 — Create the Paris Admins Universal security group

Based on the requirement to delegate a limited administrative function, I needed a reusable security principal rather than assigning rights directly to one contractor. Inside the Paris organizational unit, I selected **New → Group** and configured:

```text
Group name:  Paris Admins
Group scope: Universal
Group type:  Security
```

**What this does:** A security group is a directory object with its own Security Identifier. Permissions, delegated rights, and user-right restrictions can be assigned to the group as one entity.

**Universal scope** means that the group can contain supported members from across the forest and can be used to assign permissions throughout the forest.

**Why this matters:** Assigning rights to a group is more manageable than assigning them directly to one person. If another Paris administrator is added later, placing that account in `Paris Admins` automatically gives it the same delegated capability.

This is different from an organizational unit:

```text
Organizational Unit
→ where objects are organized
→ where Group Policy is linked
→ where administration can be delegated

Security Group
→ collection of members
→ receives permissions, rights, or restrictions
```

##### 🔷 Phase 2.3 — Create the ParisContractor account

After creating the group that would hold the delegated rights, I created the temporary identity that would perform the Paris password-management work. Inside the Paris organizational unit, I selected **New → User** and created the generic contractor account:

```text
ParisContractor
```

The user-creation wizard required a First name value, so `ParisContractor` was entered while keeping the final Full name and user logon name aligned with the identity requirement.

**What this does:** Creating the user produces a new security principal that can authenticate to the domain and receive permissions through group membership.

**Why this matters:** The Paris administration requirements called for a temporary contractor identity with limited responsibility. A dedicated account keeps its permissions and expiration separate from a permanent administrator. In a production environment, an individually attributable account would normally be preferred over a shared generic identity because it provides clearer accountability.

##### 🔷 Phase 2.4 — Configure account expiration

Because the account represented temporary contractor access, the requirements also called for a defined end date. I opened the `ParisContractor` properties, selected the **Account** tab, and set the account to expire on:

```text
December 31, 2027
```

**What this does:** After the expiration date, Active Directory prevents the account from authenticating even if its password is still correct.

**Why this matters:** Temporary and contractor accounts can become stale if nobody remembers to disable them when the engagement ends. Configuring the expiration date at account creation creates an automatic access cutoff.

##### 🔷 Phase 2.5 — Configure ParisContractor as a protected account

Because the contractor would receive a delegated administrative capability, I applied an additional credential-protection control. Based on the requirement to protect the delegated contractor account, I opened the `ParisContractor` account properties, selected the **Account** tab, and enabled **Account is sensitive and cannot be delegated**.

**What this does:** The setting marks the account as non-delegable. When Kerberos delegation is used, a trusted service can sometimes act on a user's behalf when connecting to another service. This account flag prevents `ParisContractor` credentials from being forwarded in that way.

**Why this matters:** `ParisContractor` receives a real administrative capability through `Paris Admins`. Preventing credential delegation reduces the chance that a service can reuse the contractor's identity to access another service. The control protects the account's credentials; it does not grant the account any new permissions.

> **Beginner note — protected account does not mean extra authority:** **Account is sensitive and cannot be delegated** is an account-protection flag, not a role or permission. It is also different from membership in the built-in **Protected Users** group. This workflow used the account flag required for `ParisContractor`; it did not add the contractor to Protected Users.

##### 🔷 Phase 2.6 — Add ParisContractor to Paris Admins

The account-protection flag did not grant administrative authority. To give the contractor the delegated role, I opened the `ParisContractor` properties, selected **Member Of**, and added:

```text
Paris Admins
```

**What this does:** The user becomes a member of the security group. Any rights or restrictions assigned to `Paris Admins` can then affect `ParisContractor`.

**Why this matters:** The contractor account itself was not granted permissions directly. The group serves as the administration layer:

```text
ParisContractor
        ↓ member of
Paris Admins
        ↓ receives
Delegated password-management capability
```

##### 🔷 Phase 2.7 — Delegate password management over the Paris organizational unit

With the group and contractor membership prepared, I assigned the required capability at the narrowest practical scope. I right-clicked the Paris organizational unit and selected **Delegate Control**.

In the Delegation of Control Wizard, I added `Paris Admins` and selected:

```text
Reset user passwords and force password change at next logon
```

**What this does:** The wizard writes specific access-control entries onto the Paris organizational unit. Members of `Paris Admins` can perform the selected password-management actions on user objects inside that organizational unit.

**Why this matters:** This is an example of **least privilege**. The group can manage passwords for Paris users, but it does not receive unrestricted rights over:

- the entire domain,
- users in other organizational units,
- domain controllers,
- Group Policy,
- or Domain Admins.

The delegation follows this scope:

```text
Who receives the right?
→ Paris Admins

What can the group do?
→ Reset passwords and require change at next logon

Where can it do it?
→ User objects in the Paris organizational unit
```

##### 🔷 Phase 2.8 — Phase 2 findings

| Task | Outcome |
|---|---|
| Organizational unit created | `Paris` |
| Security group created | `Paris Admins` — Universal, Security |
| User account created | `ParisContractor` |
| Expiration configured | December 31, 2027 |
| Protected-account control | **Account is sensitive and cannot be delegated** enabled |
| Group membership | `ParisContractor` added to `Paris Admins` |
| Delegated capability | Reset passwords and force change at next logon |
| Delegation scope | User objects inside the Paris organizational unit |

</details>

<details>
<summary><strong>▶ Phase 3 — Move and Secure Paris Users with Windows PowerShell</strong><br>
→ finding accounts by City, moving them into the Paris organizational unit, disabling them, requiring password changes, and validating the results
</summary><br>

Once the Paris organizational unit existed, I needed to relocate and secure the existing users associated with that office. This phase used Windows PowerShell to perform one consistent set of lifecycle actions against every account whose City attribute was `Paris`.

##### 🔷 Phase 3.1 — Preview users whose City attribute equals Paris

Based on the user-migration requirement, I needed to identify Paris users from directory data rather than manually guessing account names. Before modifying anything, I ran:

```powershell
Get-ADUser -Filter "City -eq 'Paris'" -Properties City | Select-Object Name, SamAccountName, City
```

**Command breakdown:**

- `Get-ADUser` retrieves Active Directory user objects.
- `-Filter` limits the result to users matching a condition.
- `"City -eq 'Paris'"` means the `City` attribute must equal the text `Paris`.
- `-eq` is the PowerShell comparison operator for **equals**.
- `-Properties City` requests the City property, which is not included in the smallest default result set.
- `|` is the pipeline. It sends the users returned by `Get-ADUser` into `Select-Object`.
- `Select-Object Name, SamAccountName, City` displays only the person's display name, logon name, and City value.

**What this does:** It produces a non-destructive preview of the exact accounts that the later script will change.

> **Beginner note — an attribute is stored information about an object:** Every Active Directory user has properties such as name, department, title, and City. A property may exist even when its value is blank. Here, `City` was used as selection data; it did not itself grant access or determine the user's organizational unit.

**Why this matters:** Bulk commands can modify many objects very quickly. Previewing the result first reduces the chance of moving or disabling the wrong accounts because of an incorrect filter.

##### 🔷 Phase 3.2 — Store the target organizational unit path

After confirming the correct user population, I prepared reusable values for the bulk operation. I created a variable containing the distinguished name of the Paris organizational unit:

```powershell
$targetOU = "OU=Paris,DC=contoso,DC=com"
```

**Command breakdown:**

- `$targetOU` is a PowerShell variable. A variable stores a value so that it can be reused.
- `=` assigns the value on the right to the variable on the left.
- `"OU=Paris,DC=contoso,DC=com"` is the **distinguished name** of the target organizational unit.
- `OU=Paris` identifies the Paris organizational unit.
- `DC=contoso` represents the `contoso` part of the domain name.
- `DC=com` represents the `com` part of the domain name.

**What this does:** Instead of repeating the full distinguished name for every move operation, the script can refer to `$targetOU`.

**Why this matters:** Variables improve readability and reduce typing mistakes. If the target location changed, only the variable would need to be updated.

##### 🔷 Phase 3.3 — Store the matching users

I then saved the matching directory objects as a collection that the loop could process. I stored the Paris user results in another variable:

```powershell
$users = Get-ADUser -Filter "City -eq 'Paris'" -Properties City
```

**Command breakdown:**

- `$users` stores a collection of user objects.
- `Get-ADUser` retrieves users from Active Directory.
- `-Filter "City -eq 'Paris'"` returns only users whose City attribute exactly equals `Paris`.
- `-Properties City` includes the City attribute in each returned user object.

**What this does:** The matching users are collected once and can then be processed by a loop.

##### 🔷 Phase 3.4 — Move, flag, and disable each user

With both the target location and user collection defined, I applied the required lifecycle actions consistently to each account. I used a `foreach` loop:

```powershell
foreach ($user in $users) {
    Move-ADObject -Identity $user.DistinguishedName -TargetPath $targetOU
    Set-ADUser -Identity $user.SamAccountName -ChangePasswordAtLogon $true
    Disable-ADAccount -Identity $user.SamAccountName
}
```

The same logic can also be entered on one line by separating the commands with semicolons:

```powershell
foreach ($user in $users) { Move-ADObject -Identity $user.DistinguishedName -TargetPath $targetOU; Set-ADUser -Identity $user.SamAccountName -ChangePasswordAtLogon $true; Disable-ADAccount -Identity $user.SamAccountName }
```

**Loop breakdown:**

- `foreach` starts a loop that repeats the enclosed commands.
- `($user in $users)` means: take one object at a time from the `$users` collection and temporarily call it `$user`.
- `{ ... }` contains the commands that run for each user.
- In the one-line version, semicolons separate commands that would otherwise appear on different lines.

**First command: move the object**

```powershell
Move-ADObject -Identity $user.DistinguishedName -TargetPath $targetOU
```

- `Move-ADObject` moves an Active Directory object to a new container or organizational unit.
- `-Identity $user.DistinguishedName` identifies the current user object by its complete distinguished name.
- `-TargetPath $targetOU` sends the object to the Paris organizational unit stored in `$targetOU`.

**Second command: require a password change**

```powershell
Set-ADUser -Identity $user.SamAccountName -ChangePasswordAtLogon $true
```

- `Set-ADUser` changes properties of an Active Directory user.
- `-Identity $user.SamAccountName` identifies the user through the Security Account Manager logon name.
- `-ChangePasswordAtLogon $true` enables the requirement that the user select a new password at the next successful sign-in.
- `$true` is the PowerShell Boolean value meaning enabled or yes.

**Third command: disable the account**

```powershell
Disable-ADAccount -Identity $user.SamAccountName
```

- `Disable-ADAccount` prevents the account from authenticating.
- `-Identity $user.SamAccountName` identifies which account should be disabled.

**What this does:** Every user matching City = Paris is moved, marked for a password change, and disabled.

**Why this matters:** The script guarantees that the same sequence is applied to the entire set. A manual process could easily move one user but forget to disable another or omit the password-change requirement.

The order also preserves the account objects rather than deleting them. Disabling is reversible and retains group membership, attributes, and audit context.

> **Beginner note — disabled now, password change later:** A disabled account cannot sign in, so the password-change prompt does not occur immediately. The **Change password at next logon** flag remains stored on the user object. If an administrator later re-enables the account, the user must change the password at the next successful sign-in.

##### 🔷 Phase 3.5 — Verify placement and account settings

After the bulk change completed, I queried the users again to validate the resulting state. I used the following verification command:

```powershell
Get-ADUser -Filter "City -eq 'Paris'" -Properties City, Enabled, pwdLastSet |
    Select-Object Name,
                  DistinguishedName,
                  Enabled,
                  @{Name="ChangeAtNextLogon";Expression={$_.pwdLastSet -eq 0}}
```

**Command breakdown:**

- `Get-ADUser -Filter "City -eq 'Paris'"` retrieves the same Paris population.
- `-Properties City, Enabled, pwdLastSet` requests the City, enabled-state, and password-last-set properties.
- `|` passes the user objects to `Select-Object`.
- `Name` displays the account's name.
- `DistinguishedName` displays the object's full directory location.
- `Enabled` displays whether authentication is allowed.
- `@{Name="ChangeAtNextLogon";Expression={...}}` creates a calculated output column.
- `Name="ChangeAtNextLogon"` labels the calculated column.
- `$_.pwdLastSet` refers to the current user's password-last-set value as it travels through the pipeline.
- `-eq 0` checks whether the value is zero. In this context, zero indicates that the user must change the password at the next logon.

The expected results were:

```text
DistinguishedName contains: OU=Paris,DC=contoso,DC=com
Enabled:                   False
ChangeAtNextLogon:         True
```

**Why this matters:** A command completing without red error text does not prove that every intended change occurred. Verification checks the directory's resulting state rather than assuming success.

##### 🔷 Phase 3.6 — Phase 3 findings

| Requirement | Validation |
|---|---|
| Identify Paris users | City attribute equals `Paris` |
| Move users | Distinguished name contains `OU=Paris` |
| Disable users | `Enabled` equals `False` |
| Require password change | `pwdLastSet` equals `0`, displayed as `ChangeAtNextLogon = True` |
| Preserve account objects | Accounts were disabled rather than deleted |

</details>

<details>
<summary><strong>▶ Phase 4 — Configure Password Policies and Directory Recovery</strong><br>
→ setting the domain baseline, applying a stricter policy to Domain Admins, and enabling the Active Directory Recycle Bin
</summary><br>

After organizing and securing the Paris identities, I needed to strengthen domain credential requirements and recovery readiness. This phase established a stronger password baseline, added an elevated requirement for the most privileged group, and enabled a forest-wide recovery feature for deleted directory objects.

##### 🔷 Phase 4.1 — Set the domain-wide minimum password length to 12

Based on the password-security requirements, I first established the broad minimum that should apply to ordinary domain accounts. I opened **Group Policy Management**, expanded:

```text
Forest: contoso.com
└── Domains
    └── contoso.com
```

I right-clicked **Default Domain Policy**, selected **Edit**, and navigated to:

```text
Computer Configuration
└── Policies
    └── Windows Settings
        └── Security Settings
            └── Account Policies
                └── Password Policy
```

I opened **Minimum password length**, defined the setting, and entered:

```text
12
```

> **Beginner note — what the Group Policy tree means here:** This setting was under **Computer Configuration → Policies**, even though it controls domain user passwords. Domain account policy is a special case: the effective domain password policy is processed by domain controllers from a GPO linked at the domain level. It is not configured under User Configuration for each individual user.

> **Beginner note — Policies vs. Preferences:** The setting was placed under **Policies**, the managed/enforced branch of Group Policy. The parallel **Preferences** branch is used for flexible configuration items that often behave more like centrally delivered administrator choices and may remain after the GPO no longer applies. No Preference item was required for this password policy.

**What this does:** The Default Domain Policy contains the domain's baseline password and account policies. Setting the minimum length to 12 means a newly selected domain password must contain at least 12 characters unless a Fine-Grained Password Policy applies to that user.

**Why this matters:** A longer minimum increases the possible password space and makes short-password guessing less practical. This is the broad baseline for standard users in the domain.

##### 🔷 Phase 4.2 — Create a Fine-Grained Password Policy for Domain Admins

The domain-wide setting established the baseline, but the privileged Domain Admins group required a stronger rule. I closed or minimized Group Policy Management and opened:

```text
Server Manager
→ Tools
→ Active Directory Administrative Center
```

I navigated to:

```text
contoso (local)
└── System
    └── Password Settings Container
```

In the Tasks pane, I selected **New → Password Settings** and configured:

```text
Name:                    Domain Admins 16 Character Policy
Precedence:              1
Minimum password length: 16
Directly applies to:     Domain Admins
```

**What this does:** This creates a **Password Settings Object (PSO)**. A Password Settings Object is the Active Directory object that stores a **Fine-Grained Password Policy (FGPP)**.

The policy applies directly to Domain Admins and requires a 16-character minimum instead of the domain default of 12.

**Why this matters:** Domain Admins can control the entire domain. A compromised Domain Admins account has a much larger impact than a compromised standard user account, so applying a stricter password requirement is a risk-based control.

**Precedence explanation:** If more than one Password Settings Object could apply to the same user, the lowest precedence number wins. A precedence of `1` therefore represents a very high-priority policy.

> **Beginner note — why the console changed:** Group Policy Management handles GPOs, but a Fine-Grained Password Policy is stored as a **Password Settings Object** inside Active Directory. That is why I moved to **Active Directory Administrative Center** instead of continuing in the Group Policy console.

**Important distinction:**

```text
Default Domain Policy
→ Group Policy Object
→ broad password baseline for the domain

Fine-Grained Password Policy
→ Password Settings Object
→ targets selected users or global security groups
```

##### 🔷 Phase 4.3 — Enable the Active Directory Recycle Bin

After strengthening password controls, I addressed recovery readiness so accidental directory-object deletion would be easier to reverse. I remained in **Active Directory Administrative Center**, selected the top-level `contoso (local)` node, and chose **Enable Recycle Bin** from the Tasks pane.

I confirmed the warning and refreshed the console.

**What this does:** The Active Directory Recycle Bin allows deleted directory objects to be restored with most of their important attributes and group memberships preserved.

**Why this matters:** Without the Recycle Bin, recovering an accidentally deleted user, group, or organizational unit can require more complex restoration procedures. The feature must be enabled **before** the deletion occurs to provide its full restoration benefit.

Enabling the feature is a forest-wide and effectively irreversible change, which is why the console displays a warning.

##### 🔷 Phase 4.4 — Optional PowerShell validation

The domain-wide minimum can be checked with:

```powershell
Get-ADDefaultDomainPasswordPolicy | Select-Object MinPasswordLength
```

**Command breakdown:**

- `Get-ADDefaultDomainPasswordPolicy` retrieves the domain's default password policy.
- `|` sends the policy object to `Select-Object`.
- `Select-Object MinPasswordLength` displays only the minimum-length value.

Expected value:

```text
12
```

The Fine-Grained Password Policy can be checked with:

```powershell
Get-ADFineGrainedPasswordPolicy "Domain Admins 16 Character Policy" |
    Select-Object Name, Precedence, MinPasswordLength
```

**Command breakdown:**

- `Get-ADFineGrainedPasswordPolicy` retrieves a Password Settings Object.
- `"Domain Admins 16 Character Policy"` identifies the policy by name.
- `Select-Object` displays the selected policy properties.

The assigned group can be checked with:

```powershell
Get-ADFineGrainedPasswordPolicySubject "Domain Admins 16 Character Policy"
```

- `Get-ADFineGrainedPasswordPolicySubject` displays the users or groups to which the policy directly applies.

The Recycle Bin can be checked with:

```powershell
Get-ADOptionalFeature "Recycle Bin Feature" |
    Select-Object -ExpandProperty EnabledScopes
```

**Command breakdown:**

- `Get-ADOptionalFeature "Recycle Bin Feature"` retrieves the Recycle Bin feature object.
- `-ExpandProperty EnabledScopes` displays the directory scopes in which the feature is enabled.
- A populated scope indicates that the feature is enabled.

##### 🔷 Phase 4.5 — Phase 4 findings

| Task | Outcome |
|---|---|
| Domain password policy | Minimum password length = 12 |
| Fine-Grained Password Policy | Minimum password length = 16 |
| Fine-Grained policy target | Domain Admins |
| Policy precedence | 1 |
| Active Directory Recycle Bin | Enabled |

</details>

<details>
<summary><strong>▶ Phase 5 — Configure Group Policy Security Settings</strong><br>
→ restricting New Technology LAN Manager, auditing failed logons, and denying service logon to Paris Admins
</summary><br>

The final requirements focused on centralized security enforcement. This phase modified one built-in Group Policy Object for domain-controller authentication and created one new Paris-specific Group Policy Object containing detective and preventive security controls.

##### 🔷 Phase 5.1 — Deny New Technology LAN Manager authentication through the Default Domain Controllers Policy

Based on the authentication-hardening requirement, I needed the domain controllers to reject the legacy protocol at the point where domain authentication is processed. I opened **Group Policy Management**, expanded the domain and the **Domain Controllers** organizational unit, right-clicked **Default Domain Controllers Policy**, and selected **Edit**.

I navigated to:

```text
Computer Configuration
└── Policies
    └── Windows Settings
        └── Security Settings
            └── Local Policies
                └── Security Options
```

I opened:

```text
Network security: Restrict NTLM: NTLM authentication in this domain
```

I defined the policy and selected:

```text
Deny all
```

**What this does:** New Technology LAN Manager (NTLM) is an older challenge-response authentication protocol. The configured policy instructs the domain controllers to reject covered NTLM pass-through authentication requests instead of accepting NTLM as a fallback.

**Why this matters:** NTLM is more exposed to attacks such as credential relay and hash reuse than modern Kerberos-based domain authentication. Restricting it removes a legacy authentication path.

**Why this was configured in the Default Domain Controllers Policy:** Domain controllers make domain-authentication decisions. The built-in policy is linked to the Domain Controllers organizational unit, where domain-controller computer accounts are stored.

> **Beginner note — do not confuse the two default GPOs:** **Default Domain Policy** is linked at the `contoso.com` domain root and was used for the broad domain password baseline. **Default Domain Controllers Policy** is linked to the **Domain Controllers** organizational unit and was used for a setting that domain-controller computers enforce. Similar names do not mean identical scope.

> In a production environment, organizations normally audit New Technology LAN Manager dependencies before selecting **Deny all** so that legacy applications, devices, or scripts are not unexpectedly broken. Based on the security requirement for this environment, the final configuration needed to be **Deny all**.

##### 🔷 Phase 5.2 — Create GPO1 and link it only to the Paris organizational unit

After completing the domain-controller-wide authentication setting, I created a separate policy for the narrower Paris computer scope. Back in Group Policy Management, I right-clicked the `Paris` organizational unit and selected:

```text
Create a GPO in this domain, and Link it here
```

I named the new **Group Policy Object (GPO)**:

```text
GPO1
```

**What this does:** This creates the policy object and a link from the Paris organizational unit to that policy.

**Why this matters:** A Group Policy Object is the collection of settings. A Group Policy link defines where the settings can apply.

```text
GPO1
→ the policy and its settings

Link from Paris OU to GPO1
→ the scope where those settings are processed
```

Creating and linking it from the Paris organizational unit means it is not linked at the domain root or to unrelated organizational units. This satisfies the requirement that it apply only within the Paris scope.

> **Beginner note — the link is the scope, not the folder where the GPO is stored:** GPOs are stored centrally under **Group Policy Objects**. The Paris link is what attaches `GPO1` to the Paris organizational unit. The same GPO could technically be linked elsewhere, but I did not create any additional links. Child organizational units would normally inherit the link unless inheritance or filtering changed that behavior.

##### 🔷 Phase 5.3 — Audit unsuccessful logon events

The first Paris-specific requirement was detective: the affected computers needed to record failed sign-in activity. I edited `GPO1` and navigated to:

```text
Computer Configuration
└── Policies
    └── Windows Settings
        └── Security Settings
            └── Advanced Audit Policy Configuration
                └── Audit Policies
                    └── Logon/Logoff
```

I opened **Audit Logon**, selected **Configure the following audit events**, and enabled:

```text
Failure
```

I left Success unchecked because the stated requirement was to record unsuccessful logon events specifically.

**What this does:** Computers that process `GPO1` record failed logon events in the Windows Security event log.

**Why this matters:** Failed logon records help administrators investigate:

- mistyped or expired credentials,
- disabled accounts attempting to authenticate,
- password-spraying activity,
- brute-force attempts,
- and unexpected access attempts.

This is a **detective control** because it records activity for monitoring and investigation rather than directly preventing the attempt.

> **Beginner note — where the evidence appears:** Group Policy does not create a separate Paris audit database. It configures each affected Windows computer to write matching events into that computer's **Security** event log. A security information and event management platform could later collect those logs centrally, but central collection was outside this workflow.

##### 🔷 Phase 5.4 — Deny Paris Admins the right to log on as a service

The second Paris-specific requirement was preventive: the delegated administrators should not be usable as Windows service accounts on affected computers. Still editing `GPO1`, I navigated to:

```text
Computer Configuration
└── Policies
    └── Windows Settings
        └── Security Settings
            └── Local Policies
                └── User Rights Assignment
```

I opened:

```text
Deny log on as a service
```

I defined the policy and added:

```text
Paris Admins
```

**What this does:** A user-right assignment controls which security principals are allowed or denied a system-level action. **Log on as a service** is the right required for an account to run a Windows service.

Naming `Paris Admins` inside **Deny log on as a service** means members of the group cannot be used as service accounts on computers that process `GPO1`.

**Why this matters:** `Paris Admins` exists for delegated, interactive administration of Paris users. It does not need to run background services. Explicitly denying that capability reduces the ways a stolen group member's credentials could be used for persistence or privilege abuse.

A Windows **deny** user right takes precedence over the corresponding allow right.

> **Beginner note — this does not block every type of sign-in:** **Deny log on as a service** blocks the service logon type used when Windows starts a service under an account. It does not, by itself, deny interactive desktop sign-in, Remote Desktop sign-in, or network access; those are controlled by separate user-right settings.

##### 🔷 Phase 5.5 — Clarify the organizational-unit scope and the named group

This setting uses two separate targeting concepts:

```text
Where is GPO1 linked?
→ Paris organizational unit

Which objects process these Computer Configuration settings?
→ computer objects in the Paris organizational unit

Who is named inside Deny log on as a service?
→ members of the Paris Admins security group
```

The `Paris Admins` group does not need to be located in the same organizational unit to be named inside the policy setting. The group is the **subject of the restriction**, while the computers are the **processors of the policy**.

This is also different from security filtering:

- **Organizational-unit link** defines the policy's location-based scope.
- **Security filtering** can further control which users or computers are allowed to process the Group Policy Object.
- **Paris Admins inside the setting** identifies the accounts that the resulting local user-right policy denies.

No separate `Paris Admins` security filter was required for this requirement. The new GPO retained its normal default filtering so eligible authenticated computer objects in the linked scope could process it.

> **Beginner note — Computer Configuration is the controlling side:** Both settings in `GPO1` are under **Computer Configuration → Policies**. Therefore, the computer object's organizational-unit location determines whether the computer processes them. A user can be a member of `Paris Admins` and can even be stored in the Paris organizational unit, but the user does not personally process these computer settings. Instead, a Paris-scoped computer receives a local rule that names the group.

##### 🔷 Phase 5.6 — Phase 5 findings

| Task | Outcome |
|---|---|
| Built-in Group Policy Object modified | Default Domain Controllers Policy |
| New Technology LAN Manager policy | Deny all |
| New Group Policy Object | `GPO1` |
| Group Policy link | `GPO1` → Paris organizational unit |
| Audit setting | Audit Logon — Failure |
| User-right assignment | Deny log on as a service |
| Restricted group | `Paris Admins` |
| Computer scope | Computers that process `GPO1` within the Paris organizational-unit scope |

</details>

---

### Evidence Examination Summary

| Task | Console or Tool | Object(s) Affected | Outcome |
|---|---|---|---|
| Create Paris site | Active Directory Sites and Services | `Paris` | Site created |
| Associate subnet | Active Directory Sites and Services | `172.16.1.0/24` | Mapped to Paris |
| Install server role | Server Manager | `DC2` | Active Directory Domain Services installed |
| Promote domain controller | Active Directory Domain Services Configuration Wizard | `DC2` | Added as a domain controller in `contoso.com` |
| Validate controllers | Windows PowerShell | `DC1`, `DC2` | Both controllers discovered |
| Validate replication | Replication Administration | `DC1`, `DC2` | Replication checked and synchronized |
| Transfer operations-master role | Active Directory Users and Computers / PowerShell | Relative Identifier Master | Moved to `DC2` |
| Verify role holder | Network Domain / PowerShell | Flexible Single Master Operations roles | `DC2.contoso.com` confirmed as RID pool manager |
| Create organizational unit | Active Directory Users and Computers | `Paris` | Organizational unit created |
| Create security group | Active Directory Users and Computers | `Paris Admins` | Universal security group created |
| Create contractor user | Active Directory Users and Computers | `ParisContractor` | Account created |
| Configure expiration | Active Directory Users and Computers | `ParisContractor` | Expires December 31, 2027 |
| Protect contractor | Active Directory Users and Computers | `ParisContractor` | **Account is sensitive and cannot be delegated** enabled |
| Add group membership | Active Directory Users and Computers | `ParisContractor`, `Paris Admins` | Contractor added to group |
| Delegate control | Delegation of Control Wizard | Paris organizational unit / `Paris Admins` | Password reset and force-change delegated |
| Locate Paris users | Windows PowerShell | Users with City = Paris | Matching population identified |
| Move users | Windows PowerShell | Matching Paris users | Moved into Paris organizational unit |
| Secure moved users | Windows PowerShell | Matching Paris users | Disabled and flagged for password change |
| Verify bulk changes | Windows PowerShell | Matching Paris users | Location and account states confirmed |
| Update domain password policy | Group Policy Management | Default Domain Policy | Minimum length set to 12 |
| Create Fine-Grained Password Policy | Active Directory Administrative Center | Domain Admins | Minimum length set to 16, precedence 1 |
| Enable recovery feature | Active Directory Administrative Center | Forest | Active Directory Recycle Bin enabled |
| Restrict legacy authentication | Group Policy Management | Default Domain Controllers Policy | NTLM authentication set to Deny all |
| Create Paris policy | Group Policy Management | `GPO1`, Paris organizational unit | GPO created and linked |
| Configure audit policy | Group Policy Management Editor | Computers processing `GPO1` | Failed logons audited |
| Configure user-right restriction | Group Policy Management Editor | `Paris Admins` on affected computers | Log on as a service denied |

---

### What I Learned (Skills Demonstrated)

Through this workflow, I learned how to:

- Explain the relationship between a physical office, an Active Directory site, and an Internet Protocol subnet.
- Install the Active Directory Domain Services role and distinguish role installation from domain-controller promotion.
- Add a writable domain controller to an existing domain.
- Explain why multiple domain controllers improve availability and site-local authentication.
- Check domain-controller discovery and replication through command-line tools.
- Understand the purpose of the Relative Identifier Master.
- Distinguish a planned Flexible Single Master Operations role transfer from an emergency seizure.
- Troubleshoot a failed role transfer without using the force option unnecessarily.
- Create an organizational unit and explain how it differs from a security group.
- Create a Universal security group and explain its role in permission assignment.
- Create a temporary contractor account and configure an automatic expiration date.
- Protect a delegated administrative account by enabling **Account is sensitive and cannot be delegated** and explain how that differs from assigning permissions.
- Apply least privilege by delegating only password-management capability over one organizational unit.
- Use an Active Directory attribute to locate a specific population of users.
- Use PowerShell variables to store reusable values.
- Use an Active Directory filter to identify objects safely before changing them.
- Use a `foreach` loop to perform consistent bulk changes.
- Move, disable, and flag multiple accounts for password changes.
- Use pipelines and calculated properties to verify final state.
- Configure a domain-wide password baseline through the Default Domain Policy.
- Create a Password Settings Object for a Fine-Grained Password Policy.
- Apply a stricter password requirement to Domain Admins.
- Enable the Active Directory Recycle Bin before it is needed.
- Modify the Default Domain Controllers Policy to restrict legacy authentication.
- Explain the difference between a Group Policy Object and a Group Policy link.
- Link a Group Policy Object to one organizational unit.
- Configure advanced audit policy for failed logons.
- Configure a user-right assignment that denies service logon.
- Distinguish the computers processing a Group Policy Object from the users or groups named inside its settings.

This workflow strengthened my understanding that Active Directory administration combines identity and access management, Windows Server administration, directory topology, PowerShell automation, policy enforcement, privileged-access security, and investigation readiness.

---

### Key Takeaways

This workflow showed that the individual Active Directory concepts build on one another.

```text
Define the network location
(Paris site + subnet)
        ↓
Deploy a local directory service
(DC2 promotion + replication)
        ↓
Assign a unique domain responsibility
(Relative Identifier Master transfer)
        ↓
Create an administrative boundary
(Paris organizational unit)
        ↓
Create a reusable delegated role
(Paris Admins security group)
        ↓
Protect and limit the administrator
(non-delegable credentials + scoped delegation)
        ↓
Manage user lifecycle consistently
(attribute query + PowerShell bulk changes)
        ↓
Strengthen passwords and recovery
(domain policy + Fine-Grained policy + Recycle Bin)
        ↓
Reduce authentication risk and increase visibility
(NTLM restriction + failed-logon auditing)
        ↓
Reduce credential-abuse paths
(Deny log on as a service)
```

The most important lesson is that Active Directory security is not one setting. It is the cumulative result of:

- placing directory services where users need them,
- verifying replication before assigning critical roles,
- structuring objects into meaningful administrative boundaries,
- assigning capabilities through security groups,
- delegating narrowly instead of granting broad privileges,
- automating repetitive identity tasks carefully,
- validating every bulk change,
- applying stronger controls to higher-risk accounts,
- enabling recovery before an incident,
- removing unnecessary legacy authentication,
- and recording the activity needed for investigation.

Each individual change in this workflow was limited in scope, but together they demonstrate a layered and operationally realistic approach to administering and securing an Active Directory domain. A beginner can understand the overall model as: sites and subnets describe network location; domain controllers provide the directory service; organizational units provide administrative scope; groups collect members; delegation grants narrow capabilities; PowerShell applies repeatable changes; and Group Policy centrally configures the users or computers that fall within its scope.
