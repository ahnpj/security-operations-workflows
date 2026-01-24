# Workflow Execution — Identity and Access Management (IAM) Operations, Authentication, and Permission Validation Using Active Directory

---

## Overview

This section establishes the operational purpose of the workflow, the identity and access concepts being exercised, and how directory-level actions translate into real-world security posture and detection considerations.

> **Workflow vs Execution vs Writeup (Terminology Used Here)**  
> - **Workflows** refer to operational tasks such as onboarding telemetry and validating parsing behavior.  
> - **Executions** refer to hands-on configuration and validation using real data and Splunk services.  
> - **Writeups** document configuration decisions, troubleshooting steps, and validation results.

Key investigative and operational questions addressed include:

- How does the Domain Controller authenticate users and systems?
- How do OUs and group memberships affect access rights?
- How are permissions delegated safely across administrative roles?
- How does Group Policy enforce security configurations?
- How does Kerberos authentication function within the domain?
- How do trust relationships extend authentication across domains?

Each section builds on previous steps to show how identity infrastructure operates as an interconnected system rather than isolated configuration objects.

### Purpose and Analyst Focus

This workflow execution focuses on hands-on execution of core Active Directory identity and access management operations, including user creation, group membership, organizational unit (OU) structure, delegation, and Group Policy behavior.

This workflow write-up follows a progressive identity‑operations model, starting with domain fundamentals and expanding into user and group administration, organizational unit structure, delegated permissions, group policy enforcement, authentication mechanisms, and inter‑domain trust relationships.

The primary analytical focus is on understanding how administrative actions at the directory level directly affect:

- Authentication behavior
- Authorization boundaries
- Privilege distribution
- Policy enforcement across systems

A secondary objective is to reinforce how identity infrastructure underpins nearly all enterprise security controls. By directly interacting with domain services, authentication mechanisms, and policy enforcement, the workflow demonstrates how identity becomes both a control plane and an attack surface.

Rather than treating identity as an abstract configuration layer, the workflow approaches Active Directory as an operational control plane where every administrative action creates security-relevant state changes that may later appear in logs, alerts, or incident investigations.

The workflow execution emphasizes not only how to perform identity operations, but how to reason about the security implications of those operations when viewed from a SOC or detection engineering perspective.

### What This Workflow Demonstrates

This workflow demonstrates how identity infrastructure is structured, administered, and modified in realistic enterprise-like environments, focusing on the relationship between directory objects and access enforcement.

Key operational themes include:

- How user and group objects are created and organized within directory hierarchies
- How OU placement affects policy inheritance and administrative scope
- How delegation of control enables role-based administration without granting full domain privileges
- How group membership drives access decisions across services and systems

The workflow also highlights how small configuration changes — such as group membership or delegated permissions — can significantly alter an account’s effective privilege level, sometimes in ways that are not immediately visible without careful inspection of directory relationships.

Rather than isolating individual administrative tasks, the workflow treats directory management as a connected system where identity objects, policy application, and authorization logic interact continuously.

### Investigation and Detection Relevance

In modern enterprise environments, identity systems are among the most targeted and abused components during intrusion campaigns.

Attackers frequently exploit the following to escalate access, move laterally, or establish persistence:

- Excessive group membership
- Misconfigured delegation
- Poor OU segmentation
- Weak privilege separation

In enterprise environments, directory services are central to:

- Authentication and authorization enforcement
- Privileged access management
- Lateral movement prevention and detection
- Account compromise investigations
- Policy enforcement and compliance validation

Understanding how legitimate identity operations are performed provides critical baseline knowledge for distinguishing expected administrative activity from suspicious behavior in:

- Windows Security logs
- Active Directory audit logs
- SIEM identity detections
- EDR account-related alerts

By performing identity management actions directly, the workflow reinforces how directory modifications translate into observable security telemetry and why identity infrastructure remains one of the most sensitive control layers in enterprise defense.

> **Personal Learning Note:**
> Active Directory centralizes identity and access control. Instead of configuring users and permissions separately on each workstation, everything is managed from a single point of control. This creates consistency, reduces administrative effort, and supports enterprise‑level security models. AD acts as the backbone of identity services in Windows-based networks. This reinforced Security+ concepts like AAA (Authentication, Authorization, and Accounting) and the importance of centralized directory infrastructure.

---

## Environment and Execution Context

This section documents the directory environment, access methods, and operational constraints under which identity and access operations were executed, providing important context for how configuration changes propagate and how administrative boundaries are enforced.

### Execution Platform

All identity operations were performed within a Windows domain environment backed by Active Directory Domain Services.

Administrative access was conducted using standard Windows management tools from a domain-joined system with appropriate directory privileges. This reflects a common enterprise setup where identity management tasks are performed from administrative workstations or management servers rather than directly on domain controllers.

Environment characteristics:

- **Directory Service:** Active Directory Domain Services (AD DS)
- **Domain Architecture:** Single-domain environment
- **Administrative Access:** Domain-level administrative permissions
- **Management Interface:** Windows GUI-based directory management consoles

This setup mirrors common enterprise identity administration models where directory changes are made centrally and propagated automatically across domain infrastructure.

### Tooling and Constraints

All directory operations were performed using built-in Windows administrative tools without third-party identity management platforms or automation frameworks.

Primary tools included:

