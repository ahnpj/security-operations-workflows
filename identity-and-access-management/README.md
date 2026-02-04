# Identity & Access Management (IAM)

(`identity-and-access-management/README.md`)

This folder contains workflow executions where the **primary focus is validating authentication behavior, access controls, and permission enforcement across identity systems**. These workflows simulate how SOC analysts and security engineers verify identity service behavior and investigate suspicious or unexpected authentication and authorization activity using repeatable, tool-driven methods.

Rather than presenting abstract identity concepts, each folder documents practical execution of identity validation and access control testing activities, including commands and queries used, analyst reasoning behind each verification step, and interpretation of results within an investigative context. The emphasis is on operational process and analyst decision-making, similar to internal SOC runbooks and identity operations working notes.

Each folder uses a tool-first naming convention followed by the primary operational focus (for example, `active-directory-iam-operations-and-permission-validation`). This allows quick identification of technologies being used while keeping documentation centered on investigative workflow and analytical reasoning rather than directory service theory alone.

> 👉 **Each folder represents one complete workflow execution**  
Every subfolder here is a **fully self-contained operational execution scenario**. Each one documents how a specific identity validation or access control verification task is performed from initial objective through validation and operational interpretation.

> 👉 **Follow the workflow execution first**  
Begin with `workflow-execution.md` inside a workflow folder to see how authentication activity, directory objects, and permission paths were analyzed and validated step by step using commands, queries, and investigative methodology.

> **Terminology used in this category:**  
> **Workflows** refer to common security operations tasks (such as authentication validation or permission testing).  
> **Executions** refer to the hands-on performance of those tasks using real tools, logs, and directory objects.  
> **Writeups** document how the task was performed and how results were interpreted.

---

### How Workflows Are Categorized

Workflows are grouped here when **authentication validation, identity behavior analysis, and access control verification are the primary operational objectives**, rather than alert triage or full incident reconstruction.

Although these activities frequently support investigations and incident response, they are categorized separately because their primary purpose is verifying identity security posture, authentication behavior, and permission enforcement before deeper investigative or containment actions occur.

Workflows in this category typically focus on:

- **Authentication log analysis**, reviewing successful and failed login activity to identify abnormal access patterns, unusual login sources, or repeated failures indicating credential misuse or brute-force attempts.

- **Account and group membership validation**, inspecting user, service account, and administrative group assignments to confirm privilege levels align with organizational role expectations and policy requirements.

- **Permission and access path testing**, verifying which systems, shares, or services specific accounts can access to identify excessive privileges or unintended exposure.

- **Directory object inspection**, reviewing attributes of users, computers, and service accounts to detect misconfigurations, privilege escalation risks, or indicators of compromise.

- **Policy and control verification**, validating authentication and authorization configurations to ensure identity protections are enforced consistently across environments.

Although these workflows support investigations, they are organized here when the **core work involves validating identity behavior and access boundaries rather than reconstructing full incident timelines.**

---

### Operational Context and Purpose

Identity validation and access control workflows typically occur during alert investigation, insider threat analysis, lateral movement detection, or routine security control validation.

At this stage of security operations, the objective is to determine whether observed authentication or authorization behavior represents legitimate usage, misconfiguration, or malicious activity. Analysts focus on understanding how accounts authenticate, what resources they can access, and whether those access paths align with organizational policy.

The execution examples in this category reflect real SOC and identity security operations tasks where targeted validation helps determine whether escalation, containment, or remediation is required.

---

### What’s in This Folder

Each workflow execution is contained in its **own dedicated folder** and represents **one complete operational identity validation scenario**, including execution walkthroughs, analytical reasoning, validation outcomes, and tooling references.

Current workflow executions include:

- **Identity and Access Management (IAM) Operations, Authentication, and Permission Validation Using Active Directory**  
  (`active-directory-iam-operations-and-permission-validation`)  
  Focuses on validating user authentication events, group memberships, and effective permissions within an Active Directory environment. Tasks include reviewing logon activity, inspecting group assignments, and testing access to protected resources to identify excessive privileges or configuration weaknesses.

Additional workflow executions will be added as identity coverage expands across authentication methods, directory services, and access control scenarios.

---

### Workflow Documentation Structure

Each workflow execution is fully self-contained and uses documentation aligned with how identity validation and access control tasks are performed in operational SOC environments.

| File / Folder | Purpose | Contents and Focus |
|-------------|-------------|--------------------|
| **`workflow-execution.md`** | Operational execution walkthrough | Step-by-step identity validation workflow showing commands, queries, permission checks, investigative pivots, and validation checkpoints |
| **`README.md`** | Workflow context and operational objective | Describes the authentication or access control validation objective, workflow scope, and how the execution supports detection and investigation workflows |
| **`analyst-notes.md`** | Analytical reasoning and operational considerations | Documents why specific identity validation techniques were selected and how authentication and access behavior supports investigative conclusions |
| **`tool-usage-notes.md`** | Tool and query reference documentation | Explains directory service tools, command syntax, query logic, and permission inspection techniques applicable to similar identity investigations |
| **`automation-design-notes.md`** *(when present)* | Automation and scalability planning | Documents scripted identity validation approaches, bulk permission analysis techniques, and design considerations for scaling or integrating identity checks into detection or monitoring pipelines |
| **`images/` or `screenshots/`** | Validation and evidence artifacts | Contains authentication logs, directory views, permission validation results, and visual confirmation of identity verification steps |

Together, these files separate **identity validation execution**, **analytical reasoning**, **tool reference**, and **automation planning** into clearly reviewable components while remaining tied to the same operational workflow.

---

### How These Workflow Executions Are Designed

This category is **execution-focused**, not theoretical.

You will find:

- Realistic authentication and access validation walkthroughs
- Directory inspection and permission testing techniques
- Validation checkpoints and investigative interpretation of authentication behavior
- Detection support and identity monitoring improvements tied to validation outcomes
- Operational decision-making behind identity verification methodology and investigative pivots

Each workflow demonstrates not just how identity validation is performed, but why access boundary testing and authentication monitoring are critical for early detection of credential abuse, privilege escalation, and policy violations.

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

The focus is on building a growing library of reusable identity validation and access control testing techniques rather than static one-time demonstrations.

