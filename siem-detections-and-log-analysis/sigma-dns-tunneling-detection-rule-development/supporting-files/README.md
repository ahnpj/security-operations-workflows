# Supporting Files

### Overview

This folder contains project-specific detection artifacts created and reviewed during the Sigma rule development workflow. These files support the detection engineering process documented throughout the project and provide the actual detection content referenced during analysis and validation activities.

The primary artifact in this folder is a Sigma rule designed to detect DNS tunneling activity associated with the fictional TRANSPORTER malware family. The rule was developed by modifying an existing DNS command-and-control detection using threat intelligence indicators and updated detection criteria.

---

### Contents

| File                                      | Purpose                                                                                                                        |
| ----------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------ |
| `transporter-dns-tunneling-detection.yml` | Final Sigma rule developed during the workflow.                                                                                |
| Additional `.yml` files *(when present)*  | Supporting Sigma rules, alternate detection versions, testing variations, or reference detection content used during analysis. |

---

### About Sigma Rules

Sigma is a vendor-agnostic detection rule format that allows analysts to define detection logic independently of any specific SIEM platform. Sigma rules are written in YAML (`.yml`) format and can later be converted into platform-specific queries for tools such as Splunk, Microsoft Sentinel, Elastic, QRadar, and other supported SIEM solutions.

A Sigma rule typically contains:

* Detection metadata
* Log source requirements
* Detection logic and conditions
* False positive considerations
* Severity classifications
* MITRE ATT&CK mappings

---

### Detection Development Context

The Sigma rule contained in this folder was developed as part of a threat-informed detection engineering exercise focused on DNS-based command-and-control activity.

The workflow involved:

1. Reviewing an existing DNS tunneling detection.
2. Analyzing threat intelligence related to TRANSPORTER malware.
3. Identifying detection gaps within the original rule.
4. Modifying detection criteria and indicators.
5. Validating that the completed rule aligned with observed threat behavior.

The resulting detection content demonstrates how threat intelligence can be translated into practical monitoring logic that supports SIEM-based threat detection workflows.

---

### Relationship to the Workflow

The files in this folder represent the final detection artifacts referenced throughout:

* `workflow-execution.md`
* `analyst-notes.md`
* `tool-usage-notes.md`

These artifacts serve as the operational output of the detection engineering process documented within the project.

---

### Intended Use

The files contained in this folder are intended for educational, detection engineering, and portfolio demonstration purposes. They demonstrate practical Sigma rule analysis, modification, and validation techniques commonly performed by SOC analysts, detection engineers, and threat detection teams.
