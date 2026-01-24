# Tool Usage Notes — Identity and Access Management (IAM) Operations Using Active Directory

> **Workflow vs Execution vs Writeup (Terminology Used Here)**  
> - **Workflows** refer to operational security tasks such as identity provisioning, access control design, and policy enforcement.  
> - **Executions** refer to the hands-on performance of those tasks using real directory services, management tools, and authentication mechanisms.  
> - **Writeups** document configuration steps, validation actions, and how identity changes affect security posture and detection visibility.

This document provides a technical reference for the native Windows directory management tools and PowerShell Active Directory cmdlets used during the IAM workflow execution. The focus is on how identity operations are performed, how permissions and policies propagate through the directory, and how those changes directly influence authentication and authorization behavior across domain‑joined systems.

All tooling used in this execution is native to Windows and Active Directory Domain Services. No third‑party identity management platforms or automation frameworks were used. This mirrors common enterprise environments where directory changes are performed using standard administrative consoles and scripting tools.

---

## Execution Platform and Operating Environment

### Active Directory Domain Environment

**Purpose:** Provide centralized authentication, authorization, and policy enforcement across all domain‑joined systems.  
**How It Was Used:** All identity objects (users, computers, groups, OUs) and policy settings were created and modified within the domain and automatically propagated to member systems.  
**Operational Relevance:** Mirrors real enterprise identity infrastructure where nearly all access control decisions depend on directory state.

Environment characteristics:

- **Directory Service:** Active Directory Domain Services (AD DS)  
- **Domain Model:** Single‑domain environment within a forest  
- **Authentication Protocols:** Kerberos (primary), NTLM (legacy fallback)  
- **Policy Framework:** Group Policy Objects (GPOs)  
- **Replication:** SYSVOL and directory replication across domain services

Directory replication ensures that configuration changes become effective across systems, which is why IAM changes are highly security‑sensitive and heavily audited in production environments.

---

### Administrative Access Context

**Purpose:** Perform identity operations using privileged directory accounts.  
**How It Was Used:** Identity management actions were executed from a domain‑joined administrative workstation using accounts with sufficient rights to create objects, modify OUs, and manage GPOs.  
**Operational Relevance:** Reflects standard practice where directory administration is performed remotely rather than directly on the Domain Controller console.

Access characteristics:

- **User Context:** Domain administrative privileges for configuration steps  
- **Management Style:** GUI‑based consoles supplemented with PowerShell for bulk actions  
- **Security Boundary:** OU‑level and group‑level permission enforcement

This separation supports least‑privilege models and role‑based administration in large organizations.

---

## Directory Management (GUI-Based Operations)

### Active Directory Users and Computers (ADUC)

**Purpose:** Primary graphical interface for managing directory objects and structure.  
**How It Was Used:**

- Reviewing default containers and built‑in security groups  
- Creating Organizational Units (OUs) to segment the directory  
- Creating and modifying user accounts  
- Reviewing and adjusting group memberships  
- Validating object placement before applying policies  

**Operational Relevance:** Object placement and group membership directly affect authentication outcomes, authorization decisions, and GPO inheritance.  
**Security Consideration:** Misplaced accounts or overly broad group membership can silently increase privilege and expand attack surface.

ADUC is also where many misconfigurations originate when directory structure is not intentionally designed.

---

### Delegation of Control Wizard

**Purpose:** Assign scoped administrative rights within specific OUs without granting full domain privileges.  
**How It Was Used:** Delegated password reset privileges to a support user (Phillip) only for the Sales OU.  
**Operational Relevance:** Enables helpdesk and tiered support models while preserving least‑privilege principles.  
**Security Risk:** Improper delegation can allow attackers to escalate privileges or pivot into sensitive administrative roles.

Delegation boundaries must be reviewed carefully during identity security assessments.

---

## Organizational Unit (OU) Architecture and Management

### OU Creation and Structure

**Purpose:** Provide logical segmentation for administration and policy application.  
**How It Was Used:** Created department‑based and device‑based OUs, including Sales, Marketing, Management, Workstations, and Servers.  
**Operational Relevance:** GPOs and delegated permissions are applied at OU boundaries, making OU design foundational to security posture.

Proper OU design supports:
- Role‑based administration
- Targeted policy enforcement
- Reduced blast radius for misconfigurations

---

### Accidental Deletion Protection

**Purpose:** Prevent unintentional removal of critical directory objects.  
**How It Was Used:** Temporarily disabled to allow controlled deletion of outdated OUs during restructuring.  
**Security Impact:** Protects against destructive administrative mistakes but must be consciously overridden when performing directory redesign.

This control is commonly enabled on production OUs to reduce operational risk.

---

## PowerShell Active Directory Cmdlets

PowerShell enables structured, repeatable, and auditable directory operations, especially for bulk changes.

### Get-ADComputer — Computer Object Enumeration

```powershell
Get-ADComputer -Filter 'Name -like "LPT-*"'
```

**Purpose:** Retrieve computer objects matching specific criteria.  
**How It Was Used:** Identify laptops and workstations using naming conventions before relocating them into proper OUs.  
**Operational Relevance:** Supports scalable directory hygiene and policy targeting across large device populations.

Filtering prevents manual errors and supports consistent administrative actions.

---

### Move-ADObject — Object Relocation

