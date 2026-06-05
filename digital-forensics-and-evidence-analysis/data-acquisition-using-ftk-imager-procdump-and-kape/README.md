# Data Acquisition Using FTK Imager, ProcDump, and KAPE

### Overview

This execution documents the acquisition of multiple forms of digital evidence using FTK Imager, ProcDump, and KAPE. The workflow focused on three primary acquisition objectives: capturing physical and process memory, creating a forensic disk image, and collecting browser artifacts from a remote host. Evidence acquired during the process included physical system memory (RAM), process-specific memory, physical disk storage, Chrome browser artifacts, and Chrome extension artifacts.

Rather than focusing on evidence analysis, the primary objective of the workflow was to preserve evidence for future examination while maintaining forensic integrity. The activities performed throughout the acquisition process reinforced several foundational DFIR concepts, including volatile evidence preservation, process memory acquisition, forensic imaging, evidence integrity validation, remote evidence collection, targeted artifact acquisition, and browser forensics. Together, these techniques demonstrate how investigators can collect and preserve diverse evidence sources for subsequent forensic analysis and incident response activities.

> **Click the ▶ arrow to expand or collapse hidden sections and view additional information.**

<details>
<summary><strong>▶ Recommended Reading Order</strong><br>
</summary><br>
  
> 👉 **Follow the execution walkthrough first**</br>
> Begin with `workflow-execution.md` to review the full acquisition process step-by-step.

> 👉 **Review analytical reasoning and conceptual notes**</br>
> Review `analyst-notes.md` for acquisition concepts, observations, and learning notes recorded during execution.

> 👉 **Review tooling and feature usage details**</br>
> Review `tool-usage-notes.md` for tool-specific explanations covering FTK Imager, ProcDump, PowerShell, RDP, and KAPE.

> 👉 **See what each execution file contains in full detail**</br>
> Review the **Repository Structure & Supporting Documents** section below.

</details>

<details>
<summary><strong>▶ Workflow Scope & Terminology</strong><br>
</summary><br>

- **Category:** Digital Forensics and Incident Response
- **Primary Operational Focus:** Memory acquisition, process memory acquisition, forensic disk imaging, and targeted artifact collection
- **Operational Objectives Demonstrated:** Volatile Memory Acquisition, Process Dump Acquisition, Disk Imaging, Remote Artifact Collection, Evidence Preservation, DFIR Fundamentals
- **Primary Data Sources:** Physical memory, running processes, physical storage devices, and Chrome browser artifacts

> **Terminology used:**
> **Workflows** refer to repeatable digital forensic acquisition processes.
> **Executions** refer to the hands-on collection of evidence using FTK Imager, ProcDump, PowerShell, RDP, and KAPE.
> **Writeups** document how evidence was acquired, why each acquisition method matters, and how the collected data supports later forensic analysis.

</details>

---

### How to Navigate This Current Folder

Documentation is separated into focused components to reflect how digital forensic acquisition workflows are commonly documented.

`workflow-execution.md` — If you want to follow the investigation step by step</br>
Documents the complete acquisition workflow, including memory acquisition, process memory acquisition, disk imaging, and remote artifact collection.

`analyst-notes.md` — If you want to understand the reasoning behind the process</br>
Documents observations, lessons learned, acquisition concepts, and reasoning behind each acquisition method.

`tool-usage-notes.md` — If you want to understand the reasoning behind the process</br>
Documents how each tool was used and explains the operational relevance of FTK Imager, ProcDump, PowerShell, RDP, and KAPE.

---

### Repository Structure & Supporting Documents

| File / Folder | Purpose | Contents and Focus |
|-------------|--------|--------------------|
| `workflow-execution.md` | Structured acquisition walkthrough. | Documents memory acquisition, process dumping, disk imaging, artifact collection, screenshots, and analyst observations. |
| `images/` | Visual evidence supporting workflow execution. | Contains screenshots showing FTK Imager, ProcDump, PowerShell, RDP, and KAPE activities. |
| `README.md` | Execution overview and operational summary. | Provides workflow objectives, navigation guidance, environment details, and forensic relevance. |
| `analyst-notes.md` | Analyst observations and learning notes. | Documents acquisition concepts, reasoning, and lessons learned during execution. |
| `tool-usage-notes.md` | Tool-focused reference document. | Explains how each acquisition tool was used and why it matters in DFIR workflows. |

---

### Environment, Data Sources, and Tools

<details>
<summary><strong>▶ Environment and Execution Scope (At a Glance)</strong><br>
</summary><br>

| Area | Details |
|--------|---------|
| **Environment Type** | Windows-based forensic acquisition lab |
| **Execution Platform** | Windows |
| **Primary Tools** | FTK Imager, ProcDump, PowerShell, RDP, KAPE |
| **Operational Focus** | Evidence acquisition and preservation |
| **Workflow Scope** | Memory acquisition, process acquisition, disk imaging, artifact collection |

</details>

<details>
<summary><strong>▶ Data Sources, Evidence, and Analysis Techniques</strong><br>
</summary><br>

| Area | Details |
|--------|---------|
| **Memory Source** | Live system RAM |
| **Process Source** | Calculator process (demonstration target) |
| **Disk Source** | Secondary physical drive |
| **Artifact Source** | Remote Windows host C: drive |
| **Acquisition Techniques** | Memory imaging, process dumping, disk imaging, browser artifact collection |
| **Artifact Categories** | Chrome history, cookies, sessions, extensions, browser metadata |
| **Operational Workflow Context** | Demonstrates evidence preservation prior to forensic analysis |

</details>

---

### Intended Use

This execution is intended to demonstrate practical evidence acquisition techniques used in digital forensics and incident response. The workflow reflects how investigators may collect evidence before beginning deeper analysis.

<details>
<summary><strong>▶ Investigative Questions Addressed</strong><br>
</summary><br>

- What evidence should be preserved first?
- How can volatile memory be captured?
- How can a suspicious process be preserved for later analysis?
- How can a storage device be acquired without examining the original media directly?
- How can browser artifacts be collected from a remote endpoint?

The workflow supports later activities such as:

- memory forensics,
- malware analysis,
- browser forensics,
- user activity reconstruction,
- incident response investigations,
- evidence preservation and validation.

</details>

---

### Relevance to Security Operations and Digital Forensics

Evidence acquisition is one of the earliest and most important stages of a forensic investigation. Before investigators can analyze activity, identify malware, reconstruct timelines, or review user behavior, they must first preserve evidence. The workflow also demonstrates how different evidence sources complement one another. Memory may contain evidence unavailable on disk, disk images preserve storage contents, and targeted artifact collection enables rapid triage from remote systems.

<details>
<summary><strong>▶ Analyst Use Cases</strong><br>
</summary><br>

- volatile memory acquisition,
- process memory acquisition,
- forensic disk imaging,
- targeted artifact collection.

These methods are commonly used during:

- incident response investigations,
- malware triage,
- endpoint investigations,
- evidence preservation activities,
- forensic examinations.

</details>

---

If you are reviewing this as part of my cybersecurity portfolio: this execution is intended to demonstrate practical digital forensic acquisition methodology, evidence preservation concepts, and documentation practices aligned with entry-level DFIR, SOC, and incident response workflows.
