# Identity and Access Management Operations Using Active Directory — Operational Execution

**Category:** Identity & Access Management  
**Operational Focus:** User and group administration, organizational unit management, delegation, and access control validation in an enterprise directory  
**Primary Tools & Platforms:** Windows Server, Active Directory Domain Services, Active Directory Users and Computers (ADUC), Group Policy Management Console (GPMC)

This directory contains a complete, operationally structured record of an identity and access management execution performed within an Active Directory domain environment. The directory contains the execution writeups, screenshots, configuration changes, and supporting analysis that together reflect how administrators and SOC analysts manage identity objects and validate access controls in enterprise networks.

The goal of the execution is not to configure production-ready enterprise policy, but to practice and document how identity structures are built, modified, and validated at the object and policy level, and how those changes affect authentication, authorization, and administrative scope.

At a practical level, the execution follows the same workflow used during account provisioning, access reviews, and security investigations:

- First, directory structure is created and reviewed using organizational units to reflect administrative and security boundaries.  
- Next, user and group objects are created and assigned to represent role-based access models.  
- Then, permissions and delegations are applied to restrict or grant administrative control over specific directory scopes.  
- Finally, group policy and account behavior are validated to confirm that identity configuration changes actually affect authentication and authorization.

Throughout this execution, I intentionally focused on understanding how identity objects, groups, and policies interact rather than simply performing isolated configuration tasks. This reinforced how identity misconfiguration often leads directly to security incidents and lateral movement opportunities.

---

## How to Read This Folder

This folder is structured to separate configuration actions from analytical reasoning and operational reference material, similar to how IAM teams maintain change records alongside access control documentation.

> **Workflow vs Execution vs Writeup (Terminology Used Here)**  
> - **Workflows** refer to security operations tasks such as account provisioning, access validation, and privilege delegation.  
> - **Executions** refer to the hands-on performance of those tasks using directory services and management consoles.  
> - **Writeups** document how tasks were performed, what decisions were made, and how results were validated operationally.

- The complete step-by-step configuration and validation process is recorded in **`workflow-execution.md`**.  
- This `README.md` explains the identity management goals and how configuration steps map to security outcomes.  
- Supporting documentation captures analyst reasoning and access control implications.

---

## What This Execution Is Specifically Doing

This execution simulates how administrators and security teams structure directory services to control who can access systems and who can administer identity infrastructure.

First, organizational units (OUs) are created to establish logical separation between users, groups, and administrative scopes. This reflects how enterprises delegate management authority without granting full domain privileges.

Next, user accounts are created and configured with specific attributes and password policies, followed by the creation of security groups that represent job roles or access tiers. Users are then assigned to those groups to demonstrate role-based access control.

After group membership is established, delegation of control is applied to specific OUs so that limited administrators can manage subsets of directory objects without holding domain-wide privileges. This step demonstrates how least-privilege administration is enforced in enterprise environments.

Group Policy Objects are then reviewed or applied to validate how directory-level configuration translates into system behavior such as login restrictions, password complexity, and security settings.

Finally, authentication and authorization behavior is validated by confirming that group membership and delegated permissions actually affect what users and administrators can access or modify.

Each step reflects real-world IAM workflows where misconfiguration can directly enable privilege escalation, persistence, or unauthorized access.

---

## Why These Tasks Matter in Security Operations

Identity is the control plane of most enterprise environments. Attackers frequently target directory services because:

- access to identity systems enables lateral movement,  
- privilege escalation often occurs through group membership abuse, and  
- persistence is commonly established using directory object modifications.

SOC analysts must understand how directory objects are structured in order to:

- interpret authentication logs,  
- investigate suspicious account activity, and  
- validate whether identity changes are authorized or malicious.

This execution reinforces how identity configuration decisions directly affect security posture.

---

## Environment, Operating System, and Tooling

### Execution Environment

All activity was performed within a Windows domain environment with Active Directory Domain Services installed. The execution environment reflects a small enterprise-style lab domain rather than a standalone workstation.

### Operating System and Services

The environment included:

- Windows Server acting as a domain controller  
- Active Directory Domain Services managing directory objects  
- Group Policy infrastructure managing security configuration

### Administrative Tools Used

The execution used standard Windows administrative consoles including:

- Active Directory Users and Computers (ADUC)  
- Group Policy Management Console (GPMC)  
- Built-in directory object property editors

No third-party identity management tools were used.

---

## Data Sources and Evidence Types

Evidence and configuration artifacts reviewed during the execution included:

- directory object properties  
- group membership lists  
- OU permission assignments  
- group policy settings  
- authentication and access validation behavior

These are the same data sources used during identity-related incident response and compliance investigations.

---

## Operational Relevance to SOC and Detection Engineering Workflows

This execution reflects how identity systems are managed and investigated during both routine administration and security incidents.

In enterprise environments, identity infrastructure is frequently targeted by attackers because modifying directory objects, group memberships, or delegation permissions can enable lateral movement, privilege escalation, and long-term persistence.

SOC analysts investigating authentication anomalies or suspicious account behavior must understand how directory objects are structured, how group memberships grant access, and how administrative permissions are delegated. Without this understanding, analysts cannot reliably determine whether observed behavior is authorized or malicious.

Detection engineers rely on accurate identity modeling to build detections for privilege escalation, abnormal group membership changes, suspicious logon behavior, and unauthorized administrative actions.

This execution reinforces how configuration decisions at the directory level directly affect security posture and how misconfigurations often become attack paths exploited by adversaries.

Overall, the workflows demonstrated here support identity-based detection, investigation of account compromise, and validation of access control integrity.


---

## Files and Documentation Structure

This execution is documented using multiple files to reflect how identity management operations are separated into configuration actions, validation checks, and security analysis in enterprise environments.

### `README.md`

Provides context for identity and access management goals, directory structure design, and how these configurations impact enterprise security posture.

### `workflow-execution.md`

Contains the full sequence of directory administration tasks including OU creation, user and group management, delegation configuration, and group policy validation. This file documents exactly how identity controls were implemented and tested.

### `analyst-notes.md`

Documents access control reasoning, potential security risks introduced by misconfiguration, and investigative considerations related to privilege escalation and persistence.

### `tool-usage-notes.md`

Provides reference material for Active Directory administration tools, permission models, and delegation mechanisms used during the execution.

### `images/`

Contains screenshots validating directory structure, group membership assignments, and delegation settings.



---

## Skill Demonstration Context

This execution demonstrates enterprise IAM skills including directory object management, role-based access control, privilege delegation, and policy validation.

More complex identity attacks and investigations are documented separately in the **incident-response-and-investigations** repository. This execution focuses on defensive identity administration and access validation fundamentals.