- Active Directory Users and Computers (ADUC)
- Native Windows management consoles
- Group Policy management interfaces

No external scripts or automation tooling were used, ensuring that all actions reflect how identity administration is commonly performed manually by system and directory administrators.

This constraint reinforces familiarity with:

- Standard administrative workflows
- Default permission boundaries
- Native delegation mechanisms

and avoids reliance on tooling that may abstract away underlying directory behavior.

### Data Sources Analyzed

The workflow operates primarily on directory object state rather than log telemetry, with emphasis on:

- **Active Directory Users and Computers (ADUC)** object hierarchy
- **User and group attributes** stored within the domain
- **Organizational Unit structure and delegation settings**
- **Group Policy Objects (GPOs)** and resulting policy application
- **Authentication workflows using Kerberos**
- **Trust configuration between domains**

Each of these directory elements directly influences authentication and authorization outcomes across domain-joined systems.

Although log analysis is not the focus of this workflow, all actions performed would generate corresponding identity-related events that are commonly ingested into:

- Security Information and Event Management (SIEM) platforms
- Identity protection systems
- Endpoint detection tools

Understanding the directory changes themselves is a prerequisite to interpreting those downstream telemetry signals during investigations.

### Workflow Map (High-Level)

The workflow follows a structured sequence designed to demonstrate how identity structure, policy scope, and delegated control interact within a domain environment:

1. Review existing directory structure and administrative boundaries  
2. Create and manage user and group objects  
3. Organize accounts within appropriate OUs  
4. Apply and observe Group Policy inheritance behavior  
5. Configure delegation of control for scoped administrative tasks  
6. Validate how permissions and policies propagate through directory hierarchy  

Each phase builds toward understanding how directory design choices influence both operational efficiency and security posture, reinforcing the importance of intentional identity architecture in enterprise environments.

---

## Step-by-Step Execution

### Step 1 — Domain Controller Role and Directory Initialization

I logged into a Windows Server virtual machine configured as the Domain Controller (DC). I reviewed the main role of a DC, which is to authenticate users, apply Group Policy, and maintain the AD database.

I confirmed the presence of the following management tools:

- Active Directory Users and Computers
- DNS
- Group Policy Management

I prepared the environment to explore how AD organizes users, computers, groups, policies, and authentication workflows.

---

### Step 2 — Domain Authentication and Controller Dependencies

**Objective:**
To understand the relationship between domain-joined systems and the Domain Controller, and how authentication requests are processed in a domain environment.

I logged into the Domain Controller and reviewed the domain configuration. I confirmed that DNS was hosted on the same system, which aligns with Kerberos requirements for service name resolution.

> **Personal Learning Note:**
> I noticed that the DC also hosted DNS, which matched what I learned during Security+. Kerberos authentication relies on DNS to resolve service names. I also reviewed how logging into a domain-connected machine contacts the DC to verify credentials.

This confirmed that authentication services are centralized and dependent on DC availability.

**Action Summary:**
- I examined the domain namespace and the server configuration.
- I confirmed that DNS was running on the DC, which is necessary for locating domain resources.
- I logged into a domain-joined workstation and observed that the login depended on contact with the DC.
- I reviewed how the DC stores user account information, verifies credentials, and processes access control.

> **Personal Learning Note:**
> Domains link all systems under one centralized identity platform. The DC is critical because it validates user access and enforces policy. Without a functioning DC, domain services and logins cannot occur. A domain centralizes identity and allows administrators to manage users and permissions from a single location. If the DC goes down, authentication fails—so the DC is critical infrastructure.

> **Personal Learning Note:**
> I learned how authentication traffic flows in a domain and how DNS and Kerberos rely on the DC. This directly connected to Security+ topics around secure authentication systems. I learned how domains unify authentication and how DNS and Kerberos rely on the DC. This connected directly to identity and AAA concepts from my Sec+ study.

---

### Step 3 — Users, Groups, and Organizational Unit (OU) Structure

**Objective**
To understand how Active Directory organizes and manages objects such as users and computers, and how OUs help structure the domain for easier administration.

I explored Active Directory Users and Computers (ADUC) and examined built‑in groups, the default Computers container, and the Domain Users group.

#### Step 3A — Creating and Exploring Organizational Units

I created new Organizational Units (OUs) to logically group users and machines, which makes policy assignment easier. 

I reviewed the difference between:

- **Security Groups** (used for assigning permissions)
- **Organizational Units (OUs)** (used for structure and policy scoping)

I then opened Active Directory Users and Computers (ADUC)

<p align="left">
  <img src="images/active-directory-domain-structure-01.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="800"><br>
  <em>Figure 1</em>
</p>

I explored the default OUs: Users, Computers, and Built‑in security groups. Then, I reviewed how to create new Organizational Units (OUs) to group users and systems for easier management. I created example user accounts and placed them inside appropriate OUs. Finally, I reviewed group membership and access inheritance.

### Step 3 Findings and Analysis

Security Groups handle permission assignment, while OUs provide structure and allow Group Policy to be applied at a targeted level. Organizing objects logically makes the domain easier to secure and maintain.

AD becomes much easier to manage when users and machines are organized clearly. Instead of assigning permissions individually, group‑based authorization saves time and reduces mistakes.

