# Analyst Notes — Windows Endpoint Triage and System Inspection Using PowerShell

This file captures informal observations, investigative reasoning, and follow-up ideas recorded during execution of the PowerShell-based endpoint triage walkthrough.

These notes preserve analytical context that does not belong in the formal workflow documentation but reflects how investigative thinking evolved during execution.

> **Workflow vs Execution vs Writeup (Terminology Used Here)**  
> - **Workflows** refer to operational tasks such as onboarding telemetry and validating parsing behavior.  
> - **Executions** refer to hands-on configuration and validation using real data and Splunk services.  
> - **Writeups** document configuration decisions, troubleshooting steps, and validation results.

---

### Observations During Execution

One of the most noticeable differences compared to Bash-based workflows is how PowerShell object pipelines dramatically simplify correlation across evidence types. Sorting and filtering on properties like `Length`, `Name`, `OwningProcess`, and `DisplayName` allowed me to pivot between filesystem, process, and network evidence without re-parsing text output.

Using `Get-Help` and `Get-Command` early proved extremely useful. In realistic response scenarios, analysts are rarely operating from memory alone, and built‑in discovery tools reduce reliance on external documentation that may not be accessible during incidents.

I noticed that some cmdlets, such as `Get-ComputerInfo`, return extremely large datasets. While useful for baselining, the volume of output reinforces the importance of scoping and filtering when collecting evidence to avoid overwhelming case notes or SIEM ingestion pipelines.

Service DisplayNames stood out as a surprisingly useful investigative surface. Even when service binaries are legitimate, altered DisplayNames can indicate attacker attempts to camouflage persistence mechanisms, which is easy to miss if analysts only review service names.

---

### Challenges and Troubleshooting

The most consistent friction point was remembering that PowerShell returns objects, not plain text. Early filters that assumed string output did not behave as expected until I adjusted queries to reference explicit properties.

Some recursive filesystem searches took noticeably longer than expected when executed from high-level directories. This reinforced that broad recursion should be avoided during live response unless necessary, and that starting from user‑writable locations is often more efficient.

During remote execution testing with `Invoke-Command`, it became clear that remoting depends heavily on environment configuration. Firewall rules, WinRM configuration, and authentication context can all prevent commands from executing remotely even when syntax is correct.

When inspecting network connections, understanding that `Get-NetTCPConnection` returns PIDs rather than process names required an additional pivot back into `Get-Process` or CIM queries. This reinforced the need to think in terms of correlations rather than expecting single commands to answer all questions.

---

### Investigative Reasoning and Pivots

The workflow intentionally progressed from static discovery into runtime inspection. Filesystem enumeration established what artifacts existed on disk, which then informed integrity checks using `Get-FileHash`.

Network inspection did not occur in isolation. Instead, connections were treated as investigative leads that must be correlated back to owning processes using PID relationships.

Service inspection was performed after artifact discovery and network review to validate whether persistence mechanisms were present that aligned with earlier observations.

Remote execution was placed at the end of the workflow to reflect how triage often begins locally and later expands to enterprise‑wide validation once suspicious behavior is confirmed on an initial host.

---

### Limitations of This Walkthrough

This walkthrough does not include:

- Memory analysis
- Event log analysis
- Registry autorun deep inspection
- Driver or kernel module inspection

As a result, this workflow should be treated as triage and scoping rather than full forensic validation.

Additionally, no EDR telemetry was available, which limits behavioral correlation over time.

---

### Ideas for Expansion or Automation

Future enhancements could include:

- Automated collection scripts that gather process, service, network, and file metadata into structured JSON for centralized review.
- Registry-based persistence checks (Run keys, scheduled tasks, WMI subscriptions).
- Integration with hash reputation services for automated file validation.
- Cross‑host comparison scripts to identify anomalies relative to peer systems.

These additions would move this workflow closer to full response automation.

---

### Cross-Workflow Connections

This workflow directly supports and complements:

- Linux endpoint triage workflows using Bash and core utilities
- Python-based log parsing and automation workflows
- SIEM-based detection validation workflows

The same investigative flow — enumerate, validate, pivot, and correlate — is repeated across platforms, reinforcing consistent analytical methodology.

