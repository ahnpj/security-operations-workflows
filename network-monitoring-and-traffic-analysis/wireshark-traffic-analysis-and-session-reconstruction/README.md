# Traffic Filtering, Protocol Dissection, and Session Reconstruction Using Wireshark — Operational Execution

**Category:** Network Traffic Analysis  
**Operational Focus:** Protocol inspection, OSI-layer dissection, session reconstruction, and evidence extraction from packet captures  
**Primary Tools & Platforms:** Wireshark, stored PCAP and PCAPNG capture files

This directory contains a complete, operationally structured record of packet analysis performed using Wireshark on stored capture files. The directory contains the execution writeups, screenshots, and extracted artifacts that together demonstrate how analysts move from raw packet data to protocol-level understanding and session reconstruction.

The goal of the execution is not to perform live traffic interception, but to practice how captured network evidence is interpreted after collection, which is how packet analysis is most often performed during investigations.

At a practical level, the execution reflects how analysts examine packet data:

- First, capture files are loaded and interface context is reviewed.  
- Next, individual packets are dissected across OSI layers to understand protocol behavior.  
- Then, filters and conversation tracking are used to isolate sessions.  
- Finally, transferred objects and full streams are reconstructed for validation.

Throughout this execution, I intentionally focused on understanding how decoded fields map to raw bytes and how session context emerges from individual packets. This reinforced how protocol analysis supports both security investigations and troubleshooting.

---

## How to Read This Folder

This folder separates packet analysis execution from supporting protocol reference material.

> **Workflow vs Execution vs Writeup (Terminology Used Here)**  
> - **Workflows** refer to tasks such as session reconstruction and protocol validation.  
> - **Executions** refer to hands-on packet inspection using protocol analyzers.  
> - **Writeups** document analysis steps and interpretation decisions.

- Detailed packet analysis steps are in **`workflow-execution.md`**.  
- This `README.md` explains analytical goals and methodology.  
- Supporting files provide protocol and tool usage references.

---

## What This Execution Is Specifically Doing

This execution demonstrates how to interpret packet captures using Wireshark in a structured and repeatable way.

First, capture files are opened and file properties are reviewed to confirm capture context and integrity metadata.

Next, representative packets are selected and dissected across OSI layers, with attention paid to how Ethernet, IP, TCP, and application protocols are represented in both decoded fields and raw bytes.

Then, filtering techniques are applied to isolate relevant conversations using display filters, conversation filters, and stream-following features.

After isolating sessions, full request and response content is reconstructed using TCP stream reassembly to view application-layer transactions.

Finally, transferred objects are extracted and hashed to validate integrity and support downstream analysis.

These steps reflect how analysts reconstruct communication behavior from packet-level evidence.

---

## Why These Tasks Matter in Network Security Operations

Packet captures provide the most detailed view of network behavior, but they are only useful if analysts can interpret them correctly.

SOC analysts and incident responders rely on packet analysis to:

- validate IDS alerts,  
- confirm malware command-and-control behavior, and  
- reconstruct attacker activity during breaches.

Understanding protocol behavior across OSI layers is critical for accurate interpretation and defensible reporting.

---

## Environment, Operating System, and Tooling

### Execution Environment

All analysis was performed using offline capture files rather than live traffic interception. This reflects typical investigation workflows where packet evidence is collected elsewhere and analyzed later.

### Operating System and Interface

Wireshark was used in a desktop environment with full GUI support for protocol visualization and stream reconstruction.

### Tooling Used

The execution relied exclusively on Wireshark’s native features including:

- packet dissection panes,  
- display filters,  
- conversation tracking, and  
- object extraction tools.

No external protocol decoding tools were required.

---

## Data Sources and Evidence Types

Evidence analyzed during the execution included:

- PCAP and PCAPNG packet capture files,  
- reconstructed TCP streams, and  
- extracted application-layer objects.

These artifacts represent the same data types used in real incident investigations.

---

## Operational Relevance to SOC and Detection Engineering Workflows

This execution reflects how SOC analysts and incident responders analyze packet captures to confirm attacker behavior, validate IDS alerts, and reconstruct network sessions during investigations.

Packet analysis is commonly used to verify malware command-and-control activity, data exfiltration attempts, unauthorized service exposure, and exploitation attempts that are not fully visible in higher-level logs.

The techniques demonstrated here mirror how analysts isolate conversations, reconstruct full application-layer sessions, and extract transferred objects to validate whether suspicious activity represents benign misconfiguration or active compromise.

From a detection engineering perspective, packet analysis is frequently used to validate detection signatures and anomaly models. Detection engineers often review packet-level evidence to confirm that alerts correspond to meaningful protocol behavior rather than benign noise.

This execution also supports forensic workflows where analysts reconstruct timelines of attacker activity using network evidence collected from sensors, firewalls, or endpoint capture tools.

Overall, this workflow demonstrates how packet-level visibility supports confirmation of intrusion activity and refinement of network-based detection strategies.


---

## Files and Documentation Structure

This execution is documented using multiple files to reflect how packet analysis workflows are separated into evidence review, analytical reasoning, and protocol reference material in network forensic investigations.

### `README.md`

Provides analytical scope, explains what types of protocol behavior are being examined, and describes how packet analysis supports SOC and incident response workflows.

### `workflow-execution.md`

Contains step-by-step packet dissection procedures, applied display filters, conversation isolation, session reconstruction steps, and object extraction activities. This file documents how raw packets were translated into interpretable communication behavior.

### `analyst-notes.md`

Documents interpretation of protocol behavior, identification of suspicious patterns, and conclusions about session activity that would inform escalation or containment decisions.

### `tool-usage-notes.md`

Provides technical reference material for Wireshark features including display filters, stream following, protocol panes, and object extraction functionality.

### `images/`

Contains screenshots validating protocol dissection, reconstructed sessions, and extracted artifacts to support reproducibility and review.


---

## Skill Demonstration Context

This execution demonstrates practical network forensic analysis skills including protocol dissection, session reconstruction, evidence extraction, and structured investigative documentation.

Evidence collection using tcpdump is demonstrated separately in the targeted capture execution within this category.