> **Personal Learning Note:**
> I learned the key difference between Groups and OUs. Groups define *what someone can do*, while OUs define *where and how users and computers are organized for administration and policy purposes*. I also learned how OUs form the structure of AD while groups handle access control. This supported the principle of least privilege and role-based access management from Security+.

---

### Step 4 — Delegation and Privilege Management

### Objective
To delegate administrative responsibility while maintaining the principle of least privilege.

I removed an outdated OU by disabling “Protect object from accidental deletion.” Then, using the Delegation of Control Wizard, I assigned a user (Phillip) permission to manage only the Sales OU. I reset a user password and confirmed that Phillip could manage Sales accounts but not the rest of the domain.

#### Step 4A — Deleting an OU

For testing, I attempted to delete the "Research and Development" OU, but I wasn’t able to because Active Directory indicated that I either didn’t have the necessary permissions or the OU was protected from accidental deletion.

<p align="left">
  <img src="images/active-directory-domain-structure-02.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="800"><br>
  <em>Figure 2</em>
</p>

#### Step 4B — Disabling accidental deletion protection feature

If the issue was simply a lack of permissions, there wouldn’t be anything I could do. 

However, assuming the OU was protected from accidental deletion, I disabled that protection. I went to the [View] tab and clicked [Advanced Features], which revealed additional containers. I then navigated to the THM OU (where the "Research and Development" child OU is located), right-clicked the "Research and Development" OU, selected [Properties], went to the [Object] tab, and unchecked "Protect object from accidental deletion".

<p align="left">
  <img src="images/active-directory-domain-structure-03.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="800"><br>
  <em>Figure 3</em>
</p>

I went back into the THM OU folder, right-clicked the "Research and Development" child OU, and selected [Delete].

<blockquote>
A new modal appeared asking me to confirm deletion nothing that the OU had other objects in it. I clicked [Yes].
</blockquote>

<p align="left">
  <img src="images/active-directory-domain-structure-04.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="800"><br>
  <em>Figure 4</em>
</p>

It was successfully deleted.

<p align="left">
  <img src="images/active-directory-domain-structure-05.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="800"><br>
  <em>Figure 5</em>
</p>

#### Step 4C — Using the Delegation of Control Wizard feature

At this point in the process, I worked with Active Directory Organizational Units (OUs) to delegate limited administrative privileges to a support user. The goal was to allow the user "Phillip", who is responsible for IT support, to reset passwords for users in the Sales OU without giving him full domain administrator rights.

> **Personal Learning Note:**
> I learned that Active Directory allows OU-level delegation, which lets specific users or groups manage only the objects they are responsible for. This is typically used in organizations to allow Helpdesk or Support personnel to perform tasks such as password resets or user unlocks while maintaining the principle of least privilege.

I opened **Active Directory Users and Computers**, enabled **Advanced Features**, then right-clicked the "Sales" OU and selected [Delegate Control]. I selected the user Phillip (THM\phillip).

This was to delegate OU-level privileges to Phillip for the Sales OU using the Delegation of Control Wizard.

<p align="left">
  <img src="images/active-directory-domain-structure-06.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="800"><br>
  <em>Figure 6</em>
</p>

<blockquote>
I typed his first name ("Phillip"), then clicked [Check Names] which automatically entered his information
</blockquote>

After selecting [OK], I selected Phillip’s account and delegated the “Reset user passwords and force password change at next logon” permission. 

> **Personal Learning Note:**
> This follows the principle of least privilege by only granting him the rights he needs to perform his role.

I granted permission to reset passwords and force password change at next logon.

<p align="left">
  <img src="images/active-directory-domain-structure-07.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="800"><br>
  <em>Figure 7</em>
</p>

#### Step 4D — Validating Delegated Permissions via RDP

To test the delegation, I logged into the domain using Phillip’s account. During the RDP login, I specified the domain "thm.local"

<blockquote>
Every Active Directory domain has a domain name. In this environment, the AD domain is named: thm.local. This is the internal domain namespace used by all users and computers in that AD environment. When I log in using RDP, Windows needs to know which domain the username belongs to. There are two accepted formats: "THM\phillip" or "phillip@thm.local". Alternatively, I could enter "thm.local" and manually enter Phillips login credentials, which is what I did.
</blockquote>

<p align="left">
  <img src="images/active-directory-domain-structure-08.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="800"><br>
  <em>Figure 8</em>
</p>

In this environment, the Active Directory domain name "thm.local" is configured to resolve directly to the domain controller. This means that when I entered "thm.local" in the **Computer** field in Remote Desktop Connection, RDP was able to locate and connect to the domain controller successfully.

<blockquote>
This is different from typical production environments where you usually RDP to a specific hostname or IP address. In many real-world setups, the domain name itself does not automatically resolve to a server for RDP.
</blockquote>

This resolved correctly to the domain controller and brought me to the Windows login screen with the default blue background. From there, I logged in using Phillip’s domain credentials.

<p align="left">
  <img src="images/active-directory-domain-structure-09.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="800"><br>
  <em>Figure 9</em>
</p>

I was successfully able to access Phillip’s session on the machine, confirming that the credentials and domain login were working as expected. Once logged in, I had full access to his desktop environment and could proceed with testing the delegated permissions.

<p align="left">
  <img src="images/active-directory-domain-structure-10.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="800"><br>
  <em>Figure 10</em>
</p>


