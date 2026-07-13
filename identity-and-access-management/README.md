# Identity & Access Management (IAM)

(`identity-and-access-management/README.md`)

This folder contains workflow executions where the **primary focus is validating authentication behavior, access controls, and permission enforcement across identity systems, as well as hands-on administration of Active Directory Domain Services environments**. These workflows simulate how SOC analysts, IAM engineers, and systems administrators verify identity service behavior, administer directory infrastructure, and investigate suspicious or unexpected authentication and authorization activity using repeatable, tool-driven methods.

Rather than presenting abstract identity concepts, each folder documents practical execution of identity validation, access control testing, and directory administration activities, including commands and queries used, analyst reasoning behind each verification step, and interpretation of results within an investigative or administrative context. The emphasis is on operational process and analyst decision-making, similar to internal SOC runbooks and identity operations working notes.

Each folder uses a tool-first naming convention followed by the primary operational focus (for example, `active-directory-iam-operations-and-permission-validation`). This allows quick identification of technologies being used while keeping documentation centered on investigative workflow and analytical reasoning rather than directory service theory alone.

> 👉 **Each folder represents one complete workflow execution**  
Every subfolder here is a **fully self-contained operational execution scenario**. Each one documents how a specific identity validation, access control verification, or Active Directory administration task is performed from initial objective through validation and operational interpretation.

> 👉 **Follow the workflow execution first**  
Begin with `workflow-execution.md` inside a workflow folder to see how authentication activity, directory objects, permission paths, and administrative tasks were analyzed, configured, and validated step by step using commands, queries, and investigative methodology.

