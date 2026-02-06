# Tool Usage Notes — Traffic Filtering, Protocol Dissection, and Session Reconstruction Using Wireshark

This document explains how Wireshark was used as a protocol-aware analysis platform to move from raw packet captures to session-level and object-level understanding of network activity. The emphasis is not on capturing traffic, but on validating, dissecting, and reconstructing traffic that has already been collected as evidence.

Wireshark is used here as a complementary tool to command-line capture utilities such as tcpdump. Tcpdump provides efficient telemetry collection, while Wireshark provides structured protocol decoding, visual navigation, and artifact extraction capabilities that are critical during deeper investigation and forensic validation.

> **Workflow vs Execution vs Writeup (Terminology Used Here)**  
> - **Workflows** refer to operational security tasks such as packet-level investigation, protocol validation, and session reconstruction.  
> - **Executions** refer to the hands-on performance of those tasks using packet captures, graphical protocol analyzers, and analyst navigation features.  
> - **Writeups** document how packets were interpreted, how filters and reconstructions were applied, and how evidence was extracted and validated.

---

### Execution Platform and Operating Environment

#### ▶ Analyst Workstation Performing Offline Packet Analysis

**Purpose:** Provide a controlled environment for protocol inspection without relying on live network capture.  
**How It Was Used:** All analysis was performed using stored PCAPNG files loaded directly into Wireshark. No live capture interfaces were used for investigative steps.  
**Operational Relevance:** Reflects common SOC and IR workflows where analysts receive packet evidence from sensors, firewalls, or cloud taps rather than capturing traffic themselves.

Environment characteristics:

- **Operating System:** Desktop operating system capable of running Wireshark GUI  
- **Analysis Mode:** Offline analysis only (pre-captured files)  
- **Primary Tool:** Wireshark graphical protocol analyzer  
- **Interface Dependency:** None during analysis phase  
- **User Context:** Standard desktop user session

This mode ensures that all investigative actions are repeatable and that results do not depend on transient network conditions.

---

### Capture File Handling and Evidence Validation

Before packet-level analysis begins, analysts must validate capture context and file integrity.

#### ▶ Capture File Loading and Verification

**Purpose:** Confirm that packet data loads correctly and that protocol dissection is functional.  
**How It Was Used:** PCAPNG files were opened using drag-and-drop and via the File menu to validate both loading methods.  
**Operational Relevance:** Analysts frequently receive capture files from multiple sources and must ensure they can be parsed before investing time in analysis.

#### ▶ Capture File Properties Review

Menu Path: **Statistics → Capture File Properties**

**Purpose:** Review metadata such as file size, packet count, capture duration, encapsulation type, and cryptographic hash values.  
**How It Was Used:** Validate that capture duration and packet volume were reasonable for the investigative scope and that file hashes were available for evidence handling.  
**Operational Relevance:** Hash values support chain-of-custody documentation and evidence sharing between teams during investigations.

Capture metadata also provides context about whether traffic gaps or truncation may affect interpretation.

#### ▶ Merging Multiple Capture Files

Menu Path: **File → Merge**

**Purpose:** Combine multiple PCAP files into a single timeline.  
**How It Was Used:** A second capture file was merged into the primary capture to observe how Wireshark handles multi-source or segmented evidence.  
**Operational Relevance:** In real incidents, traffic may be collected from multiple sensors or over different time windows and must be analyzed as a unified dataset.

Merged timelines support cross-system correlation and complete session reconstruction.

---

### Interface Navigation and Visual Analysis Controls

Wireshark’s three-pane model is foundational to packet-level investigation.

#### ▶ Packet List Pane (Summary View)

**Purpose:** Display high-level per-packet metadata including timestamps, addresses, protocol, and packet length.  
**How It Was Used:** Identify protocol distribution, locate specific packet numbers, and detect visible anomalies such as unusual packet sizes or protocol shifts.  
**Operational Relevance:** Serves as the primary navigation layer during large-capture triage.

Custom columns can be added here to expose protocol fields directly in summary view.

#### ▶ Packet Details Pane (Protocol Dissection)

**Purpose:** Decode each packet into hierarchical protocol layers and fields.  
**How It Was Used:** Inspect Ethernet, IP, TCP, and HTTP headers and validate how fields map to OSI layers.  
**Operational Relevance:** Enables analysts to verify flags, ports, sequence numbers, and protocol semantics without manual decoding.

Selecting a field automatically highlights the corresponding bytes in the raw data pane.

#### ▶ Packet Bytes Pane (Raw Evidence)

**Purpose:** Display captured bytes in hexadecimal and ASCII.  
**How It Was Used:** Validate that decoded protocol fields align with raw byte offsets and inspect payload contents.  
**Operational Relevance:** Supports forensic validation and detection of malformed or manipulated packets.

Raw byte inspection is critical when validating exploit payloads or protocol abuse.

---

### OSI Layer and Protocol Dissection Workflow

#### ▶ Frame Metadata (Physical Layer Context)

**Purpose:** Review capture-level metadata including frame length, arrival time, and encapsulation.  
**Operational Relevance:** Provides timing context and validates whether packets were fully captured.

Frame metadata is critical when analyzing packet loss or timing anomalies.

#### ▶ Ethernet II Header (Data Link Layer)

**Purpose:** Identify source and destination MAC addresses and encapsulated protocol type.  
**Operational Relevance:** Supports lateral movement analysis and local segment attribution.

MAC analysis becomes important in investigations involving spoofing or rogue devices.

#### ▶ IP Header (Network Layer)

**Purpose:** Identify logical addressing, TTL, fragmentation, and transport protocol selection.  
**Operational Relevance:** Supports routing path inference and detection of scanning or spoofed traffic.