#### Step 4E — Testing Delegated Permissions via PowerShell

To test Phillip's delegated permissions, I attempted to setting a new password for Sophie under Phillip's account.

Since Phillip does not have permission to open the ADUC GUI, I used PowerShell to perform the password reset. Using the `Set-ADAccountPassword cmdlet`, I reset Sophie’s password from the Sales department OU:

```powershell
Set-ADAccountPassword sophie -Reset -NewPassword (Read-Host -AsSecureString -Prompt 'New Password') -Verbose
```

- `Set-ADAccountPassword` - Change or reset a user’s password in Active Directory.
- `sophie` - The user account we are resetting the password for.
- `-Reset` - Force a password reset (instead of the user changing it themselves).
- `-NewPassword` - The new password we want to assign to the account.
- `(Read-Host -AsSecureString -Prompt 'New Password')` - Pops up a secure password prompt so we can type the new password without showing it.
- `-Verbose` - Shows a confirmation message so we know the command worked.

<p align="left">
  <img src="images/active-directory-domain-structure-11.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="800"><br>
  <em>Figure 11</em>
</p>

The output confirmed the operation was made on the correct user of the Sales department OU:

```powershell
VERBOSE: Performing the operation "Set-ADAccountPassword" on target "CN=Sophie,OU=Sales,OU=THM,DC=thm,DC=local"
```

After completing the reset, I enforced a password change upon Sophie’s next logon to ensure secure credential practices: 

```powershell
Set-ADUser -ChangePasswordAtLogon -Identity sophie -Verbose
```

- `Set-ADUser` - Used to modify settings for an Active Directory user.
- `-ChangePasswordAtLogon` - Forces the user to create a new password the next time they sign in.
- `-Identity sophie` - Specifies the user account we’re applying the change to (Sophie).
- `-Verbose` - Shows a confirmation message so we know the change was applied.

<p align="left">
  <img src="images/active-directory-domain-structure-12.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="800"><br>
  <em>Figure 12</em>
</p>

Phillip had successfully been delegated password reset capabilities for users inside the Sales OU, demonstrating how targeted privilege delegation supports operational efficiency while preserving security boundaries.

### Step 4 Findings and Analysis

Delegation allows organizations to split responsibility without granting full domain admin access. This reduces risk and supports scalable administration.

> **Personal Learning Note:**
> I learned how role-based control and least privilege are implemented in real environments, matching what I studied in Security+ regarding privilege separation and insider threat mitigation.


### Step 5 — Workstation and Server Organization

**Objective:**
Correctly organize computer objects in the domain so the right policies are applied to the right systems.

By default, any machine that joins a domain (except Domain Controllers) is automatically placed in the built-in Computers container. After reviewing the **Computers** container in **Active Directory Users and Computers**, I noticed that a mix of devices were stored there, including servers, laptops, and desktops. 

Keeping all devices together like this is not ideal because different device types often require different policies. For example, servers typically need stricter configurations and monitoring than the workstations that regular users log into every day.

To better organize the environment, I separated devices based on the device types. Following common AD best practices, I grouped machines into three main categories:

- Workstations – Devices that regular domain users log into for daily tasks and browsing. These should not have privileged accounts signed in.
- Servers – Systems that provide services to users or to other servers on the network.
- Domain Controllers – Already stored in their own OU by default, as they are considered the most sensitive systems in the domain.

#### Step 5A — Creating new OUs to better organize the environment

I created two new OUs: **Workstations** and **Servers** under the domain container. To achieve this, in **ADUC**, I right-clicked the domain, selected **[New > Organizational Unit]**.

<p align="left">
  <img src="images/active-directory-domain-structure-13.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="800"><br>
  <em>Figure 13</em>
</p>

I repeated this process twice: once to create an OU named "Workstations" and once to create an OU named "Servers". For both, I enabled the "Protect from accidental deletion" option.

<p align="left">
  <img src="images/active-directory-domain-structure-14.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="800"><br>
  <em>Figure 14</em>
</p>


#### Step 5B — Moving Devices into the Appropriate Organizational Units</h4>

After creating the Workstations and Servers OUs, I moved the existing computer objects out of the default **Computers** container and into their appropriate OUs. 

Doing so could have been done manually through the **Active Directory Users and Computers** (ADUC) GUI by simply dragging and dropping each device into the correct OU. 

This works fine for one or two devices, but since I was organizing multiple systems at once, I used PowerShell instead for efficiency and consistency.

I decided to move:

- All devices with names starting with "LPT-" or "PC-" to the Workstations OU.
- All devices with names starting with "SRV-" to the Servers OU.

<blockquote>
"LPT-" and "PC-" mean laptop and desktop PC, respectively.
</blockquote>

#### Step 5C — Moving Personal Computers and Laptops to the "Workstations" OU

In PowerShell, I ran the following command to move all personal computers (starting with PC) and laptops (starting with LPT):

```powershell
Get-ADComputer -Filter 'Name -like "LPT-*" -or Name -like "PC-*"' -SearchBase "CN=Computputers,DC=thm,DC=local" | Move-ADObject -TargetPath "OU=Workstations,DC=thm,DC=local"
```

