# Analyst Notes — Identity and Access Management (IAM) Operations, Authentication, and Permission Validation Using Active Directory

This file captures investigative reasoning, operational observations, and follow-up ideas recorded during execution of the Active Directory IAM workflow.  
These notes reflect how directory-level actions translate into security posture changes and how an analyst would reason about identity-related risk in enterprise environments.

> **Workflow vs Execution vs Writeup (Terminology Used Here)**  
> - **Workflows** refer to operational tasks such as onboarding telemetry and validating parsing behavior.  
> - **Executions** refer to hands-on configuration and validation using real data and Splunk services.  
> - **Writeups** document configuration decisions, troubleshooting steps, and validation results.

---

## Observations During Execution

Working directly inside Active Directory Users and Computers (ADUC) made it clear how much security posture is determined by object placement and inheritance rather than by individual permissions alone. Simply moving users or computers between OUs can drastically change which policies apply to them, even if their group memberships remain unchanged.

The difference between **security groups** and **organizational units (OUs)** became much more concrete during execution. Groups were consistently tied to authorization decisions (what users can access), while OUs controlled policy scope and administrative boundaries. This reinforced that misplacing objects in AD can be just as dangerous as granting excessive group membership.

Delegation of control stood out as one of the highest‑risk configuration areas. Granting Phillip password reset permissions only within the Sales OU demonstrated how narrowly-scoped delegation can support helpdesk workflows without introducing domain-wide risk. At the same time, it highlighted how easy it would be to accidentally over-delegate if OU boundaries are not designed carefully.

Group Policy inheritance behavior was very visible when applying policies at the domain level versus OU level. Linking the Auto Lock Screen GPO at the root domain ensured that all child OUs inherited the policy, while the Control Panel restriction GPO only affected specific user OUs. This showed how small linking decisions can have enterprise-wide impact.

Testing policies by logging in as different users was critical. Verifying that Mark (Marketing OU) was blocked from Control Panel access confirmed that policies were scoped correctly and not unintentionally affecting IT or other administrative users.

---

## Challenges and Troubleshooting

The most significant friction occurred when working with PowerShell AD cmdlets for bulk movement of computer objects. Getting the `-Filter` syntax correct and specifying the correct `SearchBase` distinguished whether commands targeted the intended container or silently returned no results.

Understanding that the default **Computers** container is not an OU was important. Because it does not support Group Policy linking, leaving machines there would prevent proper policy enforcement and complicate administrative delegation.

During delegation testing, Phillip could not access ADUC through the GUI, which at first appeared to be a permissions failure. In reality, the delegated rights allowed specific directory actions but not full console access, requiring PowerShell-based validation. This reinforced that permission scope and tool access are not always equivalent.

GPO application timing was another consideration. While `gpupdate /force` can refresh policies immediately, in production environments policy propagation delays can complicate incident response and configuration validation.

---

## Investigative Reasoning and Administrative Pivots

The workflow progressed from structure → permissions → enforcement → validation intentionally.

First, object placement and OU structure were reviewed to ensure that later delegation and GPO steps would apply to predictable scopes.

Delegation was tested before moving to policy configuration, because improper delegation could undermine later policy enforcement or introduce privilege escalation opportunities.

Device reorganization was performed before applying workstation and server policies so that inheritance would apply correctly once policies were linked.

Authentication protocol review (Kerberos vs NTLM) was placed after configuration steps to reinforce that directory design influences how authentication traffic is generated and logged during investigations.

Trust and forest concepts were reviewed last to contextualize how identity risk scales in enterprise environments, especially in mergers and multi-domain architectures.

---

## Limitations of This Workflow

This walkthrough does not evaluate:

- Windows Security Event Logs related to authentication
- AD auditing and change tracking logs
- Replication latency between domain controllers
- Privileged access workflows using PAM or tiered admin models

As a result, the workflow focuses on configuration state rather than behavioral detection or incident reconstruction.

The environment also does not simulate adversarial abuse of identity features, such as ticket abuse, group nesting exploitation, or delegation misconfiguration attacks.

---

## Ideas for Expansion or Automation

Potential future extensions include:

- Enabling AD auditing and correlating object changes with security logs
- Monitoring delegated accounts for abnormal activity
- Automated scripts to validate OU placement and GPO inheritance
- Detection logic for suspicious group membership changes
- Simulation of Kerberoasting or pass-the-ticket scenarios

These additions would bridge configuration management with active detection engineering.

---

## Cross-Workflow Connections

This workflow strongly supports:

- Windows authentication log analysis workflows
- SIEM detection tuning for identity alerts
- Lateral movement investigations
- Privileged access abuse investigations

Understanding legitimate identity operations makes it easier to detect misuse during security incidents.