TTL anomalies can indicate routing loops or crafted packets.

#### ▶ TCP Header (Transport Layer)

**Purpose:** Analyze ports, flags, sequence numbers, acknowledgment behavior, and window sizes.  
**Operational Relevance:** Supports detection of scanning, exploitation attempts, and abnormal session behavior.

TCP reassembly enables reconstruction of full application payloads.

#### ▶ Application Protocols (Session, Presentation, Application Layers)

**Purpose:** Decode protocol semantics such as HTTP methods, headers, and payloads.  
**Operational Relevance:** Enables detection of malware delivery, credential transmission, and data exfiltration.

Unencrypted protocols allow full payload inspection and artifact extraction.

---

### Filtering and Dataset Reduction Techniques

Filtering enables hypothesis-driven analysis by isolating only traffic relevant to investigative questions.

#### ▶ Apply as Filter

**Purpose:** Generate and immediately apply display filters from selected packet fields.  
**How It Was Used:** Isolate traffic from specific source or destination IP addresses.  
**Operational Relevance:** Enables rapid narrowing of large datasets without manual filter construction.

#### ▶ Prepare as Filter

**Purpose:** Construct filter expressions without applying them immediately.  
**How It Was Used:** Build compound filters using AND, OR, and NOT logic before execution.  
**Operational Relevance:** Prevents accidental loss of visibility during filter experimentation.

This approach supports careful hypothesis testing during investigations.

#### ▶ Conversation Filters

**Purpose:** Isolate all packets belonging to a specific session.  
**How It Was Used:** Reconstruct full TCP conversations between two endpoints.  
**Operational Relevance:** Enables behavioral analysis of complete interactions rather than isolated packets.

Conversation filters are essential for reconstructing request-response flows.

#### ▶ Colorize Conversation

**Purpose:** Highlight packets belonging to a session without hiding unrelated traffic.  
**How It Was Used:** Visually trace conversations while maintaining full dataset context.  
**Operational Relevance:** Supports multi-session analysis in busy captures.

This is useful when comparing multiple flows simultaneously.

---

### Navigation, Annotation, and Evidence Management

#### ▶ Packet Number Navigation

**Purpose:** Use packet numbers as deterministic reference points.  
**How It Was Used:** Jump directly to specific packets using the Go menu.  
**Operational Relevance:** Supports collaboration when analysts reference specific packet IDs.

#### ▶ Content-Based Search

Menu Path: **Edit → Find Packet**

**Purpose:** Locate packets by string, hex, or regex patterns.  
**Operational Relevance:** Enables detection of known IOCs, file signatures, or protocol errors.

Search scope selection is critical to avoid false negatives.

#### ▶  Marking Packets

**Purpose:** Temporarily flag packets of interest.  
**Operational Relevance:** Supports quick visual tracking during live analysis sessions.

Marks are session-only and are cleared when files are closed.

#### ▶ Packet Comments

**Purpose:** Attach persistent analyst notes directly to packets.  
**Operational Relevance:** Enables collaboration and long-term case documentation.

Comments are saved within the capture file.

---

### Artifact Extraction and File Reconstruction

#### ▶ Export Objects

Menu Path: **File → Export Objects → [Protocol]**

**Purpose:** Extract reconstructed files transferred over supported protocols.  
**Operational Relevance:** Supports malware recovery, phishing analysis, and data exfiltration investigations.

Protocol support depends on Wireshark dissector capabilities.

#### ▶ Export Packet Bytes

**Purpose:** Extract raw payload bytes from individual packets or streams.  
**Operational Relevance:** Enables manual carving and custom file reconstruction when automatic object extraction is unavailable.

#### ▶ File Integrity Validation

**Purpose:** Confirm exported artifact integrity using cryptographic hashes.  
**Operational Relevance:** Supports malware analysis pipelines and IOC sharing.

Hash verification ensures artifacts remain unchanged during handling.

---

### Session Reconstruction and Stream Analysis

#### ▶ Follow TCP Stream

**Purpose:** Reconstruct full application-layer conversations.  
**Operational Relevance:** Enables readable inspection of unencrypted sessions such as HTTP and FTP.

Follow Stream automatically applies display filters and can be cleared afterward.

---

### Expert Information and Automated Anomaly Surfacing

Menu Path: **Analyze → Expert Information**

**Purpose:** Surface protocol warnings, retransmissions, malformed packets, and sequence anomalies.  
**Operational Relevance:** Supports triage prioritization but must be interpreted in baseline context.

Severity categories include Chat, Note, Warning, and Error.

---

### Performance and Evidence Handling Considerations

- Avoid unnecessary full-dataset scans during time-sensitive investigations.  
- Preserve original capture files before exporting subsets.  
- Document filter logic and extraction methods for auditability.  
- Avoid editing original evidence files when possible.

These practices support defensible investigation workflows.

---

### Detection and Monitoring Relevance

Packet-level analysis supports detection engineering by validating:

- IDS signature triggers  
- Network anomaly models  
- Firewall and proxy log correlations  
- Zeek and Suricata behavioral telemetry

Wireshark often serves as the ground-truth reference during detection tuning.

---

### Summary of Tools, Platforms, and Data Sources

- **Platform:** Analyst workstation  
- **Primary Tool:** Wireshark GUI protocol analyzer  
- **Data Sources:** Stored PCAP and PCAPNG capture files  
- **Analysis Mode:** Offline protocol dissection and reconstruction  
- **Supporting Tools:** OS file utilities for artifact validation  
- **Execution Method:** Graphical navigation and filtering  
- **Analysis Method:** Protocol decoding, session reconstruction, and artifact extraction

These tools collectively support in-depth network investigation workflows used by SOC analysts and incident responders.