- `Get-ADComputer` - Retrieves computer objects from Active Directory.
- `-Filter 'Name -like "LPT-*"'` - Selects laptops whose names begin with "LPT-"
- `-or Name -like "PC-*"'` - Includes computers whose names begin with PC-.
- `-SearchBase "CN=Computers,DC=thm,DC=local"` - Tells the command to look inside the default Computers container.
- `|` (pipeline) - Sends the list of computer objects to the next command.
- `Move-ADObject` - Moves each computer object to a different location in AD.
- `-TargetPath "OU=Workstations,DC=thm,DC=local"` - Specifies that the matching workstation systems should be moved into the Workstations OU.

<p align="left">
  <img src="images/active-directory-domain-structure-15.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="800"><br>
  <em>Figure 15</em>
</p>

It took a several attempts to get the syntax exactly right, but once I confirmed the command was functioning, I reopened PowerShell and captured a clean screenshot of the final working version.

<p align="left">
  <img src="images/active-directory-domain-structure-16.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="800"><br>
  <em>Figure 16</em>
</p>

#### Step 5C — Moving Servers to the "Servers" OU

In PowerShell, I ran the following command to move all personal computers (starting with PC) and laptops (starting with LPT):

```powershell
Get-ADComputer -Filter 'Name -like "SRV-*"' -SearchBase "CN=Computers,DC=thm,DC=local" |
Move-ADObject -TargetPath "OU=Servers,DC=thm,DC=local"
```

- `Get-ADComputer` - Retrieves computer objects from Active Directory.
- `-Filter 'Name -like "SRV-*"'` - Selects devices whose names begin with "SRV-"
- `-SearchBase "CN=Computers,DC=thm,DC=local"` - Tells the command to look inside the default Computers container.
- `|` (pipeline) - Sends the list of computer objects to the next command.
- `Move-ADObject` - Moves each computer object to a different location in AD.
- `-TargetPath "OU=Servers,DC=thm,DC=local"` - Specifies that the matching server systems should be moved into the Servers OU.

<p align="left">
  <img src="images/active-directory-domain-structure-17.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="800"><br>
  <em>Figure 17</em>
</p>

Similarly, it took several attempts to get the syntax exactly right, but once I confirmed the command was functioning, I reopened PowerShell and captured a clean screenshot of the final working version.

<p align="left">
  <img src="images/active-directory-domain-structure-18.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="800"><br>
  <em>Figure 18</em>
</p>

### Step 5 Findings and Analysis

Grouping devices makes policy management predictable and easier to maintain. Servers require stricter controls than regular user workstations.

Separating servers from workstations allows different GPOs to apply depending on security requirements. Servers need stricter controls than user machines.

---

### Step 6 — Group Policy Configuration and Enforcement

**Objective:**
Understand how Group Policy enforces settings and security standards across the domain.

After verifying that my domain was set up correctly, I opened Active Directory Users and Computers (ADUC) and navigated to the thm.local domain. 

I first confirmed that users and computers were currently stored under default containers such as Users and Computers, which is not ideal for long-term management, especially when implementing Group Policy later.

I reviewed default GPOs, edited domain password policy, created custom policies, and validated inheritance.

#### Step 6A — Reviewing Existing GPOs</h4>

I opened **Group Policy Management** console and reviewed existing GPOs and saw there were three GPOs:

- Default Domain Controllers Policy
- Default Domain Policy
- RDP policy

Both "Default Domain Policy" and "RDP" policy were linked to the domain as a whole, and the "Default Domain Controllers Policy" GPO was linked to the "Domain Controllers" OU.

<p align="left">
  <img src="images/active-directory-domain-structure-19.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="800"><br>
  <em>Figure 19</em>
</p>

#### Step 6B — Examining the Default Domain Policy GPO

I examined the **Default Domain Policy** GPO to check its scope. Confirmed that this policy is linked at the domain level, which means it affects the entire domain by default. 

<p align="left">
  <img src="images/active-directory-domain-structure-20.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="800"><br>
  <em>Figure 20</em>
</p>

> **Personal Learning Note:**
> Security Filtering controls who a GPO actually applies to. By default, a GPO is set to apply to Authenticated Users, which includes all users and all computers in the domain. However, Security Filtering allows us to narrow down the policy so that it only applies to specific users, computers, or groups. This is helpful when we want to target a policy to only certain systems, such as a particular OU or a selected group of machines, without affecting the entire domain.

> **Personal Learning Note:**
> Administrators control the reach of a GPO, making sure policies are only applied where they are intended, which prevents configuration mistakes and keeps the environment organized.

After creating and organizing the OUs, I opened Group Policy Management to review the existing policies applied at the domain level. I selected the Default Domain Policy and navigated to **[Computer Configuration → Policies → Windows Settings → Security Settings]**. This section controls many of the core security behaviors for all domain-joined systems.

<p align="left">
  <img src="images/active-directory-domain-structure-21.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="800"><br>
  <em>Figure 21</em>
</p>

<blockquote>
Without Security Filtering, the policy applies to everyone. With Security Filtering, the policy applies only to the users or computers that I choose to include.
</blockquote>

#### Step 6C — Editing the Default Domain Policy GPO

Since the Default Domain Policy is linked at the domain level, any changes made to it would affect every user and every computer in the domain. To see how policy changes are applied, I edited this GPO and updated the password requirements.

I right-clicked the **Default Domain Policy** and selected **Edit**. This opened the **Group Policy Management Editor**, where all the policy settings are organized.

