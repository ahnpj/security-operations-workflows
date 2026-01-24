# Analyst Notes — Traffic Filtering, Protocol Dissection, and Session Reconstruction Using Wireshark

This file captures investigative reasoning, interpretation decisions, and workflow-specific observations recorded during offline packet analysis using Wireshark.

These notes focus on *how conclusions were formed* from packet-level evidence and how Wireshark features supported investigative pivots beyond simple packet viewing.

> **Workflow vs Execution vs Writeup (Terminology Used Here)**  
> - **Workflows** refer to operational tasks such as onboarding telemetry and validating parsing behavior.  
> - **Executions** refer to hands-on configuration and validation using real data and Splunk services.  
> - **Writeups** document configuration decisions, troubleshooting steps, and validation results.

---

## Observations During Execution

Switching immediately to offline PCAP analysis reinforced that most real investigations begin with captured traffic rather than live packet streams. Working from a static file made it possible to repeatedly test filters, revisit packets, and validate hypotheses without worrying about missing short-lived sessions.

Breaking down Packet #27 using the OSI model made protocol layering much more concrete. Seeing Ethernet → IP → TCP → HTTP within a single packet clarified how much contextual metadata is available even before payload inspection. This reinforced that analysts can often answer questions about source, destination, and session behavior without needing to inspect content directly.

TCP reassembly fields were especially helpful when reviewing multi-segment HTTP responses. Being able to confirm that packets were part of the same stream prevented misinterpretation of fragmented payloads as separate events.

The image extraction step using Packet #39765 demonstrated how malware payloads, phishing images, and dropped files can all be reconstructed from network traffic. Seeing the reconstructed JPEG match expectations reinforced the importance of validating reassembly before assuming payload integrity.

Hashing the exported JPEG was important not just for verification but also for establishing a repeatable investigation artifact. In real cases, hashes are used for threat intelligence correlation, sandbox submission, and chain-of-custody documentation.

Expert Information alerts initially looked concerning, but reviewing their severity and frequency made it clear that not all warnings indicate malicious behavior. This reinforced that Expert Info should guide triage, not replace analytical judgment.

---

## Challenges and Troubleshooting

One of the main challenges was avoiding over-filtering too early. Applying strict display filters at the beginning risked hiding relevant sessions before traffic patterns were understood. Starting with broad inspection and gradually narrowing scope proved more effective.

Understanding the difference between packet-level views and conversation-level analysis required careful attention. Packet counts alone did not reflect how many sessions or hosts were actually involved, which made Conversation views more useful for behavioral interpretation.

Exporting packet bytes versus exporting HTTP objects required understanding the difference between raw payload extraction and application-layer object reconstruction. Selecting the wrong export method would have produced unusable artifacts.

When reviewing timestamps, it became clear that default display formats are not always suitable for correlation with SIEM logs or firewall telemetry. Adjusting time display formats improved temporal analysis consistency.

---

## Investigative Reasoning and Workflow Design

The workflow followed an intentional analytical progression:

1. Validate capture metadata and integrity
2. Identify protocols present
3. Inspect representative packets using OSI layering
4. Reconstruct sessions and payloads
5. Extract and validate artifacts
6. Apply behavioral and severity analysis

This progression mirrors incident response processes where analysts must first trust the dataset, then understand its structure, and only later draw behavioral conclusions.

Artifact extraction was performed only after confirming packet integrity and reassembly, preventing premature conclusions about file content or malicious intent.

Expert Info was used at the end of analysis rather than the beginning to avoid biasing interpretation before understanding baseline traffic patterns.

---

## Limitations of This Workflow

This walkthrough does not include:

- Encrypted traffic decryption
- Correlation with endpoint telemetry
- Threat intelligence enrichment
- Automated malware detection

As a result, the workflow focuses on visibility and manual validation rather than detection engineering or response automation.

The dataset also does not simulate advanced attacker techniques such as covert channels or encrypted command-and-control traffic.

---

## Ideas for Expansion or Automation

Future enhancements could include:

- Integrating Zeek logs for protocol-level enrichment
- Automated extraction of transferred files for sandbox analysis
- Building Wireshark profiles optimized for SOC triage
- Time-based correlation with firewall or proxy logs
- Applying display filters that reflect common detection signatures

These additions would connect packet-level analysis to enterprise-scale detection workflows.

---

## Cross-Workflow Connections

This workflow directly supports:

- tcpdump-based capture workflows
- VPN traffic validation workflows
- SIEM alert validation exercises
- Malware delivery investigations

Understanding how artifacts traverse the network improves confidence when interpreting alerts generated by other security tools.
