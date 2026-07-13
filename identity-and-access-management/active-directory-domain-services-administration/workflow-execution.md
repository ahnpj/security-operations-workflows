# Active Directory Domain Services Administration — Domain Controller Operations, User Management, Password Policies, and Security Configuration

This workflow demonstrates hands-on Active Directory Domain Services (AD DS) administration across a two-domain-controller lab environment (`TAILWIND-DC1` and `TAILWIND-MBR1`, domain `tailwindtraders.internal`), covering domain controller promotion, FSMO role transfer, AD site/subnet configuration, organizational unit and user account management, security group and Protected Users administration, delegation of control, domain and fine-grained password policy configuration, AD Recycle Bin enablement, NTLM restriction, advanced audit policy configuration, and user rights assignment.

The main tools used are: **Server Manager**, the **Active Directory Domain Services Configuration Wizard**, **Active Directory Users and Computers (ADUC)**, the **Active Directory Administrative Center (ADAC)**, **Active Directory Sites and Services**, and the **Group Policy Management Console (GPMC)**. See **[Environment and Execution Context](#environment-and-execution-context)** section below.

---

### Overview

This project focused on four operational areas of Active Directory administration, performed in sequence against the same lab domain.

1. The first portion involved **Preparing the Lab Environment** — installing and configuring Hyper-V with an internal NAT switch on the host machine, building and configuring the `TAILWIND-DC1` virtual machine and promoting it to create the new `tailwindtraders.internal` forest, and building and configuring the `TAILWIND-MBR1` virtual machine as a domain-joined member server. This established the two-machine lab domain that every subsequent phase of the workflow was performed against.

2. The second portion involved **Domain Controller Operations** — promoting the now-domain-joined `TAILWIND-MBR1` member server to a second domain controller within `tailwindtraders.internal`, transferring the RID Flexible Single Master Operations (FSMO) role from `TAILWIND-DC1` to `TAILWIND-MBR1`, and creating a new Active Directory site (`Sydney`) with an associated subnet (`172.16.1.0/24`).

3. The third portion involved **User Management Operations** — creating three organizational units (`Sydney`, `Melbourne`, `Brisbane`), creating and relocating user accounts into each OU, creating a security group (`Sydney Administrators`), configuring a user as a Protected User, delegating password-reset control over the Sydney OU to the Sydney Administrators group, configuring and validating a City attribute on a user account, disabling a user account, and resetting another user's password.

4. The fourth portion involved **Password Policies** — configuring the domain-wide minimum password length, creating a Fine-Grained Password Policy (FGPP) scoped specifically to the Domain Admins group, and enabling the Active Directory Recycle Bin.

5. The fifth portion involved **Security Settings** — restricting NTLM authentication domain-wide via the Default Domain Controller Policy, configuring advanced audit policy to log account management activity on the Sydney OU, and configuring a "Deny log on as a service" user rights assignment for the Sydney Administrators group.

This workflow demonstrates that Active Directory administration involves more than just creating accounts — it spans virtualization and environment setup, infrastructure-level domain controller operations, day-to-day identity lifecycle management, policy-driven password enforcement, and security hardening configured through Group Policy.

> **Workflow vs Execution vs Writeup (Terminology Used Here)**
> - **Workflows** refer to repeatable AD DS administration tasks such as domain controller promotion, user lifecycle management, and policy configuration.
> - **Executions** refer to the hands-on administration performed using Server Manager, ADUC, ADAC, Active Directory Sites and Services, and the Group Policy Management Console.
> - **Writeups** document configuration steps, analyst observations, tool usage, and administrative reasoning.

> 👉 For a **detailed, step-by-step walkthrough of how this workflow was executed — complete with screenshot placeholders**, see the **[Step-by-Step Execution](#step-by-step-execution)** section below.

---

### Purpose and Analyst Focus

#### ▶ Purpose

The purpose of this workflow is to demonstrate practical administration of Active Directory Domain Services across infrastructure operations, identity lifecycle management, password policy enforcement, and directory security hardening.

The environment preparation portion focused on standing up the virtualization host and both lab virtual machines from scratch — installing Hyper-V, configuring an internal NAT switch, building `TAILWIND-DC1` and promoting it as the first domain controller of a brand-new forest, and building `TAILWIND-MBR1` and joining it to that domain as a member server. This matters because none of the later identity, policy, or security configuration work in this project is possible without a working domain to configure in the first place — this phase is what turns two blank virtual machines into an actual, usable Active Directory lab.

The domain controller operations portion focused on promoting a second domain controller, transferring a FSMO role, and configuring AD site topology. This matters because most enterprise environments run multiple domain controllers for redundancy and performance, FSMO roles need to be distributed or transferred deliberately (not left on a single point of failure), and site topology directly affects authentication traffic and replication behavior across physical locations.

The user management portion focused on structuring the directory with organizational units, creating and relocating accounts, creating a security group, configuring a Protected User, delegating administrative control, and performing routine account lifecycle actions (attribute configuration, disablement, password reset). This matters because OU structure and delegation are what allow large organizations to distribute administrative work without granting broad domain admin rights, and routine lifecycle actions are the bulk of day-to-day identity administration work.

The password policy portion focused on configuring a domain-wide password policy, layering a stricter Fine-Grained Password Policy onto a privileged group, and enabling the AD Recycle Bin. This matters because privileged accounts often warrant stricter password requirements than the rest of the organization, and enabling the Recycle Bin before it's needed is a low-cost safeguard against accidental object deletion.

The security settings portion focused on restricting NTLM authentication, configuring advanced audit policy, and configuring a "Deny log on as a service" user right. This matters because legacy authentication protocols like NTLM are a common target for relay and downgrade attacks, account management auditing is essential for detecting unauthorized privilege changes, and explicitly denying unnecessary logon rights to security groups reduces the attack surface available to a compromised account.

#### ▶ Analyst Focus

The analyst focus is on understanding not just how to click through each administrative wizard, but what each configuration change actually does to the directory and why it matters operationally and from a security hardening perspective.

This includes:

- promoting a member server to a domain controller and understanding what that promotion actually changes,
- understanding what a FSMO role is and why transferring one matters,
- understanding how AD sites and subnets relate to physical network topology,
- structuring a directory using organizational units,
- creating and relocating user accounts between OUs,
- creating a security group and understanding group scope,
- understanding what the Protected Users group enforces that standard group membership does not,
- delegating a specific, limited administrative capability rather than granting full control,
- configuring and searching on a custom user attribute,
- performing account disablement and password reset as routine lifecycle actions,
- understanding the difference between a domain password policy and a Fine-Grained Password Policy,
- understanding why the AD Recycle Bin is not enabled by default and why that matters,
- understanding why NTLM restriction is a meaningful hardening step,
- understanding the value of advanced audit policy over basic auditing,
- understanding user rights assignment as a security control distinct from group membership alone.

The goal is not just to complete each wizard screen. The goal is to understand the underlying AD DS concept behind each task well enough to explain why an organization would perform it and what risk or operational need it addresses.

---

### What This Workflow Demonstrates

This workflow demonstrates how to:

- Promote a member server to a domain controller within an existing domain.
- Transfer a FSMO role (RID Master) between domain controllers.
- Create an Active Directory site and associate a subnet with that site.
- Create organizational units to structure the directory.
- Create user accounts and configure account properties (account expiration).
- Copy a user account to quickly provision similarly-configured accounts.
- Relocate user accounts between organizational units.
- Create a security group and add a member to it.
- Configure a user account as a Protected User.
- Delegate a specific administrative capability over an OU using the Delegation of Control Wizard.
- Configure a custom user attribute (City) and validate it using the Find/Advanced Find feature.
- Disable a user account.
- Reset a user account's password.
- Configure the domain-wide password policy via the Default Domain Policy.
- Create and apply a Fine-Grained Password Policy scoped to a specific security group.
- Enable the Active Directory Recycle Bin.
- Restrict NTLM authentication domain-wide via the Default Domain Controller Policy.
- Create and link a new GPO scoped to an OU.
- Configure advanced audit policy for account management events.
- Configure a "Deny log on as a service" user rights assignment for a security group.

This workflow also demonstrates the layered nature of Active Directory security configuration — a domain-wide password policy applies broadly, while a Fine-Grained Password Policy overrides it for a specific, higher-risk group; a GPO linked at the domain level applies broadly, while a GPO linked at the OU level applies more narrowly and can carry more targeted (and in this case, more restrictive) settings.

---

### Investigation and Security Operations Relevance

Active Directory Domain Services underpins identity and access management for the vast majority of enterprise environments, and how it is configured directly affects an organization's exposure to credential-based attacks, privilege escalation, and lateral movement.

This workflow can help answer questions such as:

- Why would an organization run more than one domain controller, and what happens if a FSMO role holder becomes unavailable?
- How does Active Directory site topology affect authentication and replication traffic across physical locations?
- How can organizational structure (OUs) be used to scope administrative delegation instead of granting broad rights?
- What additional protections does the Protected Users group provide beyond standard group membership?
- Why might a specific group need a stricter password policy than the rest of the domain?
- Why would an organization enable the AD Recycle Bin proactively rather than after an accidental deletion occurs?
- Why is restricting NTLM authentication considered a meaningful security hardening step?
- Why does advanced audit policy provide more investigative value than basic auditing?
- Why would a security group need to be explicitly denied a logon right it wouldn't normally be expected to need?

These questions matter because AD DS misconfiguration — overly broad delegation, weak password policies on privileged accounts, unrestricted legacy authentication protocols, and insufficient auditing — are consistently among the most common findings in real-world Active Directory security assessments and incident investigations.

The table below summarizes the role of each configuration area in this workflow:

| Area | What Was Configured | Why It Matters |
|---|---|---|
| Domain Controller Promotion | Second domain controller added to the domain | Redundancy, load distribution, and site-local authentication |
| FSMO Role Transfer | RID Master moved to the new domain controller | Ensures FSMO roles aren't left solely on a single, potentially unavailable DC |
| AD Site and Subnet | New site and subnet mapping created | Aligns authentication/replication topology with physical network layout |
| Organizational Units | Directory structured into OUs | Enables scoped administration, delegation, and Group Policy application |
| User and Group Management | Accounts, groups, and attributes configured | Core identity lifecycle and access management activity |
| Protected Users | Privileged-adjacent account hardened | Restricts use of weaker/legacy credential caching and authentication mechanisms |
| Delegation of Control | Scoped administrative rights granted | Reduces reliance on broad Domain Admin rights for routine tasks |
| Domain Password Policy | Minimum password length increased domain-wide | Baseline password strength enforcement |
| Fine-Grained Password Policy | Stricter policy applied to Domain Admins | Higher-risk accounts held to a stronger standard than the rest of the domain |
| AD Recycle Bin | Enabled proactively | Reduces recovery time and risk from accidental object deletion |
| NTLM Restriction | NTLM authentication denied domain-wide | Reduces exposure to NTLM relay and downgrade-style attacks |
| Advanced Audit Policy | Account management auditing enabled on an OU | Improves visibility into account and group changes for detection/investigation |
| User Rights Assignment | Deny log on as a service configured for a group | Reduces attack surface if that group's credentials are compromised |

---

### Environment and Execution Context

This section documents the tools, lab domain, and execution environment used during the workflow.

**Note:** Each section is collapsible. Click the ▶ arrow to expand and view details on platform, tools, evidence sources, workflow scope, and the high-level execution map.

<details>
<summary><strong>▶ Environment & Platform</strong><br>
</summary><br>

The workflow was performed in a virtualized lab environment running two Windows Server 2022 Evaluation Edition virtual machines within the `tailwindtraders.internal` domain:

```text
TAILWIND-DC1   — existing domain controller
TAILWIND-MBR1  — member server, promoted to a domain controller during this workflow
```

Administrative actions were performed while signed in as `TAILWINDTRADERS\Administrator`.

</details>

<details>
<summary><strong>▶ Directory Objects Created or Modified</strong><br>
</summary><br>

| Object Type | Name(s) |
|---|---|
| Domain Controller | `TAILWIND-MBR1` (promoted) |
| AD Site | `Sydney` |
| Subnet | `172.16.1.0/24` |
| Organizational Units | `Sydney`, `Melbourne`, `Brisbane` |
| User Accounts | `SydneyContractor`, `MelbourneContractor`, `BrisbaneContractor` |
| Security Group | `Sydney Administrators` (Universal scope) |
| Password Settings Object (FGPP) | `Domain Admin Password Policy` |
| Group Policy Objects | `Default Domain Policy` (edited), `Default Domain Controller Policy` (edited), `SydneyOUPolicy` (created) |

</details>

<details>
<summary><strong>▶ Tooling Used</strong><br>
</summary><br>

The tools and consoles used during execution included:

- **Server Manager** — used to add the AD DS role and launch domain controller promotion.
- **Active Directory Domain Services Configuration Wizard** — used to promote `TAILWIND-MBR1` to a domain controller.
- **Active Directory Users and Computers (ADUC)** — used for OU creation, user/group management, delegation, attribute configuration, account disablement, and password reset.
- **Active Directory Administrative Center (ADAC)** — used for Fine-Grained Password Policy configuration and AD Recycle Bin enablement.
- **Active Directory Sites and Services** — used to create the Sydney site and associated subnet.
- **Group Policy Management Console (GPMC)** and **Group Policy Management Editor** — used to configure the domain password policy, NTLM restriction, advanced audit policy, and user rights assignment.

</details>

<details>
<summary><strong>▶ Workflow Scope</strong><br>
</summary><br>

This workflow focused on guided, hands-on AD DS administration across domain controller operations, identity lifecycle management, password policy, and security configuration.

Out-of-scope activities included:

- deploying additional domain controllers beyond the second one promoted in this workflow,
- configuring AD DS in a multi-domain or multi-forest topology,
- validating replication behavior between sites in depth,
- scripting these tasks via PowerShell (all actions were performed through the graphical consoles),
- penetration testing or attack simulation against the configured environment.

Those activities could occur in later, more advanced Active Directory security or automation-focused workflows.

</details>

<details>
<summary><strong>▶ Workflow Map (High-Level)</strong><br>
</summary><br>

**Prepare the Lab Environment**
1. Install Hyper-V on the host machine and configure an internal NAT switch.
2. Build the `TAILWIND-DC1` virtual machine, install Windows Server 2022, configure networking, rename the computer, install AD DS, and promote it as the first domain controller of a new forest (`tailwindtraders.internal`).
3. Build the `TAILWIND-MBR1` virtual machine, install Windows Server 2022, configure networking, rename the computer, and join it to the `tailwindtraders.internal` domain as a member server.

**Domain Controller Operations**
4. Promote `TAILWIND-MBR1` to a domain controller.
5. Transfer the RID FSMO role to `TAILWIND-MBR1`.
6. Create the `Sydney` AD site and associate the `172.16.1.0/24` subnet.

**User Management Operations**
7. Create the `Sydney`, `Melbourne`, and `Brisbane` OUs.
8. Create `SydneyContractor` and configure account expiration.
9. Copy `SydneyContractor` to create `MelbourneContractor` and `BrisbaneContractor`; relocate each to its OU.
10. Create the `Sydney Administrators` group and add `SydneyContractor`.
11. Add `SydneyContractor` to the Protected Users group.
12. Delegate password reset/force-change control over the Sydney OU to Sydney Administrators.
13. Configure the City attribute on `SydneyContractor` and validate via Find.
14. Disable `MelbourneContractor`.
15. Reset the password for `BrisbaneContractor`.

**Password Policies**
16. Configure the domain password policy (minimum length 14).
17. Create and apply the `Domain Admin Password Policy` FGPP (minimum length 16, precedence 1) to Domain Admins.
18. Enable the Active Directory Recycle Bin.

**Security Settings**
19. Restrict NTLM authentication domain-wide via the Default Domain Controller Policy.
20. Create `SydneyOUPolicy`, link it to the Sydney OU, and enable advanced auditing for account management.
21. Configure "Deny log on as a service" for Sydney Administrators via `SydneyOUPolicy`.

</details>

---

### Step-by-Step Execution

This section documents the workflow in the same order it was performed: domain controller operations first, followed by user management operations, password policies, and finally security settings.

**Note:** Each section is collapsible. Click the ▶ arrow to expand and view the detailed steps.

<details>
<summary><strong>▶ Phase 0 — Prepare the Lab Environment</strong><br>
→ installing Hyper-V, and building the domain controller and member server virtual machines
</summary><br>

This phase focused on standing up the virtualization host and both lab virtual machines from a blank state — before any Active Directory-specific configuration could begin. Nothing in Phases 1 through 4 is possible until this phase establishes a working, network-connected domain.

##### 🔷 Phase 0.1 — Install Hyper-V and configure an internal NAT switch

On the Windows 11 host machine, I enabled the **Hyper-V** optional feature via **Settings → System → Optional Features → More Windows Features**, then restarted the computer to complete installation.

**What this does:** Hyper-V is Windows' built-in Type-1 hypervisor. Enabling it turns the physical host into a machine capable of running virtual machines, which is what let me build two separate "servers" (`TAILWIND-DC1` and `TAILWIND-MBR1`) on a single physical computer instead of needing two real machines.

After restarting, I opened **Hyper-V Manager**, opened **Hyper-V Settings**, and redirected the default storage locations for both **Virtual Machines** and **Virtual Machine Hard Disks** to a dedicated `C:\VirtualMachines` folder structure.

**Why this matters:** Hyper-V's default storage paths are buried inside the Windows user profile. Redirecting them to a clearly named, top-level folder makes the VM files easy to locate later — useful both for cleanup (removing the whole lab afterward) and for understanding disk space usage, since Evaluation Edition VHDs can grow fairly large.

I then opened an administrative PowerShell session and ran:

```powershell
New-VMSwitch -SwitchName "NATSwitch" -SwitchType Internal
New-NetIPAddress -IPAddress 10.10.10.1 -PrefixLength 24 -InterfaceAlias "vEthernet (NATSwitch)"
New-NetNat -Name "NATNetwork" -InternalIPInterfaceAddressPrefix "10.10.10.0/24"
```

**What this does:** These three commands create an **internal** Hyper-V virtual switch (meaning it lets VMs talk to each other and to the host, but isn't directly bridged to the physical network), assign the host itself the address `10.10.10.1` on that internal network so it can act as a gateway, and then configure Network Address Translation (NAT) so VMs on that internal `10.10.10.0/24` network can still reach the internet through the host's real network connection.

**Why this matters:** A completely isolated internal switch would keep the lab self-contained (no risk of interfering with the physical LAN), but the VMs would have no way to reach the internet — which matters here since both VMs still need outbound access (for Windows activation checks, and general connectivity even though the domain itself is fully self-contained). NAT solves that by letting outbound traffic leave through the host while keeping the lab network logically separate from the physical network.

##### 🔷 Phase 0.2 — Build TAILWIND-DC1 and promote it as the first domain controller

Using **Hyper-V Manager → New → Virtual Machine**, I created a Generation 2 VM named `TAILWIND-DC1` with 4096 MB of startup memory (dynamic memory enabled), connected to `NATSwitch`, and attached the downloaded Windows Server 2022 Evaluation Edition ISO as its installation source.

**What this does:** This provisions a virtual machine shell — CPU, memory, virtual disk, and a virtual NIC connected to the NAT switch created in the previous step — and points it at the installation media needed to actually install an operating system onto that virtual hardware.

Before starting the VM, I opened its Settings and confirmed **Use automatic checkpoints** was disabled.

**Why this matters:** Automatic checkpoints periodically snapshot VM state, which is useful in some scenarios but can create confusing extra disk overhead and unexpected "rollback" points during a lab that's meant to represent a single continuous build-out. Disabling it keeps the VM's disk state straightforward for the rest of the project.

I started the VM, booted from the attached ISO, and installed **Windows Server 2022 Standard Evaluation (Desktop Experience)**, selecting a custom installation to a blank virtual disk. After installation completed and the VM restarted, I set the built-in Administrator password and signed in.

**What this does:** This installs a full, GUI-based Windows Server operating system onto the virtual machine — the "Desktop Experience" edition specifically, rather than Server Core, so Server Manager and the graphical AD DS tools used throughout the rest of this project would be available.

I then configured static networking on `TAILWIND-DC1`:

```text
IP address:        10.10.10.10
Subnet mask:        255.255.255.0
Default gateway:    10.10.10.1
Preferred DNS:      1.1.1.1
Alternate DNS:      8.8.8.8
```

**Why this matters:** A domain controller needs a stable, predictable IP address — DHCP-assigned addresses can change, which would break domain functionality if the DC's address shifted unexpectedly. The gateway (`10.10.10.1`) points back at the host machine's side of the NAT switch configured in Phase 0.1, and the external DNS servers (`1.1.1.1`, `8.8.8.8`) give the machine internet name resolution *before* it becomes a domain controller and starts serving its own DNS for the domain.

I renamed the computer to `TAILWIND-DC1` via **Server Manager → Local Server → Computer Name**, restarting to apply the change, then used **Add Roles and Features** to install the **Active Directory Domain Services** role.

**What this does:** Installing the AD DS role adds the binaries and management tools needed to run Active Directory on this server, but — same as in Phase 1 later in this workflow — it does **not** by itself make the server a domain controller. That still requires a separate promotion step.

From the Server Manager notification flag, I selected **Promote this server to a domain controller**, launching the AD DS Configuration Wizard. On the Deployment Configuration page, I selected **Add a new forest** and set the root domain name to `tailwindtraders.internal`, then accepted the default Domain Controller options (providing a DSRM password), DNS Options, Additional Options, and Paths, before completing the Prerequisites Check and selecting **Install**.

**What this does:** This is the step that actually creates the `tailwindtraders.internal` Active Directory forest and domain from scratch, with `TAILWIND-DC1` as its first domain controller. Selecting "Add a new forest" (rather than "add a domain controller to an existing domain," which is what Phase 1 uses later for `TAILWIND-MBR1`) is the key distinction — this is the one moment in the entire project where the domain itself is created rather than joined.

**Why this matters:** Everything in Phases 1 through 4 of this project — every OU, user, group, password policy, and GPO — only exists because this step created the domain they all live inside. Choosing "new forest" here versus "existing domain" later (in Phase 1) is what separates *creating* a domain from *extending* one, and mixing those up would either fail outright or create an entirely separate, unrelated domain instead of extending `tailwindtraders.internal`.

The VM restarted automatically to complete promotion, and I signed back in as `tailwindtraders\administrator`.

##### 🔷 Phase 0.3 — Build TAILWIND-MBR1 and join it to the domain as a member server

I repeated the same virtual machine creation process — Generation 2, 4096 MB dynamic memory, connected to `NATSwitch`, Windows Server 2022 Evaluation Edition ISO — this time naming the VM `TAILWIND-MBR1`, and again confirming automatic checkpoints were disabled.

I installed **Windows Server 2022 Standard Evaluation (Desktop Experience)** the same way as on `TAILWIND-DC1`, set the built-in Administrator password, and signed in.

I then configured static networking on `TAILWIND-MBR1`:

```text
IP address:        10.10.10.20
Subnet mask:        255.255.255.0
Default gateway:    10.10.10.1
Preferred DNS:      10.10.10.10
Alternate DNS:      8.8.8.8
```

**Why this matters:** This is the first meaningful configuration difference from `TAILWIND-DC1` — the **Preferred DNS server is now `10.10.10.10`**, which is `TAILWIND-DC1`'s own address. A server can't locate or join a domain without being able to resolve that domain's DNS records, and Active Directory DNS is hosted on the domain controllers themselves. Pointing `TAILWIND-MBR1` at `TAILWIND-DC1` for DNS (rather than at an external resolver) is what makes domain discovery and join possible in the next step.

I renamed the computer to `TAILWIND-MBR1` via **Server Manager → Local Server → Computer Name**, restarting to apply the name change. I then returned to **Local Server → Computer Name**, selected **Change** again, and this time selected **Domain** under "Member of," entering `TAILWINDTRADERS` and authenticating with `TAILWINDTRADERS\Administrator`.

**What this does:** This is the domain-join operation itself — `TAILWIND-MBR1` contacts the domain (via the DNS pointing at `TAILWIND-DC1`), authenticates using domain credentials, and becomes a **member server**: a Windows Server that belongs to the domain and can authenticate domain accounts, but is not itself a domain controller.

**Why this matters:** This distinction — member server versus domain controller — is exactly what sets up the very first task of Phase 1 later in this workflow. `TAILWIND-MBR1` joins the domain here as a regular member server; it isn't promoted to a *second domain controller* until Phase 1.1. Understanding that these are two separate, sequential milestones (join the domain, and only later promote to a DC) reflects a realistic administrative pattern — servers are very often joined to a domain first and promoted to additional domain controller roles later, rather than always being provisioned as domain controllers from the moment they're built.

After confirming domain membership (the "Welcome to the tailwindtraders domain" dialog), I restarted the VM to complete the join.

##### 🔷 Phase 0.4 — Phase 0 findings

| Task | Outcome |
|---|---|
| Hyper-V installed | Enabled on the Windows 11 host |
| VM/VHD storage paths | Redirected to `C:\VirtualMachines` |
| NAT switch created | `NATSwitch` (internal), host gateway `10.10.10.1`, NAT for `10.10.10.0/24` |
| `TAILWIND-DC1` built | Windows Server 2022 Standard Evaluation (Desktop Experience), static IP `10.10.10.10` |
| Forest/domain created | `tailwindtraders.internal`, with `TAILWIND-DC1` as the first domain controller |
| `TAILWIND-MBR1` built | Windows Server 2022 Standard Evaluation (Desktop Experience), static IP `10.10.10.20`, DNS pointed at `TAILWIND-DC1` |
| `TAILWIND-MBR1` domain-joined | Joined `TAILWINDTRADERS` as a member server (not yet a domain controller) |

</details>

<details>
<summary><strong>▶ Phase 1 — Configure Domain Controller Operations</strong><br>
→ promoting a domain controller, transferring a FSMO role, and configuring AD site topology
</summary><br>

This phase focused on promoting `TAILWIND-MBR1` to a domain controller, transferring the RID FSMO role to it, and creating an Active Directory site with an associated subnet.

##### 🔷 Phase 1.1 — Install AD DS and promote TAILWIND-MBR1 to a domain controller

Signed in to `TAILWIND-MBR1` as `TAILWINDTRADERS\Administrator`, I opened Server Manager and used **Manage → Add Roles and Features** to install the Active Directory Domain Services role, accepting the default feature dependencies.

**What this does:** This installs the AD DS binaries and management tools onto `TAILWIND-MBR1` — the same software that already runs on `TAILWIND-DC1`. At this point, `TAILWIND-MBR1` has the *capability* to become a domain controller, but hasn't actually become one yet.

**Why this matters:** This is a two-step process by design (install the role, then separately promote), and it's easy to assume the server is "done" the moment the role finishes installing. It isn't — a domain controller isn't a domain controller until the promotion wizard actually runs and replicates the directory database to it, which is the next step.

<p align="left">
  <img src="images/active-directory-domain-services-administration-01.png"
       alt="Adding the AD DS role via Server Manager"
       style="border: 2px solid #444; border-radius: 6px;"
       width="900"><br>
  <em>Figure 1: Adding the Active Directory Domain Services role via the Add Roles and Features wizard.</em>
</p>

Once installation completed, I selected the notification flag icon in Server Manager and chose **Promote this server to a domain controller**, launching the AD DS Configuration Wizard.

**What this does:** This launches the same Configuration Wizard used back in Phase 0.2 to create the forest — but this time it will run in a different mode, since a domain (`tailwindtraders.internal`) already exists.

<p align="left">
  <img src="images/active-directory-domain-services-administration-02.png"
       alt="Server Manager notification flag with promotion option"
       style="border: 2px solid #444; border-radius: 6px;"
       width="900"><br>
  <em>Figure 2: Selecting "Promote this server to a domain controller" from the Server Manager notification menu.</em>
</p>

On the Deployment Configuration page, I selected **Add a domain controller to an existing domain**, confirmed the domain name as `tailwindtraders.internal`, and re-authenticated as `Administrator`. I accepted the default Domain Controller options (configuring the DSRM password), accepted the default DNS, Additional Options, and Paths settings, and completed the Prerequisites Check before selecting **Install**.

**What this does:** Unlike Phase 0.2, where "Add a new forest" created `tailwindtraders.internal` from nothing, this run selects **"Add a domain controller to an existing domain"** — `TAILWIND-MBR1` isn't creating a new domain, it's joining as an additional domain controller for the one that already exists. Behind the scenes, this installs the NTDS (directory) database on `TAILWIND-MBR1` and begins replicating the existing directory data from `TAILWIND-DC1` to it.

**Why this matters:** This is the moment `TAILWIND-MBR1` goes from "a domain-joined member server" (established back in Phase 0.3) to "a second domain controller." Once complete, both `TAILWIND-DC1` and `TAILWIND-MBR1` hold a full, writable copy of the directory database and can independently authenticate users and process directory queries — this is what redundancy actually looks like at the directory level, not just "two servers are both turned on."

<p align="left">
  <img src="images/active-directory-domain-services-administration-03.png"
       alt="AD DS Configuration Wizard Deployment Configuration page"
       style="border: 2px solid #444; border-radius: 6px;"
       width="900"><br>
  <em>Figure 3: AD DS Configuration Wizard — adding TAILWIND-MBR1 as a domain controller to the existing tailwindtraders.internal domain.</em>
</p>

The server restarted automatically to complete promotion. After restart, I signed back in as `tailwindtraders\administrator`.

<p align="left">
  <img src="images/active-directory-domain-services-administration-04.png"
       alt="TAILWIND-MBR1 successfully promoted to a domain controller"
       style="border: 2px solid #444; border-radius: 6px;"
       width="900"><br>
  <em>Figure 4: TAILWIND-MBR1 successfully promoted to a domain controller for tailwindtraders.internal.</em>
</p>

##### 🔷 Phase 1.2 — Transfer the RID FSMO role

With `TAILWIND-MBR1` now a domain controller, I opened **Active Directory Users and Computers** from the Tools menu, right-clicked the root node, selected **All Tasks → Operations Masters**, and on the **RID** tab selected **Change** to transfer the RID Master role from `TAILWIND-DC1` to `TAILWIND-MBR1`.

**What this does:** The RID (Relative ID) Master is one of five FSMO (Flexible Single Master Operations) roles in Active Directory. It's responsible for handing out blocks of unique relative identifiers that get combined with the domain SID to form each new object's SID (used any time a new user, group, or computer object is created anywhere in the domain). Unlike most directory operations (which are multi-master and can happen on any DC), RID allocation can only be performed by whichever DC currently holds this specific role. Transferring it moves that responsibility from `TAILWIND-DC1` to `TAILWIND-MBR1`.

**Why this matters:** If the current RID Master becomes permanently unavailable and the role is never transferred (or seized) to another DC, the domain will eventually run out of previously-issued RID blocks on other domain controllers and be unable to create new security principals. Being comfortable transferring FSMO roles — rather than leaving them wherever they happened to land by default (typically the very first DC in the domain) — is a routine but important piece of domain controller lifecycle management, especially before decommissioning or performing maintenance on whichever DC currently holds a given role.

<p align="left">
  <img src="images/active-directory-domain-services-administration-05.png"
       alt="Operations Masters dialog box, RID tab"
       style="border: 2px solid #444; border-radius: 6px;"
       width="900"><br>
  <em>Figure 5: Operations Masters dialog box, RID tab, prior to transferring the role.</em>
</p>

<p align="left">
  <img src="images/active-directory-domain-services-administration-06.png"
       alt="Confirming the RID Master role transfer to TAILWIND-MBR1"
       style="border: 2px solid #444; border-radius: 6px;"
       width="900"><br>
  <em>Figure 6: Confirming the RID Master role transfer to TAILWIND-MBR1.</em>
</p>

##### 🔷 Phase 1.3 — Create an Active Directory site and configure a subnet

Signed in to `TAILWIND-DC1`, I opened **Active Directory Sites and Services**, right-clicked **Sites**, selected **New Site**, named it `Sydney`, and selected `DEFAULTIPSITELINK` as the site link.

**What this does:** An AD **site** is a logical representation of a physical network location — typically an office, data center, or region with fast, reliable connectivity within itself. `DEFAULTIPSITELINK` connects the new `Sydney` site to the default site (`Default-First-Site-Name`, where both DCs currently live) so replication can actually flow between them.

<p align="left">
  <img src="images/active-directory-domain-services-administration-07.png"
       alt="Creating the Sydney AD site"
       style="border: 2px solid #444; border-radius: 6px;"
       width="900"><br>
  <em>Figure 7: Creating the Sydney site in Active Directory Sites and Services.</em>
</p>

I then expanded **Sites**, right-clicked **Subnets**, selected **New Subnet**, entered the prefix `172.16.1.0/24`, and associated it with the `Sydney` site.

**What this does:** Associating a subnet with a site is what actually makes the site useful — Active Directory uses subnet-to-site mapping to determine which site a given client or domain controller "belongs to" based on its IP address. Without this mapping, the `Sydney` site would exist as an empty label with no real effect.

**Why this matters:** Site and subnet configuration directly affects two things in a real environment: (1) **authentication locality** — a client prefers authenticating against a domain controller in its own site rather than one across a slower WAN link, so a correctly mapped site keeps login traffic local instead of unnecessarily crossing to a distant DC; and (2) **replication scheduling** — replication *within* a site happens frequently and aggressively, while replication *between* sites can be scheduled (e.g., only during off-hours) to avoid saturating limited WAN bandwidth. Neither of these behaviors would be possible without first defining the site and subnet, even though this task's real-world office (`172.16.1.0/24`) isn't physically present in this lab.

<p align="left">
  <img src="images/active-directory-domain-services-administration-08.png"
       alt="Associating the 172.16.1.0/24 subnet with the Sydney site"
       style="border: 2px solid #444; border-radius: 6px;"
       width="900"><br>
  <em>Figure 8: Associating the 172.16.1.0/24 subnet with the Sydney site.</em>
</p>

##### 🔷 Phase 1.4 — Phase 1 findings

| Task | Outcome |
|---|---|
| Domain controller promoted | `TAILWIND-MBR1` added to `tailwindtraders.internal` |
| FSMO role transferred | RID Master moved from `TAILWIND-DC1` to `TAILWIND-MBR1` |
| AD site created | `Sydney` |
| Subnet associated | `172.16.1.0/24` → `Sydney` |

</details>

<details>
<summary><strong>▶ Phase 2 — Configure User Management Operations</strong><br>
→ organizational units, user accounts, groups, Protected Users, delegation, attributes, and account lifecycle actions
</summary><br>

This phase focused on structuring the directory with organizational units, provisioning and managing user accounts, creating a security group, hardening a user via Protected Users, delegating administrative control, configuring a custom attribute, and performing routine account lifecycle actions.

##### 🔷 Phase 2.1 — Create organizational units

In **Active Directory Users and Computers** on `TAILWIND-DC1`, I right-clicked the `tailwindtraders.internal` domain, selected **New → Organizational Unit**, and created three OUs: `Sydney`, `Melbourne`, and `Brisbane`.

**What this does:** An Organizational Unit (OU) is a container object inside Active Directory used purely for organizing and administering other objects (users, groups, computers) — it has no security identity of its own the way a group does. Creating `Sydney`, `Melbourne`, and `Brisbane` establishes three separate containers that later steps will use to place location-specific accounts.

**Why this matters:** OUs are the boundary that both **Group Policy application** and **administrative delegation** are scoped to. A GPO or a delegated permission applied at the OU level only affects objects inside that OU (and any OUs nested beneath it) — it doesn't spill over into sibling OUs. This is what makes it possible, later in this workflow, to delegate password-reset rights over *just* the Sydney OU (Phase 2.6) and to apply auditing/security settings to *just* the Sydney OU (Phase 4) without those settings touching Melbourne or Brisbane at all. Without this OU structure in place first, that kind of narrow scoping wouldn't be possible.

<p align="left">
  <img src="images/active-directory-domain-services-administration-09.png"
       alt="Creating the Sydney, Melbourne, and Brisbane organizational units"
       style="border: 2px solid #444; border-radius: 6px;"
       width="900"><br>
  <em>Figure 9: Sydney, Melbourne, and Brisbane organizational units created under tailwindtraders.internal.</em>
</p>

##### 🔷 Phase 2.2 — Create and configure the SydneyContractor user account

Within the Sydney OU, I selected **New → User**, entered `SydneyContractor` as the full name and logon name, and set a password. I then opened the account's properties and, on the **Account** tab, configured the account to expire on `Jan 1, 2030`.

**What this does:** Creating the user places a new security principal (with its own SID, drawn from the RID pool discussed in Phase 1.2) inside the Sydney OU. Setting an account expiration date adds a hard stop after which the account can no longer authenticate at all, regardless of whether the password is still valid.

**Why this matters:** The `Contractor` naming and the account expiration date aren't incidental — they reflect a real identity management pattern. Contractor and temporary accounts are a common source of stale, forgotten access in real environments (an account that's never disabled because no one remembered to do it after the engagement ended). Setting an expiration date at creation time builds the "offboarding" into the account from day one, rather than relying on someone remembering to manually disable it later.

<p align="left">
  <img src="images/active-directory-domain-services-administration-10.png"
       alt="Creating the SydneyContractor user account"
       style="border: 2px solid #444; border-radius: 6px;"
       width="900"><br>
  <em>Figure 10: Creating the SydneyContractor user account in the Sydney OU.</em>
</p>

<p align="left">
  <img src="images/active-directory-domain-services-administration-11.png"
       alt="Configuring account expiration for SydneyContractor"
       style="border: 2px solid #444; border-radius: 6px;"
       width="900"><br>
  <em>Figure 11: Configuring the account expiration date (Jan 1, 2030) for SydneyContractor.</em>
</p>

##### 🔷 Phase 2.3 — Copy the account to create MelbourneContractor and BrisbaneContractor

Rather than creating each account from scratch, I right-clicked `SydneyContractor` and selected **Copy**, which carried over the account expiration setting. I created `MelbourneContractor` and `BrisbaneContractor` this way, then dragged each into its respective OU.

**What this does:** ADUC's **Copy** function creates a new user object that inherits most of the source account's properties — including, in this case, the Jan 1, 2030 expiration date — but deliberately does **not** carry over the password (a new one must be set for each copy) or existing group memberships, since those are usually account-specific rather than template-worthy.

**Why this matters:** Knowing exactly what does and doesn't get copied is important — it's easy to assume a copied account is a complete duplicate and forget that group memberships need to be re-added manually. This distinction becomes directly relevant in the next two steps: `SydneyContractor`'s group memberships (Sydney Administrators, Protected Users) had to be configured *after* the copy, specifically because copying doesn't bring those along automatically.

<p align="left">
  <img src="images/active-directory-domain-services-administration-12.png"
       alt="Copying SydneyContractor to create MelbourneContractor and BrisbaneContractor"
       style="border: 2px solid #444; border-radius: 6px;"
       width="900"><br>
  <em>Figure 12: Copying SydneyContractor to provision MelbourneContractor and BrisbaneContractor.</em>
</p>

<p align="left">
  <img src="images/active-directory-domain-services-administration-13.png"
       alt="Relocating MelbourneContractor and BrisbaneContractor into their OUs"
       style="border: 2px solid #444; border-radius: 6px;"
       width="900"><br>
  <em>Figure 13: Relocating MelbourneContractor and BrisbaneContractor into the Melbourne and Brisbane OUs.</em>
</p>

##### 🔷 Phase 2.4 — Create the Sydney Administrators group and add a member

Within the Sydney OU, I selected **New → Group**, named it `Sydney Administrators`, and set the group scope to **Universal**. I then opened `SydneyContractor`'s properties, went to the **Member Of** tab, and added the account to the `Sydney Administrators` group.

**What this does:** This creates a new security group — unlike an OU, a group *is* a security principal with its own SID, meaning permissions and rights can be granted directly to it (which is exactly what happens later in Phases 2.6 and 4.3). **Universal** scope means the group can contain members from any domain in the forest and can be granted permissions anywhere in the forest — the broadest of the three group scopes (Domain Local, Global, Universal). In a single-domain lab like this one, the practical difference versus Global scope is minimal, but Universal is the correct choice for a group that might need to apply across multiple domains in a larger, multi-domain forest.

**Why this matters:** Creating this group (rather than, say, adding `SydneyContractor` directly to Domain Admins for convenience) is what makes scoped delegation possible at all. Permissions and rights get granted to `Sydney Administrators` as a group, not to individual users — meaning additional Sydney-based staff could be added to this one group later and automatically inherit the same delegated password-reset rights and security settings, without needing those permissions re-configured from scratch for each new person.

<p align="left">
  <img src="images/active-directory-domain-services-administration-14.png"
       alt="Creating the Sydney Administrators security group"
       style="border: 2px solid #444; border-radius: 6px;"
       width="900"><br>
  <em>Figure 14: Creating the Sydney Administrators group with Universal scope.</em>
</p>

<p align="left">
  <img src="images/active-directory-domain-services-administration-15.png"
       alt="Adding SydneyContractor to the Sydney Administrators group"
       style="border: 2px solid #444; border-radius: 6px;"
       width="900"><br>
  <em>Figure 15: Adding SydneyContractor to the Sydney Administrators group via the Member Of tab.</em>
</p>

##### 🔷 Phase 2.5 — Configure SydneyContractor as a Protected User

Using the same **Member Of** tab, I added `SydneyContractor` to the built-in **Protected Users** group.

**What this does:** Protected Users is a special, built-in security group (introduced in Windows Server 2012 R2) that doesn't grant any additional *permissions* — instead, it changes **how the account is allowed to authenticate**. Membership enforces several restrictions simultaneously: the account can no longer authenticate using NTLM at all, can't use weaker Kerberos encryption types (like DES or RC4), can't have its credentials cached for offline logon, and has its Kerberos ticket-granting-ticket (TGT) lifetime capped at 4 hours (forcing more frequent re-authentication instead of a long-lived ticket).

**Why this matters:** This is a good example of a security control whose effect is completely invisible just by looking at the object in ADUC — group membership alone doesn't hint at what Protected Users actually does. Its real purpose is reducing how *useful* a stolen credential or cached ticket for this account would be to an attacker, which is a fundamentally different kind of protection than granting or restricting access to specific resources.

<p align="left">
  <img src="images/active-directory-domain-services-administration-16.png"
       alt="Adding SydneyContractor to the Protected Users group"
       style="border: 2px solid #444; border-radius: 6px;"
       width="900"><br>
  <em>Figure 16: Adding SydneyContractor to the built-in Protected Users group.</em>
</p>

##### 🔷 Phase 2.6 — Delegate control over the Sydney OU

I right-clicked the Sydney OU and selected **Delegate Control**, launching the Delegation of Control Wizard. I added the `Sydney Administrators` group and, on the Tasks to Delegate page, selected **Reset user passwords and force password change at next logon**.

**What this does:** This grants `Sydney Administrators` a specific, pre-defined administrative capability — resetting passwords and forcing a password change at next logon — but only for objects **inside the Sydney OU**, not anywhere else in the domain. Behind the scenes, the wizard is writing specific Access Control Entries (ACEs) onto the Sydney OU's security descriptor rather than modifying any group's own membership or rights elsewhere.

**Why this matters:** This is the single clearest demonstration of least-privilege thinking in the whole workflow. It would have been technically simpler to just add `Sydney Administrators` to Domain Admins — that group would then be able to reset passwords (and do essentially anything else) anywhere in the domain. Delegating this one specific task, scoped to this one specific OU, means that if `Sydney Administrators`' credentials were ever compromised, the blast radius is "password reset capability over Sydney accounts" — not "full administrative control over the entire domain." This is exactly why the OU structure from Phase 2.1 had to exist first: delegation needs a container to scope itself to.

<p align="left">
  <img src="images/active-directory-domain-services-administration-17.png"
       alt="Delegation of Control Wizard — selecting Sydney Administrators"
       style="border: 2px solid #444; border-radius: 6px;"
       width="900"><br>
  <em>Figure 17: Delegation of Control Wizard — granting Sydney Administrators the "reset user passwords" delegated task.</em>
</p>

##### 🔷 Phase 2.7 — Configure and validate the City attribute

In the **Address** tab of `SydneyContractor`'s properties, I set the **City** field to `Sydney`. I then right-clicked `tailwindtraders.internal` and selected **Find**, and on the **Advanced** tab configured a query for Field = User, City, Condition = Is (exactly), Value = `Sydney`, confirming `SydneyContractor` appeared in the search results.

**What this does:** The City field is a standard Active Directory user attribute (part of the schema every user object already has, whether populated or not). Setting it to `Sydney` and then querying for it via Advanced Find demonstrates that AD attributes aren't just descriptive metadata sitting unused — they're directly searchable and can be used to build dynamic groups, reports, or targeted queries based on real organizational data (location, department, title, etc.).

**Why this matters:** In a real environment with hundreds or thousands of accounts, being able to search "find every user whose City is Sydney" (rather than manually knowing every account name) is exactly how administrators locate the right set of objects to act on — for bulk operations, reporting, or troubleshooting. This task is a small-scale demonstration of a pattern that becomes essential at real organizational scale.

<p align="left">
  <img src="images/active-directory-domain-services-administration-18.png"
       alt="Setting the City attribute for SydneyContractor"
       style="border: 2px solid #444; border-radius: 6px;"
       width="900"><br>
  <em>Figure 18: Setting the City attribute to "Sydney" on the SydneyContractor account.</em>
</p>

<p align="left">
  <img src="images/active-directory-domain-services-administration-19.png"
       alt="Advanced Find query validating the City attribute"
       style="border: 2px solid #444; border-radius: 6px;"
       width="900"><br>
  <em>Figure 19: Advanced Find query confirming SydneyContractor matches City = Sydney.</em>
</p>

##### 🔷 Phase 2.8 — Disable MelbourneContractor and reset BrisbaneContractor's password

In the Melbourne OU, I right-clicked `MelbourneContractor` and selected **Disable Account**. In the Brisbane OU, I right-clicked `BrisbaneContractor`, selected **Reset Password**, and set a new password.

**What this does:** Disabling an account immediately blocks it from authenticating, without deleting the object or losing any of its configuration, group memberships, or history — it's fully reversible by simply re-enabling it later. Resetting a password immediately invalidates the old password and requires the new one going forward (and, depending on how it's configured, can force a change at next logon).

**Why this matters:** These two actions represent the two most common day-to-day identity lifecycle tasks an administrator performs — far more frequently than anything involving OUs, FSMO roles, or Group Policy. Disabling (rather than deleting) is the standard first step whenever an account needs to be immediately deactivated but might still need investigation or reactivation later — for example, an employee departure, a suspected compromised account, or (as in this case) simply demonstrating the lifecycle action itself. Password reset is the routine response to a forgotten password or a suspected credential exposure.

<p align="left">
  <img src="images/active-directory-domain-services-administration-20.png"
       alt="Disabling the MelbourneContractor account"
       style="border: 2px solid #444; border-radius: 6px;"
       width="900"><br>
  <em>Figure 20: Disabling the MelbourneContractor user account.</em>
</p>

<p align="left">
  <img src="images/active-directory-domain-services-administration-21.png"
       alt="Resetting the BrisbaneContractor account password"
       style="border: 2px solid #444; border-radius: 6px;"
       width="900"><br>
  <em>Figure 21: Resetting the password for the BrisbaneContractor account.</em>
</p>

##### 🔷 Phase 2.9 — Phase 2 findings

| Task | Outcome |
|---|---|
| OUs created | `Sydney`, `Melbourne`, `Brisbane` |
| User accounts created | `SydneyContractor`, `MelbourneContractor`, `BrisbaneContractor` |
| Account expiration configured | `SydneyContractor` → expires Jan 1, 2030 |
| Security group created | `Sydney Administrators` (Universal scope) |
| Protected Users membership | `SydneyContractor` added |
| Delegation configured | Sydney Administrators → password reset/force-change over Sydney OU |
| Attribute configured and validated | City = `Sydney` on `SydneyContractor`, confirmed via Advanced Find |
| Account disabled | `MelbourneContractor` |
| Password reset | `BrisbaneContractor` |

</details>

<details>
<summary><strong>▶ Phase 3 — Manage Password Policies</strong><br>
→ domain password policy, Fine-Grained Password Policy, and the AD Recycle Bin
</summary><br>

This phase focused on configuring the domain-wide password policy, layering a stricter Fine-Grained Password Policy onto the Domain Admins group, and enabling the Active Directory Recycle Bin.

##### 🔷 Phase 3.1 — Configure the domain password policy

From the Tools menu, I opened the **Group Policy Management** console, expanded the `tailwindtraders.internal` domain, right-clicked **Default Domain Policy**, and selected **Edit**. I navigated to `Computer Configuration\Policies\Windows Settings\Security Settings\Account Policies\Password Policy` and changed **Minimum password length** to `14`.

**What this does:** The Default Domain Policy is the GPO linked at the domain root by default, meaning its settings apply to every computer (and, for password policy specifically, every domain user account) in the domain unless overridden by a Fine-Grained Password Policy. Raising the minimum length to 14 characters sets a new domain-wide baseline that every account's password must meet going forward.

**Why this matters:** Password policy is one of the few security settings in Active Directory that can *only* be set once per domain via Group Policy (domain-wide) — you can't have two different "domain password policies" active at the same GPO level simultaneously. This is exactly why Microsoft introduced Fine-Grained Password Policies (used in the next step) as a separate mechanism: the domain policy sets the floor for everyone, and FGPPs layer stricter requirements on top for specific higher-risk groups.

<p align="left">
  <img src="images/active-directory-domain-services-administration-22.png"
       alt="Editing the Default Domain Policy password settings"
       style="border: 2px solid #444; border-radius: 6px;"
       width="900"><br>
  <em>Figure 22: Navigating to Password Policy settings within the Default Domain Policy.</em>
</p>

<p align="left">
  <img src="images/active-directory-domain-services-administration-23.png"
       alt="Setting minimum password length to 14 characters"
       style="border: 2px solid #444; border-radius: 6px;"
       width="900"><br>
  <em>Figure 23: Setting the domain-wide minimum password length to 14 characters.</em>
</p>

##### 🔷 Phase 3.2 — Create and apply a Fine-Grained Password Policy

From the Tools menu, I opened **Active Directory Administrative Center**, navigated to the `System` container, then the `Password Settings Container`, and selected **New → Password Settings**. I named the policy `Domain Admin Password Policy`, set the precedence to `1`, set the minimum password length to `16`, and applied it directly to the **Domain Admins** group.

**What this does:** This creates a Password Settings Object (PSO) — a distinct AD object type, not a GPO — that defines its own password rules (in this case, a 16-character minimum, stricter than the domain's 14-character baseline) and applies them only to the specific group or users it's linked to (Domain Admins). When a PSO applies to an account, its settings **override** the domain password policy entirely for that account, rather than combining with it.

**Why this matters:** The **precedence** value determines which PSO wins if more than one could apply to the same account (a scenario that doesn't happen in this lab with only one PSO, but matters in larger environments with multiple FGPPs). Precedence works in reverse of what might be intuitive — the **lowest** number wins, so setting this policy's precedence to `1` marks it as the highest-priority policy available, not the lowest. Beyond the mechanics, the actual decision to apply a stricter policy specifically to Domain Admins reflects a core risk-based principle: a compromised standard user account is a problem, but a compromised Domain Admins account can mean total domain compromise, so it's reasonable to hold that smaller, higher-risk group to a meaningfully higher password standard than the rest of the domain.

<p align="left">
  <img src="images/active-directory-domain-services-administration-24.png"
       alt="Creating the Domain Admin Password Policy fine-grained password policy"
       style="border: 2px solid #444; border-radius: 6px;"
       width="900"><br>
  <em>Figure 24: Creating the Domain Admin Password Policy Password Settings Object with precedence 1 and minimum length 16.</em>
</p>

<p align="left">
  <img src="images/active-directory-domain-services-administration-25.png"
       alt="Applying the fine-grained password policy to the Domain Admins group"
       style="border: 2px solid #444; border-radius: 6px;"
       width="900"><br>
  <em>Figure 25: Applying the Domain Admin Password Policy directly to the Domain Admins group.</em>
</p>

##### 🔷 Phase 3.3 — Enable the Active Directory Recycle Bin

Still in Active Directory Administrative Center, I selected `Tailwindtraders (local)` and, in the right-hand Tasks pane, selected **Enable Recycle Bin**, acknowledging the warnings regarding replication and irreversibility.

**What this does:** By default, deleting an AD object doesn't fully remove it immediately — it's converted to a "tombstone" with most of its attributes stripped, and after a retention period, it's permanently garbage-collected. Enabling the Recycle Bin changes this behavior forest-wide: deleted objects retain nearly all of their original attributes (group memberships, attributes, etc.) for a much longer window, and can be restored in a single action rather than requiring an authoritative restore from backup.

**Why this matters:** This setting can't be casually toggled — enabling it is a one-way, irreversible, forest-wide change, which is exactly why it isn't turned on by default. That irreversibility is also the reason it's worth enabling proactively (as done here) rather than waiting until after an accidental deletion has already happened, at which point it's too late for that specific object to benefit from it. A single accidentally deleted OU (imagine the Sydney OU, along with every user and group inside it) could otherwise mean hours of manual recreation work instead of a quick restore.

<p align="left">
  <img src="images/active-directory-domain-services-administration-26.png"
       alt="Enabling the Active Directory Recycle Bin"
       style="border: 2px solid #444; border-radius: 6px;"
       width="900"><br>
  <em>Figure 26: Enabling the Active Directory Recycle Bin from Active Directory Administrative Center.</em>
</p>

##### 🔷 Phase 3.4 — Phase 3 findings

| Task | Outcome |
|---|---|
| Domain password policy updated | Minimum password length → `14` characters |
| Fine-Grained Password Policy created | `Domain Admin Password Policy` — precedence `1`, minimum length `16` |
| FGPP applied to | `Domain Admins` group |
| AD Recycle Bin | Enabled |

</details>

<details>
<summary><strong>▶ Phase 4 — Configure Security Settings</strong><br>
→ NTLM restriction, advanced audit policy, and user rights assignment
</summary><br>

This phase focused on restricting NTLM authentication domain-wide, configuring advanced audit policy for account management activity on the Sydney OU, and configuring a "Deny log on as a service" user right for the Sydney Administrators group.

##### 🔷 Phase 4.1 — Restrict NTLM authentication

From the Group Policy Management console, I right-clicked the **Default Domain Controller Policy** and selected **Edit**. I navigated to `Computer Configuration\Policies\Windows Settings\Security Settings\Local Policies\Security Options`, opened **Network security: Restrict NTLM: NTLM authentication in this domain**, enabled **Define this policy setting**, and set the value to **Deny all**.

**What this does:** NTLM is an older, challenge-response authentication protocol that Active Directory still supports for backward compatibility, even though Kerberos is the default and preferred protocol for domain authentication. This setting instructs domain controllers to reject NTLM authentication attempts entirely, forcing all authentication in the domain to rely on Kerberos instead.

**Why this matters:** NTLM has well-known weaknesses that Kerberos doesn't share — it doesn't mutually authenticate client and server the same way, which makes it a common target for **relay attacks** (an attacker intercepts an NTLM authentication attempt and forwards/relays it to a different target to authenticate as the victim) and **downgrade attacks** (an attacker forces a fallback to NTLM specifically because it's easier to abuse than Kerberos). Denying NTLM domain-wide closes off an entire category of attack technique that depends entirely on NTLM being available as a fallback option — note that this is configured on the **Default Domain Controller Policy** (not the Default Domain Policy used in Phase 3.1) since NTLM authentication is a domain-controller-enforced setting.

<p align="left">
  <img src="images/active-directory-domain-services-administration-27.png"
       alt="Locating the NTLM restriction policy setting"
       style="border: 2px solid #444; border-radius: 6px;"
       width="900"><br>
  <em>Figure 27: Locating "Network security: Restrict NTLM: NTLM authentication in this domain" within Security Options.</em>
</p>

<p align="left">
  <img src="images/active-directory-domain-services-administration-28.png"
       alt="Setting NTLM authentication to Deny all"
       style="border: 2px solid #444; border-radius: 6px;"
       width="900"><br>
  <em>Figure 28: Setting NTLM authentication in the domain to "Deny all."</em>
</p>

##### 🔷 Phase 4.2 — Create SydneyOUPolicy and enable advanced auditing for account management

From the Group Policy Management console, I right-clicked the Sydney OU and selected **Create a GPO in this domain, and link it here...**, naming the new GPO `SydneyOUPolicy`. I edited the GPO and navigated to `Computer Configuration\Policies\Windows Settings\Security Settings\Advanced Audit Policy Configuration\Audit Policies\Account Management`, then enabled **Audit User Account Management** for both **Success** and **Failure**.

**What this does:** This creates a brand-new GPO (distinct from the Default Domain Policy or Default Domain Controller Policy edited in earlier phases) and links it specifically to the Sydney OU rather than the domain root — meaning its settings only apply to objects inside that OU. Enabling **Audit User Account Management** (Success and Failure) instructs domain controllers to write Security event log entries any time an account is created, modified, deleted, or has group membership changed for objects within Sydney's scope.

**Why this matters:** This connects directly to the OU structure and delegation established in Phase 2 — the same Sydney OU that Sydney Administrators was delegated password-reset control over (Phase 2.6) is now also the OU with account management activity actively logged. That pairing matters operationally: since Sydney Administrators has real administrative capability over this OU, having detailed Success/Failure auditing in place means any account changes — whether legitimate delegated actions or unauthorized/unexpected ones — get captured with enough detail to investigate later. Using **Advanced Audit Policy Configuration** here (rather than the older, coarser "basic auditing" categories) is also a deliberate choice — advanced audit policy provides much more granular event subcategories, avoiding the common problem of basic auditing either generating too much noise or missing the specific events that actually matter.

<p align="left">
  <img src="images/active-directory-domain-services-administration-29.png"
       alt="Creating and linking SydneyOUPolicy to the Sydney OU"
       style="border: 2px solid #444; border-radius: 6px;"
       width="900"><br>
  <em>Figure 29: Creating and linking the SydneyOUPolicy GPO to the Sydney OU.</em>
</p>

<p align="left">
  <img src="images/active-directory-domain-services-administration-30.png"
       alt="Enabling Success and Failure auditing for User Account Management"
       style="border: 2px solid #444; border-radius: 6px;"
       width="900"><br>
  <em>Figure 30: Enabling Success and Failure auditing for "Audit User account management" within SydneyOUPolicy.</em>
</p>

##### 🔷 Phase 4.3 — Configure Deny Log On As a Service

Still within `SydneyOUPolicy`, I navigated to `Computer Configuration\Policies\Windows Settings\Security Settings\Local Policies\User Rights Assignment`, opened **Deny log on as a service**, enabled **Define this policy setting**, and added the `Sydney Administrators` group via **Add User or Group → Browse → Advanced → Find Now**.

**What this does:** User Rights Assignments control what a security principal is allowed (or explicitly *not* allowed) to do on a system, distinct from object-level permissions. "Log on as a service" is the right that allows an account to be used to run a Windows service; explicitly *denying* that right to `Sydney Administrators` means accounts in that group can never be used to start a service, even if some other misconfiguration elsewhere might have otherwise permitted it. Deny rules in Windows always take precedence over Allow rules for the same right.

**Why this matters:** This is a least-privilege / attack-surface-reduction decision rather than a "what does this group need" decision. `Sydney Administrators` exists for interactive administrative tasks (like the delegated password-reset capability from Phase 2.6) — it has no legitimate reason to ever run a Windows service. Explicitly denying that logon type closes off a potential path of abuse: if this group's credentials were ever compromised, an attacker couldn't use them to install and run a malicious service under that account's context, which is a common technique for establishing persistence. This same `SydneyOUPolicy` GPO now carries both a *detective* control (the account management auditing from Phase 4.2) and a *preventive* control (this user rights restriction) — two different but complementary categories of security control, both scoped to the same OU.

<p align="left">
  <img src="images/active-directory-domain-services-administration-31.png"
       alt="Locating the Deny log on as a service policy setting"
       style="border: 2px solid #444; border-radius: 6px;"
       width="900"><br>
  <em>Figure 31: Locating "Deny log on as a service" within User Rights Assignment.</em>
</p>

<p align="left">
  <img src="images/active-directory-domain-services-administration-32.png"
       alt="Adding Sydney Administrators to Deny log on as a service"
       style="border: 2px solid #444; border-radius: 6px;"
       width="900"><br>
  <em>Figure 32: Adding the Sydney Administrators group to "Deny log on as a service" within SydneyOUPolicy.</em>
</p>

##### 🔷 Phase 4.4 — Phase 4 findings

| Task | Outcome |
|---|---|
| NTLM authentication | Denied domain-wide via Default Domain Controller Policy |
| New GPO created and linked | `SydneyOUPolicy` → Sydney OU |
| Advanced audit policy | Audit User Account Management (Success and Failure) enabled |
| User rights assignment | Deny log on as a service → `Sydney Administrators` |

</details>

---

### Evidence Examination Summary

| Task | Console/Tool | Object(s) Affected | Outcome |
|---|---|---|---|
| Install Hyper-V and configure NAT switch | Windows Settings, PowerShell | Host machine, `NATSwitch` | Hyper-V enabled; internal NAT network `10.10.10.0/24` created |
| Build and promote TAILWIND-DC1 | Hyper-V Manager, Windows Server Setup, Server Manager, AD DS Config Wizard | `TAILWIND-DC1` | VM built, static IP `10.10.10.10` configured, promoted as first DC of new forest `tailwindtraders.internal` |
| Build and domain-join TAILWIND-MBR1 | Hyper-V Manager, Windows Server Setup, Server Manager | `TAILWIND-MBR1` | VM built, static IP `10.10.10.20` configured (DNS → `10.10.10.10`), joined to `TAILWINDTRADERS` as a member server |
| Promote domain controller | Server Manager / AD DS Config Wizard | `TAILWIND-MBR1` | Promoted to a domain controller in `tailwindtraders.internal` |
| Transfer FSMO role | ADUC | RID Master | Transferred from `TAILWIND-DC1` to `TAILWIND-MBR1` |
| Create AD site and subnet | AD Sites and Services | `Sydney` site, `172.16.1.0/24` subnet | Site and subnet created and associated |
| Create OUs | ADUC | `Sydney`, `Melbourne`, `Brisbane` | Three OUs created |
| Create and configure users | ADUC | `SydneyContractor`, `MelbourneContractor`, `BrisbaneContractor` | Accounts created, expiration configured, relocated to respective OUs |
| Create security group | ADUC | `Sydney Administrators` | Universal-scope group created, `SydneyContractor` added |
| Configure Protected User | ADUC | `SydneyContractor` | Added to Protected Users group |
| Delegate control | ADUC (Delegation of Control Wizard) | Sydney OU → Sydney Administrators | Password reset/force-change delegated |
| Configure and validate attribute | ADUC | `SydneyContractor` | City = Sydney, validated via Advanced Find |
| Disable account | ADUC | `MelbourneContractor` | Account disabled |
| Reset password | ADUC | `BrisbaneContractor` | Password reset |
| Configure domain password policy | GPMC / Default Domain Policy | Domain-wide | Minimum password length → 14 |
| Configure Fine-Grained Password Policy | ADAC | Domain Admins | `Domain Admin Password Policy` — precedence 1, min length 16 |
| Enable AD Recycle Bin | ADAC | Domain-wide | Recycle Bin enabled |
| Restrict NTLM | GPMC / Default Domain Controller Policy | Domain-wide | NTLM authentication → Deny all |
| Configure advanced auditing | GPMC / SydneyOUPolicy | Sydney OU | Audit User Account Management (Success/Failure) enabled |
| Configure user rights assignment | GPMC / SydneyOUPolicy | Sydney Administrators | Deny log on as a service |

---

### What I Learned (Skills Demonstrated)

Through this workflow, I learned how to:

- Enable Hyper-V and configure an internal virtual switch with NAT to support an isolated, internet-connected lab network.
- Build Windows Server virtual machines from installation media and configure their static networking.
- Distinguish between creating a brand-new Active Directory forest/domain versus joining an existing one.
- Promote a member server to a domain controller within an existing Active Directory domain.
- Understand and transfer a FSMO role (RID Master) between domain controllers.
- Create an Active Directory site and associate a subnet to reflect physical network topology.
- Structure a directory using organizational units to support scoped administration.
- Create, copy, and relocate user accounts, and configure account expiration.
- Create a security group with an appropriate group scope and manage its membership.
- Configure a user account as a Protected User and understand what that hardens.
- Delegate a specific, limited administrative capability using the Delegation of Control Wizard rather than granting broad rights.
- Configure and validate a custom user attribute using Advanced Find.
- Perform routine identity lifecycle actions: account disablement and password reset.
- Configure a domain-wide password policy via Group Policy.
- Create and apply a Fine-Grained Password Policy scoped to a specific privileged group.
- Enable the Active Directory Recycle Bin proactively.
- Restrict NTLM authentication domain-wide as a security hardening measure.
- Create and link a new GPO scoped to a specific OU.
- Configure advanced audit policy for account management activity.
- Configure a "Deny log on as a service" user rights assignment for a security group.

This workflow strengthened my understanding that Active Directory administration spans a wide range of responsibilities — from infrastructure-level domain controller management, to daily identity lifecycle tasks, to the password policy and security configuration decisions that directly affect an organization's exposure to credential-based attacks.

---

### Key Takeaways

This workflow showed that Active Directory administration is not a single task — it's a layered set of responsibilities that build on one another.

It involves a sequence of operational decisions:

```text
Establish infrastructure (promote DC, transfer FSMO, configure sites)
      ↓
Structure the directory (OUs, users, groups)
      ↓
Harden specific accounts and delegate scoped access (Protected Users, delegation)
      ↓
Enforce baseline and elevated password requirements (domain policy, FGPP)
      ↓
Reduce recovery risk (AD Recycle Bin)
      ↓
Restrict legacy authentication and improve visibility (NTLM restriction, advanced auditing)
      ↓
Reduce attack surface for specific groups (user rights assignment)
```

The most important lesson from this workflow is that Active Directory security isn't a single setting — it's the cumulative effect of many individually reasonable configuration decisions: structuring the directory sensibly, delegating narrowly instead of broadly, applying stricter requirements to higher-risk accounts, and closing off legacy authentication paths and unnecessary logon rights before they can be abused.

Each configuration change made in this workflow was small on its own, but together they represent the kind of layered, defense-in-depth thinking that distinguishes a well-administered Active Directory environment from one that's merely functional.