To change the minimum password length, I navigated to: **[Computer Configuration > Policies > Windows Settings > Security Settings > Account Policies > Password Policy]**

<p align="left">
  <img src="images/active-directory-domain-structure-22.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="800"><br>
  <em>Figure 22</em>
</p>

I set the minimum password length policy to 10 characters.

This change meant that any user account in the domain must now use a password with at least 10 characters, helping enforce stronger password security across the environment.

<p align="left">
  <img src="images/active-directory-domain-structure-23.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="800"><br>
  <em>Figure 23</em>
</p>

#### Step 6D — Editing the Default Domain Policy GPO

Group Policy Objects (GPOs) are distributed across the network. When a GPO is created or modified, it is stored in a shared folder on the Domain Controller called `SYSVOL`. 

This share is automatically available to all domain-joined computers so they can regularly pull down any new or updated Group Policy settings.

The SYSVOL share is located at:

`C:\Windows\SYSVOL\sysvol`

This meant that computers in the domain will automatically receive updated policies over time. However, the update is not instant. It can take up to about 2 hours for Group Policy changes to fully sync and apply across the network. 

To apply the changes immediately, I ran the PowerShell command below which forced the system to re-check and apply the latest GPO settings right away:

```powershell
gpupdate /force
```

<p align="left">
  <img src="images/active-directory-domain-structure-24.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="800"><br>
  <em>Figure 24</em>
</p>

#### Step 6E — Creating and Applying Custom GPOs

For testing, I created two new Group Policy Objects (GPOs) to enforce security and access control settings across the domain:

- Restrict access to the Windows Control Panel for non-IT users (Step 6F)
- Automatically lock workstations and servers after 5 minutes of inactivity (Step 6G)

#### Step 6F — Restricting Access to the Control Panel

The goal of this policy was to make sure that only members of the IT department could access the Control Panel. Other departments (Sales, Marketing, Management, etc.) should not be able to modify system settings on their machines.

I created a new GPO named "Restrict Control Panel Access" and opened it for editing.

<p align="left">
  <img src="images/active-directory-domain-structure-25.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="800"><br>
  <em>Figure 25</em>
</p>

After creating the GPO, I opened it for editing. Inside the Group Policy Management Editor, I navigated to **[User Configuration → Policies → Administrative Templates → Control Panel]**. I updated setting for the "Prohibit access to Control Panel and PC Settings policy" and se it to **[Enabled]**, and saved the configuration.

<p align="left">
  <img src="images/active-directory-domain-structure-26.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="800"><br>
  <em>Figure 26</em>
</p>

The filter in the new GPO was successfully enabled:

<p align="left">
  <img src="images/active-directory-domain-structure-27.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="800"><br>
  <em>Figure 27</em>
</p>

Once the GPO was configured, I linked it to all of the OUs corresponding to users who shouldn't have access to the Control Panel of their PCs. In this case, I applied the GPO to the following child OUs:

- Marketing
- Management
- Sales OUs

I achieved this with a simple drag-and-drop:

<p align="left">
  <img src="images/active-directory-domain-structure-28.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="800"><br>
  <em>Figure 28</em>
</p>

This ensured that only users within those specific OUs would be restricted, while IT staff retained full access.

#### Step 6G — Automatically lock workstations and servers after 5 minutes of inactivity

Next, I created a GPO to automatically lock workstations and servers after 5 minutes of inactivity. I named this policy "Auto Lock Screen". 

Since I wanted this policy to apply to all computers in the domain, I linked the GPO at the root domain level (thm.local). Because the Workstations, Servers, and Domain Controllers OUs are all child OUs of the domain, they automatically inherited this policy.

<p align="left">
  <img src="images/active-directory-domain-structure-29.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="800"><br>
  <em>Figure 29</em>
</p>

I opened the GPO Editor for the "Auto Lock Screen" GPO, and navigated to **[Computer Configuration > Windows Settings > Security Settings > Security Options]**. Then, I located the "Interactive logon: Machine inactivity limit".

<p align="left">
  <img src="images/active-directory-domain-structure-30.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="800"><br>
  <em>Figure 30</em>
</p>

I selected the "Define this policy setting" checkbox to allow for editing, then set the inactivity limit to 5 minutes (which is 300 seconds).

<p align="left">
  <img src="images/active-directory-domain-structure-31.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="800"><br>
  <em>Figure 31</em>
</p>

After closing the GPO editor, I linked the GPO to the root domain by dragging the GPO into it.

<p align="left">
  <img src="images/active-directory-domain-structure-32.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="800"><br>
  <em>Figure 32</em>
</p>

#### Step 6H — Verifying GPOs have been applied to the correct OUs</h4>

To verify, I logged into the domain using Mark’s account, who is in the "Marketing" OU. The Marketing OU should now have the following GPOs applied to it:

- Restrict access to the Windows Control Panel
- Automatically lock workstations and servers after 5 minutes of inactivity

During the RDP login, I specified the domain "thm.local"

In this environment, the Active Directory domain name "thm.local" is configured to resolve directly to the domain controller. This means that when I entered "thm.local" in the **Computer** field in Remote Desktop Connection, RDP was able to locate and connect to the domain controller successfully.

<blockquote>
This is different from typical production environments where you usually RDP to a specific hostname or IP address. In many real-world setups, the domain name itself does not automatically resolve to a server for RDP.
</blockquote>