> 👉 **See what files exist in each workflow folder**  
For a complete breakdown of every standard file included in a workflow execution — including what each document contains, how it supports automation and detection engineering workflows, and how the documentation is structured — see the **[Workflow Documentation Structure](#workflow-documentation-structure)** section below.

> 👉 **See which workflow executions currently exist in this category**  
For a list of all identity and access management executions currently included in this folder, along with a description of what each workflow demonstrates and which operational skills it focuses on, see the **[What's in This Folder](#whats-in-this-folder)** section below.

> **Terminology used in this category:**  
> **Workflows** refer to common security operations and systems administration tasks (such as authentication validation, permission testing, or domain controller deployment).  
> **Executions** refer to the hands-on performance of those tasks using real tools, logs, and directory objects.  
> **Writeups** document how the task was performed and how results were interpreted.

---

### How Workflows Are Categorized

Workflows are grouped here when **authentication validation, identity behavior analysis, access control verification, and Active Directory administration are the primary operational objectives**, rather than alert triage or full incident reconstruction.

Although these activities frequently support investigations and incident response, they are categorized separately because their primary purpose is verifying identity security posture, administering directory infrastructure, and enforcing authentication and authorization controls before deeper investigative or containment actions occur.

Workflows in this category typically focus on:

- **Authentication log analysis**, reviewing successful and failed login activity to identify abnormal access patterns, unusual login sources, or repeated failures indicating credential misuse or brute-force attempts.

- **Account and group membership validation**, inspecting user, service account, and administrative group assignments to confirm privilege levels align with organizational role expectations and policy requirements.

- **Permission and access path testing**, verifying which systems, shares, or services specific accounts can access to identify excessive privileges or unintended exposure.

- **Directory object inspection**, reviewing attributes of users, computers, and service accounts to detect misconfigurations, privilege escalation risks, or indicators of compromise.

- **Policy and control verification**, validating authentication and authorization configurations to ensure identity protections are enforced consistently across environments.

- **Active Directory domain controller administration**, deploying and managing domain controllers, configuring site and subnet topology, transferring FSMO roles, and validating replication to support branch office and multi-DC environments.

- **Identity lifecycle management**, creating, modifying, delegating, and bulk-managing user accounts and organizational units using both graphical consoles and PowerShell automation.

- **Directory security hardening**, configuring domain and fine-grained password policies, enabling the Active Directory Recycle Bin, restricting legacy authentication protocols, and applying Group Policy security settings.

Although these workflows support investigations, they are organized here when the **core work involves validating identity behavior, administering directory infrastructure, and enforcing access boundaries rather than reconstructing full incident timelines.**

---

### Operational Context and Purpose

Identity validation and Active Directory administration workflows typically occur during alert investigation, insider threat analysis, lateral movement detection, routine security control validation, branch office deployments, and domain infrastructure maintenance.

At this stage of security operations, the objective is to determine whether observed authentication or authorization behavior represents legitimate usage, misconfiguration, or malicious activity — and to administer and harden the directory infrastructure that governs identity and access across the environment. Analysts and administrators focus on how accounts authenticate, what resources they can access, how directory infrastructure is deployed and maintained, and whether those configurations align with organizational policy.

The execution examples in this category reflect real SOC, IAM, and systems administration tasks where targeted validation and hands-on administration help determine whether escalation, containment, remediation, or configuration change is required.

---

### What's in This Folder

Each workflow execution is contained in its **own dedicated folder** and represents **one complete operational identity validation or Active Directory administration scenario**, including execution walkthroughs, analytical reasoning, validation outcomes, and tooling references.

Current workflow executions include:

- **Identity and Access Management (IAM) Operations, Authentication, and Permission Validation Using Active Directory**  
  (`active-directory-iam-operations-and-permission-validation`)  
  Focuses on validating user authentication events, group memberships, and effective permissions within an Active Directory environment. Tasks include reviewing logon activity, inspecting group assignments, and testing access to protected resources to identify excessive privileges or configuration weaknesses.

- **Active Directory Domain Services Administration — Domain Controller Operations, User Management, Password Policies, and Security Configuration**  
  (`active-directory-domain-services-administration`)  
  Focuses on building and administering a two-domain-controller Active Directory environment from scratch. Tasks include preparing the Hyper-V lab environment, promoting a domain controller and transferring a FSMO role, configuring AD site/subnet topology, managing organizational units and user accounts, configuring Protected Users and delegation of control, applying domain and fine-grained password policies, enabling the AD Recycle Bin, restricting NTLM authentication, and configuring advanced audit policy and user rights assignment via Group Policy.

- **Active Directory Branch Office Deployment and Security Hardening**  
  (`active-directory-branch-office-deployment-and-security-hardening`)  
  Focuses on extending an existing Active Directory domain to support a new branch office. Tasks include deploying a second domain controller and configuring site and subnet topology, creating the branch office OU structure and delegating scoped administrative rights, performing bulk user lifecycle management using PowerShell, configuring domain and fine-grained password policies, enabling the Active Directory Recycle Bin, restricting NTLM authentication domain-wide, and applying Group Policy security settings including failed-logon auditing and user rights assignment.

Additional workflow executions will be added as identity coverage expands across authentication methods, directory services, and access control scenarios.

---

### Workflow Documentation Structure

Each workflow execution is fully self-contained and uses documentation aligned with how identity validation, access control, and Active Directory administration tasks are performed in operational environments.

| File / Folder | Purpose | Contents and Focus |
|-------------|-------------|---|
| **`workflow-execution.md`** | Operational execution walkthrough | Step-by-step identity validation or administration workflow showing commands, queries, permission checks, investigative pivots, and validation checkpoints |
| **`README.md`** | Workflow context and operational objective | Describes the authentication, access control, or administration objective, workflow scope, and how the execution supports detection and investigation workflows |
| **`analyst-notes.md`** | Analytical reasoning and operational considerations | Documents why specific identity validation or administration techniques were selected and how authentication and access behavior supports investigative or operational conclusions |
| **`tool-usage-notes.md`** | Tool and query reference documentation | Explains directory service tools, command syntax, query logic, and permission inspection techniques applicable to similar identity investigations and administration tasks |
| **`automation-design-notes.md`** *(when present)* | Automation and scalability planning | Documents scripted identity validation approaches, bulk permission analysis techniques, and design considerations for scaling or integrating identity checks into detection or monitoring pipelines |
| **`images/` or `screenshots/`** | Validation and evidence artifacts | Contains authentication logs, directory views, permission validation results, and visual confirmation of identity verification and administration steps |

Together, these files separate **identity validation and administration execution**, **analytical reasoning**, **tool reference**, and **automation planning** into clearly reviewable components while remaining tied to the same operational workflow.

---

### How These Workflow Executions Are Designed

This category is **execution-focused**, not theoretical.

You will find:

- Realistic authentication and access validation walkthroughs
- Active Directory domain controller deployment and administration scenarios
- Directory inspection and permission testing techniques
- Bulk identity lifecycle management using PowerShell
- Group Policy security configuration and hardening
- Validation checkpoints and investigative interpretation of authentication behavior
- Detection support and identity monitoring improvements tied to validation outcomes
- Operational decision-making behind identity verification methodology and investigative pivots

Each workflow demonstrates not just how identity validation and administration are performed, but why access boundary testing, directory security hardening, and authentication monitoring are critical for early detection of credential abuse, privilege escalation, and policy violations.

Documentation is intentionally structured to resemble internal SOC identity operations procedures and analyst runbooks rather than tutorial-style instruction.

---

### Relationship to Full Incident Investigations

Identity validation workflows directly support investigations but do not represent complete incident case studies on their own.

In operational environments, identity verification tasks documented here are frequently performed during broader response efforts, including credential misuse validation, lateral movement detection, access violation investigation, and identity configuration drift verification.

Full incident lifecycle investigations — including alert context, timeline reconstruction, MITRE ATT&CK mapping, artifact correlation, root cause analysis, and remediation planning — are documented separately in the:

`incident-response-and-investigations` repository

This separation reflects how professional security teams distinguish between identity operations workflows and full investigative case documentation.

---

### Ongoing Development

Workflows in this category are continuously expanded as authentication technologies, identity integrations, directory services, and threat techniques evolve.

Some workflow directories may appear iterative or incomplete. This reflects active development and refinement rather than unfinished work. In production environments, identity systems are frequently reconfigured, integrated with new services, and targeted by evolving attack techniques, requiring ongoing validation and monitoring.

The focus is on building a growing library of reusable identity validation, Active Directory administration, and access control testing techniques rather than static one-time demonstrations.
