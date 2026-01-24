# Analyst Notes — Process and Network Triage Using Windows CLI (CMD) + PowerShell

This file captures informal observations, investigative reasoning, and follow-up ideas recorded during execution of the native Windows CLI and PowerShell endpoint triage walkthrough.

These notes preserve analytical context that did not belong in the formal workflow execution write-up but reflect how investigative decisions were made and how correlations were formed across evidence sources.

> **Workflow vs Execution vs Writeup (Terminology Used Here)**  
> - **Workflows** refer to operational tasks such as onboarding telemetry and validating parsing behavior.  
> - **Executions** refer to hands-on configuration and validation using real data and Splunk services.  
> - **Writeups** document configuration decisions, troubleshooting steps, and validation results.

---

## Observations During Execution

Working with CMD utilities first helped establish a fast, low-friction way to enumerate system state before switching to PowerShell for deeper inspection. Commands such as `dir`, `type`, and `tasklist` provided immediate visibility without requiring object parsing or script construction.

Seeing multiple instances of `sshd.exe` running at the same time reinforced that not all suspicious-looking processes are necessarily malicious. On servers and developer workstations, multiple service workers can legitimately exist, which makes PID-level analysis critical before taking containment actions.

Using `netstat -abon` provided a surprisingly rich dataset in a single command. Being able to see the listening port, owning PID, and executable name together made it easy to confirm that port 22 exposure was directly tied to the `sshd.exe` process rather than another service masquerading as SSH traffic.

After terminating one SSH process, observing that port 22 remained open under a different PID reinforced that containment actions must be validated and that services may automatically respawn or have multiple instances running by design.

This made it clear that in real investigations, process termination alone is rarely sufficient and must be followed by service configuration review, authentication log inspection, and persistence checks.

---

## Challenges and Troubleshooting

The most important challenge was avoiding overreaction to the presence of `sshd.exe`. Without understanding whether SSH should be running on the host, terminating all instances could have caused legitimate remote access disruption.

Another challenge was keeping track of which PID corresponded to which instance of the process when switching between `tasklist` and `netstat` outputs. Careful note-taking was necessary to avoid confusing multiple PIDs across commands.

Interpreting `netstat` output required careful attention to listening versus established states. A listening service may be legitimate, whereas established external connections might indicate active exploitation or lateral movement.

In PowerShell, ensuring that process correlation used the correct PID field was necessary when pivoting from network connections to owning processes. This reinforced that no single command provides full investigative context.

---

## Investigative Reasoning and Pivots

The workflow intentionally began with filesystem inspection to anchor investigation in observable artifacts rather than assumptions about active compromise.

Once artifacts were confirmed, the next logical pivot was into memory by identifying which executables were currently running. This helps differentiate between dormant files and active services.

Network inspection was used as the deciding factor for whether a running process represented active exposure. A process running without listening ports or external connections may represent staging or benign activity, whereas exposed services require immediate risk assessment.

Containment was performed only after correlating file presence, process execution, and network exposure. This layered confirmation reduces the risk of disrupting benign system functions.

Post-containment validation was treated as a mandatory investigative step rather than optional confirmation. Observing unchanged network exposure after killing a single PID helped demonstrate why analysts must verify state rather than assume success.

---

## Limitations of This Walkthrough

This walkthrough does not evaluate:

- Windows Event Logs for authentication or service installation
- Registry persistence mechanisms
- Scheduled task creation
- Service configuration files or startup parameters

As a result, it should be considered triage and exposure validation, not full compromise assessment.

The workflow also does not evaluate whether SSH access was authorized or whether credentials were abused, which would require log review and identity telemetry.

---

## Ideas for Expansion or Automation

Future enhancements could include:

- PowerShell scripts that correlate processes, services, and ports into structured output tables.
- Automated checks for services running from user-writable directories.
- Event log queries for SSH authentication attempts and failures.
- Scheduled task enumeration to validate persistence mechanisms.
- Exporting triage results to JSON for centralized incident tracking.

These additions would move the workflow closer to an automated response toolkit rather than manual investigation steps.

---

## Cross-Workflow Connections

This workflow directly complements:

- PowerShell-only endpoint triage workflows focusing on object pipelines
- Linux endpoint triage using Bash and core utilities
- SIEM workflows analyzing authentication and remote access telemetry

Across all of these workflows, the same analytical pattern appears: establish baseline, identify anomalies, correlate across telemetry layers, and validate containment actions.