<p align="left">
  <img src="images/active-directory-domain-structure-33.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="800"><br>
  <em>Figure 33</em>
</p>

This resolved correctly to the domain controller and brought me to the Windows login screen with the default blue background. From there, I logged in using Mark’s domain credentials.

<p align="left">
  <img src="images/active-directory-domain-structure-34.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="800"><br>
  <em>Figure 34</em>
</p>

I was successfully able to access Mark’s session on the machine, confirming that the credentials and domain login were working as expected. Once logged in, I had full access to his desktop environment and could proceed with testing the delegated permissions.

<p align="left">
  <img src="images/active-directory-domain-structure-35.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="800"><br>
  <em>Figure 35</em>
</p>

#### Step 6I — Testing Control Panel Access

I opened the Control Panel, but access was blocked. This confirmed that the GPO restricting Control Panel access was successfully applied to the intended OUs:

- Marketing
- Management
- Sales OUs

Mark being in the "Marketing" OU should not have access to the Control Panel, which worked successfully.

<p align="left">
  <img src="images/active-directory-domain-structure-36.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="800"><br>
  <em>Figure 36</em>
</p>

#### Step 6J — Testing inactivity GPO

I left the workstation idle for five minutes to verify the Auto Lock Screen GPO from Step 6G. After the idle period, the screen automatically locked and required credentials to log back in, confirming the policy was successfully applied.

<blockquote>
Had the policies not taken effect right away, I would have opened PowerShell and used the gpupdate /force command to immediately refresh and apply the updated GPOs.
</blockquote>

### Step 6 Findings / Analysis

Group Policy ensures consistency, compliance, and baseline security across large numbers of systems. It is one of the strongest administrative tools in AD environments.

Group Policy allows configuration enforcement at scale. It ensures users and machines follow consistent security rules, which is something I repeatedly saw emphasized in Security+ for enterprise hardening.

---

### Step 7 — Kerberos vs NTLM Authentication Review

**Objective:**
To understand how Active Directory handles authentication using Kerberos and how NTLM remains for legacy support.

#### Review & Analysis
I reviewed how authentication works in a Windows Active Directory domain, focusing on Kerberos and NTLM. I remembered learning the high-level concepts when studying for the CompTIA Security+ exam, but seeing the actual authentication flows laid out step-by-step helped solidify how they function in a real enterprise environment.

**Review & Analysis: Kerberos**

Kerberos is the default authentication protocol used in modern AD domains. Instead of sending passwords or password hashes across the network repeatedly, Kerberos uses encrypted tickets and temporary session keys to verify identity. This greatly reduces credential exposure and makes it harder for attackers to reuse stolen hashes.

The Kerberos authentication workflow involves three main components:
- Client: The user or device attempting to authenticate.
- Key Distribution Center (KDC): Located on the Domain Controller; responsible for issuing tickets.
- Service / Server: The resource being accessed (e.g., file server, SQL server, internal web app).

The Domain Controller acts as the Key Distribution Center (KDC), which provides two important services:

- The Authentication Service (AS)
- The Ticket Granting Service (TGS)

**Kerberos Authentication Steps:**

1. **Initial User Login** - When a user logs into Windows, they enter their username and password. The workstation does not send the password to the Domain Controller. Instead, it creates an encrypted timestamp using the user's password hash (which is stored locally after the login attempt) and sends that to the KDC. This is known as the AS-REQ.
2. **Ticket Granting Ticket (TGT) Issued** - The KDC verifies the timestamp by decrypting it with the stored copy of the user’s password hash. If it matches, the KDC sends two things back to the client:

- A Ticket Granting Ticket (TGT), which is encrypted using the krbtgt account hash.
- A Session Key, which is shared between the client and the KDC for further communication.

The user cannot open the TGT, since it is encrypted using the domain’s internal krbtgt secret key.

3. **Requesting Access to a Service** - When the user tries to access something like a network file share or database, the client sends the following to the KDC:

- The TGT
- A new encrypted timestamp using the session key
- The Service Principal Name (SPN) of the service being requested
- This is the TGS-REQ.

4. **Ticket Granting Service Response** - The KDC verifies the request and creates:

- A Service Ticket (TGS ticket), encrypted with the service account’s password hash
- A new Service Session Key for encrypted communication between the client and the service
  
The client still cannot read the ticket; only the target service can decrypt it.

5. **Authenticating to the Service** - The client presents the Service Ticket to the service. The service decrypts it using its own stored account hash. If the decrypted ticket is valid and the timestamps match, the service allows the user to connect.

With Kerberos, the important detail is that credentials are never re-sent once authentication begins. Access relies on time-limited tickets and session keys, which is why Kerberos is considered secure and efficient.

**Review & Analysis: NTLM Authentication (Challenge-Response Model)**

NTLM is the older authentication protocol and still shows up in environments for backward compatibility. NTLM does not use tickets. Instead, it relies heavily on the user’s password hash, which can be vulnerable to Pass-the-Hash attacks.

**NTLM Authentication Steps:**

1. The client requests authentication from a server.
2. The server sends a randomly generated challenge back to the client.
3. The client encrypts (hashes) the challenge using its NTLM password hash and sends the result back.
4. The server forwards the challenge and the response to the Domain Controller.
5. The Domain Controller performs the same hashing operation using its stored copy of the user’s password hash.
6. If the results match, access is granted; if not, the request is denied.