```powershell
Move-ADObject -TargetPath "OU=Workstations,DC=thm,DC=local"
```

**Purpose:** Relocate directory objects between containers and OUs.  
**How It Was Used:** Moved devices out of the default Computers container into policy‑controlled OUs.  
**Security Impact:** Changes which GPOs apply and which administrators have authority over the object.

Object placement is a frequent source of security misconfiguration in enterprise environments.

---

### Set-ADAccountPassword — Password Reset

```powershell
Set-ADAccountPassword sophie -Reset -NewPassword (Read-Host -AsSecureString)
```

**Purpose:** Perform administrative password resets.  
**How It Was Used:** Validate delegated support privileges for Sales OU users.  
**Security Risk:** Password resets are sensitive operations and often targeted during insider threat and breach scenarios.

Audit logging and delegation boundaries are critical controls for this operation.

---

### Set-ADUser — Account Attribute Updates

```powershell
Set-ADUser -ChangePasswordAtLogon -Identity sophie
```

**Purpose:** Modify security‑relevant account attributes.  
**How It Was Used:** Enforced credential hygiene following administrative password reset.  
**Operational Relevance:** Supports compliance with password management policies.

---

## Group Policy Management and Enforcement

### Group Policy Management Console (GPMC)

**Purpose:** Central platform for creating, linking, and managing GPOs.  
**How It Was Used:**

- Reviewing default GPO scope and inheritance  
- Modifying Default Domain Policy password settings  
- Creating custom Control Panel restriction GPO  
- Creating inactivity auto‑lock GPO  
- Linking policies at domain and OU levels  

**Operational Relevance:** GPOs define baseline security posture for all systems and are a primary enforcement mechanism in Windows environments.  
**Detection Impact:** Policy changes often correlate with compliance alerts and configuration drift detections.

---

### SYSVOL Replication and Policy Distribution

**Purpose:** Distribute GPO templates and scripts to all domain‑joined systems.  
**Observed in Workflow:** Policies stored under `C:\Windows\SYSVOL\sysvol` and pulled by endpoints during refresh cycles.  
**Security Impact:** Attackers targeting SYSVOL can deploy malicious scripts domain‑wide.

SYSVOL integrity is critical for preventing large‑scale compromise.

---

### gpupdate /force — Immediate Policy Refresh

```powershell
gpupdate /force
```

**Purpose:** Force immediate application of updated GPOs.  
**How It Was Used:** Validate that policy changes propagated without waiting for standard refresh intervals.  
**Operational Relevance:** Frequently used during troubleshooting and incident response when configuration changes must take effect quickly.

---

## Authentication Infrastructure Components

### Kerberos Authentication

**Components:** Client, KDC (Domain Controller), Service  
**Operational Relevance:** Kerberos ticket issuance and validation govern access to nearly all domain resources.  
**Detection Relevance:** Ticket activity generates security events used for lateral movement and credential abuse detection.

Understanding Kerberos flows is critical when investigating authentication anomalies.

---

### NTLM Legacy Authentication

**Purpose:** Backward compatibility with legacy systems and services.  
**Security Risk:** Vulnerable to pass‑the‑hash and relay attacks.  
**Detection Relevance:** NTLM usage in modern environments is frequently flagged as suspicious.

Reducing NTLM usage is a common identity‑hardening objective.

---

## Trust Relationships and Domain Scaling

### Trees and Forests

**Purpose:** Support organizational growth and administrative segmentation.  
**Security Impact:** Trust boundaries influence how compromise can spread between domains.

Attack paths frequently leverage misconfigured trust relationships.

---

### Trust Configuration

**Purpose:** Enable cross‑domain authentication and resource access.  
**Operational Relevance:** Common during mergers, acquisitions, and hybrid identity deployments.  
**Security Risk:** Improper trust scoping can enable lateral movement across business units.

Trust relationships are a major focus of identity threat modeling.

---

## Investigative Interpretation and Detection Relevance

Directory changes that typically warrant investigation:

- Increases in privileged group membership  
- New delegation assignments outside expected support roles  
- GPOs linked at overly broad scopes  
- Computers remaining in default containers  
- NTLM authentication in hardened environments

These signals frequently appear in breach investigations involving privilege escalation and lateral movement.

---

## Extending Into Detection and Monitoring

To operationalize detection of IAM abuse, environments typically ingest:

- Windows Security Event Logs (4624, 4672, 4720–4735, 4768–4776)  
- AD change auditing telemetry  
- SIEM identity correlation rules  
- Privileged Access Management (PAM) logs  

Configuration workflows demonstrated here directly generate telemetry used by these detection systems.

---

## Summary of Tools, Platforms, and Data Sources

- **Platform:** Windows domain environment  
- **Directory Service:** Active Directory Domain Services  
- **Management Interfaces:** ADUC, Group Policy Management Console  
- **Scripting Tools:** PowerShell AD module  
- **Authentication Protocols:** Kerberos, NTLM  
- **Policy Framework:** Group Policy Objects  
- **Data Sources:** Directory object state, policy configuration, authentication infrastructure  
- **Execution Method:** GUI‑based administration and scripted bulk operations  
- **Analysis Method:** Identity structure validation, permission scoping, and policy enforcement verification

These tools collectively support enterprise‑grade identity administration and security governance workflows.