One major drawback is that if an attacker steals the NTLM hash, they can authenticate without needing to know the actual password.

### Step 7 Key Takeaways

Seeing the actual authentication flows helped reinforce the Security+ concepts I studied. Kerberos makes sense now in a more practical way: it reduces credential exposure by using tickets and session keys instead of password hashes. NTLM, on the other hand, now feels clearly outdated and risky, especially because of how frequently Pass-the-Hash attacks are seen in real incident response cases.

Understanding these authentication flows is extremely important for Blue Team and SOC Analyst work, especially when reviewing Windows Event Logs, investigating failed logon attempts, or analyzing lateral movement across a network.

---

### Step 8 — Trees, Forests, and Trust Relationships

**Objective:**
Understand how AD scales to large and multi-organization environments.

#### Review & Analysis

I reviewed the concepts of Trees, Forests, and Trust Relationships within Active Directory. I had learned these concepts at a high level while studying for the CompTIA Security+ exam, but documenting them here helped clarify how these structures apply in real enterprise environments.

A single domain (such as thm.local) is enough for smaller organizations. However, as a company grows, especially across regions or business units, managing everything under one domain can become difficult and inefficient. Different teams may require separate administrative control, unique Group Policies, or different compliance requirements.

**Review & Analysis: Trees**

A **Tree** is formed when multiple domains share the same root namespace. For example:
- thm.local (root domain)
- uk.thm.local
- us.thm.local

<blockquote>
These are separate domains but share the same namespace ("thm.local") and are connected hierarchically. Each location or administrative unit can manage its own domain resources without interfering with others. Domain Admins in each domain only control their part of the network.
</blockquote>

However, an Enterprise Admin (a different security group) has full control across the entire Tree.

**Review & Analysis: Forests**

I learned (and reviewed) that a **Forest** is created when there are multiple Trees that do NOT share the same namespace. For example, if company THM merges with another company MHT Inc., each may already have its own established domain structure:
- thm.local Tree
- mht.local Tree

Combining them creates a Forest. This allows the organizations to connect their networks while still keeping their internal structures separate. Each Tree maintains its own domain namespace and domain admins.

The Forest acts as the security boundary for the network. Enterprise Admins can manage objects across all domains in the Forest.

**Review & Analysis: Trust Relationships**

**Trusts** allow users in one domain to access resources located in another domain. Without a trust, domains are isolated from each other.

There are two main types of Trusts:

1. One-Way Trust:
   - Domain A trusts Domain B
   - Users in Domain B can access resources in Domain A
   - But users in Domain A cannot access resources in Domain B unless another trust is configured in the opposite direction.

2. Two-Way Trust:
   - Both domains mutually trust each other
   - Users from either domain can be granted access to resources in the other

By default, domains in the same Tree or Forest automatically establish Two-Way Transitive Trusts, meaning permissions can be granted across domains as needed.

It is important to note that a trust relationship only allows the opportunity for access. Permissions must still be configured at the group, file, or service level before access is actually granted.

This explained how AD scales across large organizations.

### Step 8 Key Takeaways

Understanding how Trees and Forests are structured makes it clear why large enterprise environments rarely use only one domain. Delegation of control, geographic separation, compliance rules, and operational boundaries all require structured domain hierarchy.

Trusts are also key in real-world networks, especially during mergers, acquisitions, or environments with multiple business units.

This ties directly back to the Security+ objectives around AAA (authentication, authorization, accounting) and enterprise identity management. Seeing the visual breakdown and practical examples made these architectural concepts much clearer and easier to remember.

---

## Results & Interpretation

This workflow confirmed how identity, authentication, and access control are enforced centrally in Windows enterprise environments. Administrative boundaries, policy enforcement, and authentication protocols form layered controls that reduce operational risk and support scalable security governance.

---

## Operational & Defensive Takeaways

- Misconfigured delegation can allow privilege escalation.
- Weak GPO scoping can expose systems to insecure configurations.
- NTLM exposure increases lateral movement risk.
- Poor OU design complicates detection and enforcement.
- Identity infrastructure is a primary attack target in enterprise breaches.

---

## Reuse Pack (Quick Reference)

- ADUC for object management
- Delegation of Control Wizard for least-privilege assignment
- PowerShell AD cmdlets for bulk operations
- Group Policy Management Console for enforcement
- gpupdate /force for immediate policy refresh

---

## What I Learned (Skills Demonstrated)

I gained practical experience working with AD infrastructure and now understand how identity, access control, and system configuration are enforced in enterprise environments.

- Active Directory administration
- Identity and access control design
- Privilege delegation modeling
- Policy enforcement verification
- Authentication protocol analysis
- Enterprise directory architecture concepts

### Conclusion and Final Reflection

This has allowed me to practice real administrative tasks in Active Directory while reinforcing Security+ identity management concepts. I learned how to organize users and devices, delegate privileges, apply Group Policy, and understand authentication protocols.

This workflow execution has also helped me move from theoretical understanding to hands‑on use of Active Directory. I managed users, devices, OUs, delegated privileges, and configured GPOs. I also reinforced key identity and authentication concepts from my CompTIA Security+ studies.

