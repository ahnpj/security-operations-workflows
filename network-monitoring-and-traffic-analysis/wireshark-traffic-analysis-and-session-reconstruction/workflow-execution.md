# Workflow Execution — Traffic Filtering, Protocol Dissection, and Session Reconstruction Using Wireshark

### Overview

This workflow documents practical network traffic analysis using Wireshark to inspect packet-level communications and reconstruct network activity during investigative scenarios. The workflow focuses on capturing, filtering, and analyzing packet data to identify communication patterns, validate network behavior, and isolate traffic associated with potential security events.

The analysis progresses through structured traffic inspection phases, beginning with baseline network observation and advancing into protocol dissection, stream reconstruction, and targeted filtering. The workflow demonstrates how graphical packet analysis complements command-line capture tools by providing deeper visibility into protocol structure, payload data, and session context.

> **Workflow vs Execution vs Writeup (Terminology Used Here)**  
> - **Workflows** refer to operational tasks such as onboarding telemetry and validating parsing behavior.  
> - **Executions** refer to hands-on configuration and validation using real data and Splunk services.  
> - **Writeups** document configuration decisions, troubleshooting steps, and validation results.

> 👉 For a **detailed, step-by-step walkthrough of how this workflow was executed — complete with screenshots**, see the **[Step-by-Step Execution Walkthrough](#step-by-step-execution)** section below.

---

### Purpose and Analyst Focus

#### ▶ Purpose

The purpose of this workflow is to perform detailed packet inspection using Wireshark to analyze captured network traffic and validate communication behavior across multiple protocols. The workflow focuses on identifying suspicious or abnormal network activity by examining packet metadata, protocol fields, and reconstructed session streams.

The workflow demonstrates how visual packet inspection supports alert triage, investigation scoping, and validation of potential indicators of compromise. It emphasizes how analysts use Wireshark to interpret packet contents, identify anomalies within protocol communications, and extract evidence that supports detection and incident response workflows.

#### ▶ Analyst Focus

The analyst focus is on using Wireshark to dissect network traffic, apply targeted display filters, reconstruct communication sessions, and interpret protocol behavior to determine whether observed activity represents legitimate network operations or potential malicious behavior.

The workflow reflects responsibilities commonly performed by SOC analysts, incident responders, and detection engineers when validating alerts, investigating suspicious network communications, and performing deep traffic analysis. It reinforces the importance of understanding protocol structures, session reconstruction techniques, and traffic filtering strategies when analyzing packet-level telemetry during investigations.

---

### What This Workflow Demonstrates

This workflow demonstrates an end-to-end packet review process using pre-captured traffic. Specifically, it demonstrates how to:

- Load and interpret stored PCAP/PCAPNG evidence rather than relying on live capture availability.
- Use Wireshark’s three-pane model (Packet List, Packet Details, Packet Bytes) to correlate decoded fields to raw bytes.
- Apply and reason about coloring rules and priority ordering (top-to-bottom matching behavior).
- Validate capture context by merging multiple files and reviewing capture file properties (including hashes and metadata).
- Break down a specific HTTP packet (Packet #27) across OSI layers and interpret how fields map to header bytes.
- Navigate large datasets by jumping to packets, searching by content, marking and commenting for analyst annotation, and exporting subsets of evidence.
- Extract transferred objects (for example, HTTP-delivered files) and validate them using file hashing.
- Apply filters efficiently using “Apply as Filter,” conversation filters, “Prepare as Filter,” and stream reconstruction (“Follow TCP Stream”).

---

### Investigation and Detection Relevance

Packet captures are frequently used when telemetry sources (firewall logs, proxy logs, EDR network events, IDS alerts) require validation at the packet level. The workflow’s steps map directly to common security and troubleshooting questions, including:

- What hosts communicated, on which ports, and under which protocol?
- What was the application-layer transaction content (for example, HTTP request/response)?
- Do packet timings, sequence/ack numbers, and flags indicate normal session behavior or anomalies?
- Can files transferred over the network be extracted and integrity-checked for analysis?

By documenting repeatable procedures (navigation, filtering, object export, and evidence annotation), this workflow supports collaboration and defensible reporting—especially when multiple analysts need to review the same capture artifact.

---

### Environment and Execution Context

This section documents the tools, evidence sources, and constraints used to perform the workflow so that results can be interpreted consistently and reproduced by another analyst.

**Note:** Each section is collapsible. Click the ▶ arrow to expand and view details on software, tools, environment, data sources, and more.

<details>
<summary><strong>▶ Environment & Platform</strong><br>
</summary><br>

Wireshark analysis was performed using stored capture files (PCAPNG) rather than relying exclusively on live sniffing. This decision ensures repeatability and removes dependency on local interface availability.

</details>

<details>
<summary><strong>▶ Tooling and Constraints</strong><br>
</summary><br>

- **Primary tool:** Wireshark (graphical network protocol analyzer)
- **Capture mode used:** Offline analysis (pre-captured files)
- **Live capture attempt:** A live capture attempt was performed to validate interface availability; the capture could not be started because no active local network interfaces were available and the selected option was a remote capture module.

The workflow intentionally relies on Wireshark-native capabilities (pane navigation, display filtering, stream reconstruction, export features) rather than external protocol decoding tools.

</details>

<details>
<summary><strong>▶ Data Sources Analyzed</strong><br>
</summary><br>

The workflow used the following stored packet capture files:

- **http1.pcapng** (used for HTTP-centric packet inspection and session reconstruction)
- **Exercise.pcapng** (merged with the primary capture to evaluate multi-file timeline behavior and to practice file property review)

</details>

<details>
<summary><strong>▶ Workflow Map (High-Level)</strong><br>
</summary><br>

1. Load and orient within Wireshark (panes, filters, coloring rules).
2. Attempt live capture and validate capture interface availability.
3. Merge capture files and verify capture metadata and file properties.
4. Dissect a representative HTTP packet (Packet #27) across OSI layers; correlate decoded fields to raw bytes.
5. Navigate and annotate captures (search, mark, comment) and export evidence (objects and packet subsets).
6. Apply display filters and conversation/session reconstruction (conversation filters, prepared filters, columns, Follow Stream).

</details>

---

### Step-by-Step Execution

This section documents the workflow in the same order an analyst would realistically perform network traffic analysis using Wireshark. The process begins with loading and reviewing captured traffic to establish baseline network activity and identify protocols present within the dataset. Analysis then progresses into targeted filtering, packet inspection, and session reconstruction to isolate traffic relevant to investigative objectives.

Each phase captures both the technical actions performed within Wireshark and the investigative reasoning behind those actions. The workflow intentionally progresses from broad traffic visibility into targeted protocol analysis and stream reconstruction, mirroring how analysts move from general network observation to evidence-driven traffic interpretation during network investigations.

**Note:** Each section is collapsible. Click the ▶ arrow to expand and view the detailed steps.

<details>
<summary><strong>▶ Phase 1 — Interface Familiarization and Capture Context Validation</strong><br>
→ establishing the Wireshark interface model and validating how captures are loaded, visualized, and contextualized
</summary><br>

This phase establishes the Wireshark interface model and validates how captures are loaded, visualized, and contextualized.

**Note:** This section focused on familiarizing myself with Wireshark’s graphical interface, core features, and basic functionalities. I learned how to load PCAP files, interpret different panes, and understand what each visual section of the interface represents.

##### 🔷 Phase 1.1 — Review Wireshark interface layout and primary panes

Wireshark’s layout was reviewed to establish where critical analysis controls and evidence views are located. The interface areas observed included the **Toolbar**, **Display Filter Bar**, **Recent Files**, **Capture Interfaces**, and **Status Bar**.

<p align="left">
  <img src="images/wireshark-packet-analysis-and-filtering-01.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="800"><br>
  <em>Figure 1</em>
</p>

Wireshark evidence is interpreted primarily through three panes, each serving a distinct purpose:

- **Packet List Pane** (top pane) presents a row-per-packet summary view, including time, source, destination, protocol, and length. This pane supports fast scanning and navigation.
- **Packet Details Pane** (bottom-left pane) decodes the selected packet into a hierarchical protocol tree (for example: Ethernet → IP → TCP → HTTP). This pane is used to interpret fields and flags.
- **Packet Bytes Pane** (bottom-right pane) shows the raw packet bytes in hexadecimal and ASCII. This pane is used to validate how decoded fields map to bytes on the wire.

##### 🔷 Phase 1.2 — Load and open stored capture files

Stored capture file loading was practiced using **http1.pcapng**, ensuring that the capture was displayed correctly and that packet details were accessible immediately.

<blockquote>
You can load the PCAP file by either opening it from the "File" menu, dragging and dropping the file directly, or simply double-clicking the file itself. Drag-and-drop was used during this execution.
</blockquote>

<p align="left">
  <img src="images/wireshark-packet-analysis-and-filtering-02.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="800"><br>
  <em>Figure 2</em>
</p>

The packet details were displayed in three key panes:
  - **Packet List Pane** – shows a summary of each captured packet, including protocol, source, destination, and length. (top pane)
  - **Packet Details Pane** – displays protocol details in a hierarchical structure, such as Ethernet, IP, TCP, and application layer data. (bottom-left pane)
  - **Packet Bytes Pane** – presents hexadecimal and ASCII representations of the selected packet. (bottom-right pane)

##### 🔷 Phase 1.3 — Evaluate packet coloring behavior and rule priority

Wireshark’s default packet coloring system was reviewed to understand how visual highlighting can accelerate protocol identification and anomaly spotting. Both **temporary** and **permanent** coloring rules were explored by navigating to **View → Coloring Rules** and reviewing the **Wireshark - Coloring Rules Default** modal.

<p align="left">
  <img src="images/wireshark-packet-analysis-and-filtering-03.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="800"><br>
  <em>Figure 3</em>
</p>

A key behavioral detail was validated: Wireshark applies colors using the **first matching rule from top to bottom**. As a result, TCP packets can appear green even when a default TCP rule is configured as purple, if those packets match a higher-priority rule (for example, a “Bad TCP” or other earlier rule).

<blockquote>
At first, I was confused about why most of the TCP packets in my Wireshark capture were showing up green instead of purple, even though the default TCP colouring rule was clearly set to purple. After checking the **Coloring Rules** window, I realized that Wireshark applies colours based on the first matching rule from top to bottom. That means if a packet matches a rule higher in the list (like “Bad TCP” or another green rule), Wireshark uses that colour and doesn’t continue checking further rules. Once I understood this priority system, it made sense why most of my TCP packets appeared green. They were simply being matched by an earlier rule in the list before the default purple TCP rule.
</blockquote>

<blockquote>
I later experimented with toggling the “Colorize Packet List” feature and using conversation filters for temporary highlighting. Overall, I now understand how packet colours can make analysis more efficient and how to customize these rules for specific events of interest.
</blockquote>


##### 🔷 Phase 1.4 — Attempt live traffic capture and validate interface availability

A live capture attempt was performed to validate whether traffic could be sniffed directly from local interfaces. Capture start/stop mechanics were reviewed (including the blue “shark fin” capture control).

Capture configuration was accessed via **Capture → Options**, and available interfaces were reviewed. The interface **“Cisco remote capture: ciscodump”** was initially selected under the assumption it represented a local adapter; it was then determined this option is intended for **remote capture from Cisco devices**, not local traffic collection.

<blockquote>
I selected “Cisco remote capture: ciscodump,” thinking it was my network interface, but I later learned it’s actually used for remote captures from Cisco devices, not local network traffic. The other interfaces listed were also virtual or system-based, not real network adapters. Because there were no active local interfaces, the [Start Capture] button stayed greyed out.
</blockquote>

Because the other listed interfaces were virtual or system-based rather than active local network adapters, the **[Start Capture]** button remained greyed out and a live capture could not be initiated.

##### 🔷 Phase 1.5 — Merge PCAP files and review capture file properties

Wireshark’s multi-file analysis capability was exercised using **File → Merge** to combine captures into a unified timeline. This approach is useful when:

- traffic is captured from different interfaces or locations,
- client-side and server-side captures need correlation,
- sequential captures need to be analyzed as a continuous session.

<blockquote>
I decided to try merging another .pcap file with my current capture to see how Wireshark handles multiple data sources in one timeline. Merging pcap files is useful when you want to analyze traffic captured from different interfaces or at different times together. For example, combining client and server captures to see the full conversation, or merging sequential captures to create one continuous session. It helps provide a more complete picture of network activity without having to switch between separate files.
</blockquote>

(Phase 1.5, Step 1) — Merge Exercise.pcapng into http1.pcapng

The merge action was performed via **File > Merge**, selecting **Exercise.pcapng** to merge into **http1.pcapng**.

<p align="left">
  <img src="images/wireshark-packet-analysis-and-filtering-04.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="800"><br>
  <em>Figure 4</em>
</p>

(Phase 1.5, Step 2) — Review capture file properties and hash metadata

Capture file properties were reviewed via **Statistics → Capture File Properties** to validate capture context and metadata such as creation timing, source interface information, file format, and checksum values (including SHA256).

This review is operationally important because capture interpretation depends on confirming:

- capture duration and packet count,
- capture origin and interface context,
- whether the file has identifiable integrity markers (hashes) for evidence handling.

<p align="left">
  <img src="images/wireshark-packet-analysis-and-filtering-05.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="800"><br>
  <em>Figure 5</em>
</p>

<blockquote>
I went to **Statistics → Capture File Properties** because I wanted to see more information about the capture file itself, such as when it was created, what interface it came from, the SHA256 hash value, and what format or capture options were used. Viewing file details is important because it helps verify the context of the capture. For example, confirming the capture duration, packet count, and source interface can all be crucial for accurate analysis. It ensures you understand where the data originated and whether anything might affect how you interpret the packets.
</blockquote>

<blockquote>
This task helped me become comfortable with the Wireshark environment. I realized that while it can look overwhelming at first, its layout is designed for efficiency. The ability to colorize, filter, and merge captures helps tremendously when analyzing complex datasets. Packet details across the three panes allowed me to trace communication flow between hosts from the link layer up to the application layer.
</blockquote>

</details>

<details>
<summary><strong>▶ Phase 2 — Packet Dissection Across OSI Layers (Representative HTTP Packet)</strong><br>
→ dissecting packets at multiple OSI layers and examine detailed protocol information
</summary><br>

This phase focuses on a single packet to demonstrate how Wireshark decodes protocol layers and how decoded fields map to raw bytes. The objective of this section was to dissect packets at multiple OSI layers and examine detailed protocol information. 

<blockquote>
I wanted to understand how Wireshark decodes network packets and organizes them into structured fields for analysis. I examined captured HTTP traffic and learned to break packets down by OSI layers, starting from the physical layer up to the application layer. By clicking on a specific packet, Wireshark expanded its contents to reveal information such as Ethernet source/destination MAC addresses, IP headers, TCP flags, and payloads. For this phase, I focused in on a specific packet, which was packet #27 which was using the HTTP protocol. 
</blockquote>

##### 🔷 Phase 2.1 — Select a representative HTTP packet for layer-by-layer analysis

Packet dissection was performed on **Packet #27**, identified as an HTTP protocol packet. The selection supports consistent interpretation because the same packet can be re-opened and reviewed repeatedly across analysis passes.

The relationship between panes was explicitly exercised:

- The **Packet Details Pane** (bottom-left) lists each protocol layer decoded from the packet.
- The **Packet Bytes Pane** (bottom-right) presents raw bytes captured on the wire.
- Selecting any decoded field in the **Packet Details Pane** highlights the corresponding bytes in the **Packet Bytes Pane**, enabling direct verification of how structured fields are represented in binary.

The **Packet Details Pane** pane at the bottom-left lists each decoded protocol layer, while the **Packet Bytes Pane** pane at the bottom-right displays the raw hexadecimal data that was actually captured on the wire.

<blockquote>
When I click any field in the **Packet Details Pane** pane at the bottom-left, Wireshark automatically highlights the exact bytes in the **Packet Bytes Pane** pane at the bottom-right hex view that correspond to that field.
</blockquote>

The byte mapping concept was reinforced:
- Each row in the hex view shows 16 bytes (the actual bits sent over the network).
- Wireshark maps those bytes to their decoded meaning, so when you select, say, the “Source IP” line, the four bytes representing that IP address turn blue in the hex pane.
- This makes it easy to trace any part of a packet back to its raw data representation and see how the packet is built layer-by-layer.

##### 🔷 Phase 2.2 — Layer 1: Frame metadata (Physical)

The **Frame** section was reviewed as the representation of Layer 1 (Physical) capture metadata, including arrival timing, encapsulation type, and total frame length.

In this Wireshark capture, the **Frame** section represents information captured at Layer 1 (Physical layer) of the OSI model, which is the point where raw bits are transmitted across the physical medium (like an Ethernet cable or Wi-Fi).

<p align="left">
  <img src="images/wireshark-packet-analysis-and-filtering-06.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="800"><br>
  <em>Figure 6</em>
</p>

The fields observed describe the packet as it appeared on the wire rather than the semantic contents:

- Frame 27 identifies the specific packet captured out of all the traffic on the wire.
- 214 bytes on wire, 214 bytes captured means the full frame was successfully captured from the physical medium.
- Encapsulation type: Ethernet (1) tells us this capture was taken on an Ethernet network which is the physical and data-link technology used.
- Arrival Time / Epoch Time / Time delta fields show when the signal reached the network interface and how much time elapsed between packets. This relates to the timing of bit transmission on the medium.
- Protocols in frame: lists the protocol stack Wireshark detected inside the captured bits (Ethernet → IP → TCP → HTTP).

This view corresponds to the Physical Layer context where data exists as electrical/optical/radio signals before being interpreted by higher layers.

<blockquote>
In other words, this layer shows metadata about how the packet physically appeared on the wire — its total size in bits, when it was received, and how it was encapsulated.
</blockquote>

The highlighted blue section corresponds to the bytes that belong to the Ethernet, IP, and TCP headers (and possibly part of the HTTP payload). It visually connects the physical transmission (hexadecimal data) to the structured OSI layers shown in the details pane.

<p align="left">
  <img src="images/wireshark-packet-analysis-and-filtering-07.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="800"><br>
  <em>Figure 7</em>
</p>

##### 🔷 Phase 2.3 — Layer 2: Ethernet II header (Data Link / MAC addressing)

The Ethernet II header was reviewed as Layer 2 evidence for framing and MAC-based delivery on the local network.

The **Source [MAC] Layer** (Layer 2 - Data Link) revealed IP header information, including source and destination IPv4 addresses, protocol version, and time-to-live (TTL) value.

In this capture, Wireshark displayed the Ethernet II header, which represents Layer 2 (the Data Link layer) of the OSI model.

<blockquote>
This layer is responsible for framing, MAC addressing, and delivering packets between devices on the same local network. It doesn’t deal with IPs or ports yet, only the physical device identifiers (MAC addresses).
</blockquote>

<p align="left">
  <img src="images/wireshark-packet-analysis-and-filtering-08.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="800"><br>
  <em>Figure 8</em>
</p>

Fields interpreted (inside the red box):

- Destination: Xerox_00:00:00 → The hardware address of the receiving device.
- Source: fe:ff:20:00:01:00 → The MAC address of the sending device.
- Type: IPv4 (0x0800) → Indicates that the payload inside this Ethernet frame is an IP packet (Layer 3).

These details show how the **Data Link layer** wraps the network-layer data in an Ethernet frame to move it across a physical medium (like a switch or LAN). When this frame reaches the destination, the MAC address helps ensure it’s delivered to the correct network interface before being passed up to Layer 3 (IP).

##### 🔷 Phase 2.4 — Layer 3: IPv4 header (Network / routing)

The IP header was reviewed as Layer 3 evidence for logical addressing, routing semantics, and transport protocol identification.

The **Source [IP] Layer** (Layer 3 - Network) revealed IP header information, including source and destination IP addresses, protocol version, and time-to-live (TTL) value.

<blockquote>
In this capture, Wireshark is displaying details from the Internet Protocol (IP) header, which represents the Network layer (Layer 3) of the OSI model. This layer is responsible for logical addressing and routing as it determines how packets travel from one device to another across different networks.
</blockquote>

<p align="left">
  <img src="images/wireshark-packet-analysis-and-filtering-09.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="800"><br>
  <em>Figure 9</em>
</p>

Fields (Layer 3) interpreted (inside the red box):

- **Version 4** confirms IPv4.
- **Header Length (20 bytes)** indicates IP header size before TCP begins.
- **Source IP: 216.239.59.99** identifies the sender’s logical address.
- **Destination IP: 145.254.160.237** identifies the receiver’s logical address.
- **Protocol: TCP (6)** specifies the transport layer protocol.
- **TTL: 55** indicates the remaining hop limit.
- **Total Length: 200 bytes** indicates the full IP datagram size.

All of these values are used by routers and network devices to route the packet from its source to its final destination across networks, regardless of physical medium or local addressing (like MACs).

Selecting these fields in the details pane was used to confirm byte-level mapping in the bytes pane for direct validation of each Layer 3 element.

<blockquote>
When you click any of these IP fields in Wireshark, the corresponding bytes in the **Packet Bytes Pane** (bottom-right) are highlighted which showed the exact binary data representing these Layer 3 details.
</blockquote>

##### 🔷 Phase 2.5 — Layer 4: TCP header (Transport / session reliability)

The TCP header was reviewed as Layer 4 evidence for end-to-end reliability, port-based application identification, sequencing, and acknowledgment behavior.

The **Protocol Layer** (Layer 4 - Transport) revealed details of the protocol used (UDP/TCP), including sequence and acknowledgment numbers, flags (SYN, ACK, FIN), and window size and source/destination ports.

<blockquote>
In this capture, Wireshark is displaying details from the Transmission Control Protocol (TCP) header, which represents the Transport layer (Layer 4) of the OSI model. This layer is responsible for end-to-end communication, ensuring data is reliably delivered between the source and destination applications. It uses port numbers to identify which process or service is sending and receiving the data.
</blockquote>

<p align="left">
  <img src="images/wireshark-packet-analysis-and-filtering-10.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="800"><br>
  <em>Figure 10</em>
</p>

Core fields interpreted (key fields that define this TCP segment):
- **Source Port: 80** – the sending application’s port (HTTP server).
- **Destination Port: 3371** indicates the client-side ephemeral port.
- **Sequence Number: 1431** supports ordered byte tracking.
- **Acknowledgment Number: 722** confirms receipt of prior data.
- **Header Length: 20 bytes** indicates TCP header size.

The bytes pane highlight was used to validate exactly where TCP header bytes reside within the packet.

Expanded subsections in the TCP tree were reviewed to interpret behavior:

- **Flags** (including PSH and ACK) describe control behavior and delivery semantics and shows control bits like PSH (push data to the app immediately) and ACK (acknowledges received data). Flags are critical for managing TCP’s connection-oriented behavior (SYN, ACK, FIN, etc.).
- **SEQ/ACK Analysis** provides relative numbers to follow stream order more easily. Wireshark calculates and shows relative sequence/acknowledgment numbers to make it easier to follow streams of packets in order.
- **Timestamps** support delay measurement, congestion control, and retransmission logic. It records the timing information used to measure round-trip delay and help with congestion control and retransmission.

<p align="left">
  <img src="images/wireshark-packet-analysis-and-filtering-11.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="800"><br>
  <em>Figure 11</em>
</p>

The observed **Flags field (0x018 – PSH, ACK)** indicates the segment is acknowledging received data and requesting that received content be pushed to the application immediately.

<blockquote>
These details show how TCP provides reliability by numbering segments, confirming receipt, and keeping track of timing. In the **Packet Bytes Pane** on the bottom-right, the blue-highlighted bytes correspond to the exact section of the packet where the TCP header data resides. This visually connects the decoded TCP information to its raw binary form.
</blockquote>

<blockquote>
Within the TCP header, I observed important fields such as **Sequence and Acknowledgment** numbers, which track data flow between the sender and receiver. The **Flags field (0x018 – PSH, ACK)** showed that the packet was actively acknowledging received data and instructing the receiver to push it immediately to the application.
</blockquote>

<blockquote>
Additional dropdowns like **SEQ/ACK analysis** and **Timestamps** revealed how Wireshark tracks packet timing, delays, and flow control. These values help verify that TCP communication is synchronized and reliable. Overall, this section demonstrated how Layer 4 manages data delivery, acknowledgment, and timing—bridging the IP-based routing (Layer 3) below and application data (Layer 7) above.
</blockquote>

##### 🔷 Phase 2.6 — Layer 4 continuation: reassembly and protocol segmentation behavior

Wireshark’s TCP reassembly behavior was reviewed to understand how fragmented payloads are reconstructed into a single data stream.

The **Protocol Errors Layer** (Layer 4 - Transport) is a continuation of the 4th layer and showed specfic details about any TCP errors. I explored **protocol reassembly**, where Wireshark automatically combined fragmented TCP streams to show complete data transfers.

<blockquote>
In this capture, Wireshark is displaying reassembled TCP Segments, which is part of the **Transport layer (Layer 4)** in the OSI model. TCP often splits large pieces of data into multiple smaller segments, and Wireshark automatically reassembles them to show the full data stream.
</blockquote>

<p align="left">
  <img src="images/wireshark-packet-analysis-and-filtering-12.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="800"><br>
  <em>Figure 12</em>
</p>

Reassembly details interpreted (inside the red box):

- **Frame 26 (payload 0–1429 bytes)** and **Frame 27 (payload 1430–1589 bytes)** represent two segments of the same message.
- **Segment count: 2** confirms the payload was divided.
- **Reassembled TCP length: 1590 bytes** confirms combined payload size.
- **Reassembled TCP Data** shows the reconstructed byte stream before handing off to the application protocol decoder (HTTP).

This confirms ordered delivery behavior: even when data is split across segments, TCP stream reconstruction enables analysis of the full payload content.

##### 🔷 Phase 2.7 — Layers 5–7: HTTP application protocol and content

The HTTP section was reviewed as the representation of upper OSI layers (Session, Presentation, Application) where user-facing protocol semantics and content appear.

The **Application layer** (Layer 5,6,7 - Sessions, Presentation, Application) decoded protocols like HTTP, showing request methods, user agents, and URLs accessed. The **Application Data Layer** showed the actual content or payload (HTML, JSON, etc.)

<blockquote>
In this capture, Wireshark displays the Hypertext Transfer Protocol (HTTP) section, which represents the top layers of the OSI model (5–7) — the Session, Presentation, and Application layers.
</blockquote>

This part of the packet shows the actual application data being exchanged between the client and server after all lower-layer transmissions (Ethernet, IP, TCP) have been completed.

<p align="left">
  <img src="images/wireshark-packet-analysis-and-filtering-13.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="800"><br>
  <em>Figure 13</em>
</p>

Fields interpreted (inside the red box):
- **HTTP/1.1 200 OK** → the server’s response indicating the client’s request was successful.
- **Status Code 200 and Response Phrase “OK”** → confirm proper communication and content delivery.
- **Content-Type: text/html** → tells the client the data being sent is an HTML web page.
- **Content-Length: 1272 bytes** → specifies the size of the response body.
- **Date and Server fields** → show when and by what system the response was generated.
- The HTML text in the “Line-based text data” section shows part of the actual web content.

<blockquote>
This layer corresponds to the Application level of the OSI model, where user-facing protocols (like HTTP, FTP, SMTP, or DNS) operate.
</blockquote>

OSI mapping in this context:
- Layer 5 (Session): manages and maintains the communication session between client and server.
- Layer 6 (Presentation): translates and formats data for readability (e.g., text/html, encoding type).
- Layer 7 (Application): handles the actual application protocol — in this case, HTTP for web communication.

<blockquote>
Packet dissection allowed me to see how data travels through network layers. By analyzing headers, I could identify the path, type, and purpose of packets. I also learned how Wireshark automatically interprets complex fields like checksums and TCP segments, saving time compared to manual decoding. Seeing the full HTTP request headers (like “GET /index.html”) helped connect the transport and application layers.
</blockquote>

<blockquote>
I learned to correlate protocol layers to understand end-to-end communication. This exercise gave me hands-on experience tracing traffic from Ethernet frames to TCP streams and application data.
</blockquote>

</details>

<details>
<summary><strong>▶ Phase 3 — Capture Navigation, Evidence Annotation, and Export</strong><br>
→ managing large packet datasets and creating analyst-friendly evidence subsets
</summary><br>

This phase focuses on managing large packet datasets and creating analyst-friendly evidence subsets.

<blockquote>
This phase also focused on learning how to efficiently navigate within Wireshark captures, locate specific packets, and manage annotations for deeper analysis.
</blockquote>

##### 🔷 Phase 3.1 — Use packet numbers for deterministic navigation

Wireshark’s packet numbering was used as a primary navigation anchor. Selecting a packet number in the **Packet List Pane** drives synchronized updates in:

- **Packet Details Pane** for decoded protocol layers
- **Packet Bytes Pane** for raw hex/ASCII view

<p align="left">
  <img src="images/wireshark-packet-analysis-and-filtering-14.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="800"><br>
  <em>Figure 14</em>
</p>

<blockquote>
While exploring Wireshark, I learned that each packet is assigned a unique number in the **Packet List Pane**, which helps identify and analyze individual transmissions. When I click on a specific packet number, Wireshark displays its detailed breakdown in the **Packet Details Pane** at the bottom-left, showing protocol layers and fields. At the same time, the **Packet Bytes Pane** at the bottom-right reveals the raw hexadecimal and ASCII data, allowing me to see exactly what the packet looks like at the byte level.
</blockquote>

##### 🔷 Phase 3.2 — Jump directly to specific packets using Go menu controls

Navigation features under **[Go]** were exercised to move deterministically within the capture:

- **Go to Packet** supports jumping by packet number or relative position.
- **Next/Previous Packet** supports sequential navigation.
- **Next/Previous Packet in Conversation** supports stream-focused navigation.
- **First/Last Packet** supports boundary navigation.

These features support conversation tracking and reduce time spent scrolling through large captures.

<p align="left">
  <img src="images/wireshark-packet-analysis-and-filtering-15.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="800"><br>
  <em>Figure 15</em>
</p>

<blockquote>
In this step, I explored how packet numbering works in Wireshark and how it helps with both navigation and analysis. I learned that each packet has a unique number in the **Packet List Pane**, and then clicking one opens its protocol details in the **Packet Details Pane** and its raw data in the **Packet Bytes Pane**. 
</blockquote>

<blockquote>
I also practiced using the **[Go]** menu and toolbar options, including **[Go to Packet]** to jump to a specific number by number or relative position, **[Next/Previous Packet]** to move up or down, **[Next/Previous Packet in Conversation]** to follow related packets within the same stream, and **[First/Last Packet]** to reach the start or end of the capture. 
</blockquote>

<blockquote>
These tools made it easier to track communication between hosts and understand how packets relate to each other. Overall, this part helped me see how packet numbering and navigation features improve the efficiency of analyzing network traffic in Wireshark.
</blockquote>

###### 🔷 Phase 3.3 — Find packets by content using Edit → Find Packet

Packet discovery was practiced using **Edit → Find Packet** for content-based searching.

<p align="left">
  <img src="images/wireshark-packet-analysis-and-filtering-16.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="800"><br>
  <em>Figure 16</em>
</p>

The four input types were reviewed:

- Display filter
- Hex
- String
- Regex

<p align="left">
  <img src="images/wireshark-packet-analysis-and-filtering-17.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="800"><br>
  <em>Figure 17</em>
</p>

<blockquote>
In this step, I learned how to find packets in Wireshark using the [Edit → Find Packet] feature. Unlike packet numbers, this tool allows searching by packet content, which is useful for locating specific events such as intrusion patterns or network errors. I also learned that "String" and "Regex" are the most commonly used, with an option to enable/disable case sensitivity.
</blockquote>

Search scope selection across panes was practiced to ensure searches target the correct representation of data:

- Packet List pane (top half)
- Packet Details pane (bottom-left) 
- Packet Bytes pane (bottom-right)

<p align="left">
  <img src="images/wireshark-packet-analysis-and-filtering-18.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="800"><br>
  <em>Figure 18</em>
</p>

<blockquote>
This showed me how important it is to choose the right input type and pane when performing searches to efficiently pinpoint the packets of interest.
</blockquote>

##### 🔷 Phase 3.4 — Mark packets for temporary highlighting

Marking was exercised to flag packets of interest for quick visual review. Marking is temporary and cleared when the capture file is closed.

- Marking method: **Edit → Mark/UnMark Packet(s)** or right-click
- Shortcut: **Ctrl + M**
- Example scope marked: packets **20–26**

Marked packets display in black regardless of prior coloring, improving visibility during analysis.

<blockquote>
I noticed that once marked, packets appear in black, regardless of their original color, which helps quickly identify them during analysis. For practice, I marked packets 20 - 26. 
<blockquote>

<p align="left">
  <img src="images/wireshark-packet-analysis-and-filtering-19.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="800"><br>
  <em>Figure 19</em>
</p>

<blockquote>
I learned how to mark one or more packets in Wireshark to highlight specific event(s) or packet(s) for further analysis. By using the **[Edit] → Mark/UnMark Packet(s)]** or right-clicking a packet or group of packets, I could easily mark or unmark one or more packets of interest. This makes them stand out for later review or export. 
</blockquote>

<blockquote>
I also learned that marking is temporary. Marked packets are cleared once the capture file is closed. This feature is especially useful for keeping track of important findings during live or large-scale packet investigations.
</blockquote>

<blockquote>
"Ctrl + M" is the hotkey shortcut.
</blockquote>

##### 🔷 Phase 3.5 — Add persistent packet comments for documentation

Packet commenting was exercised to embed analyst notes directly into the capture file. Unlike marking, comments persist until removed.

<blockquote>
I explored how to add comments to packets for documentation purposes, then viewed and edited them through the "Packet Comments" panel.
</blockquote>

<p align="left">
  <img src="images/wireshark-packet-analysis-and-filtering-20.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="800"><br>
  <em>Figure 20</em>
</p>

Commenting methods reviewed:

- Right-click → Packet Comment
- Edit → Packet Comment
- Shortcut: **Ctrl + Alt + C**

However, unlike marking, comments are saved within the capture file and remain there until manually removed. This makes it a valuable feature for collaboration and long-term investigations, as analysts can leave detailed notes directly tied to specific packets.

<blockquote>
I used the [right-click → Packet Comment] option (as shown in my screenshot), but I also learned that you can comment on packets through the [Edit → Packet Comment] menu or by using the [Ctrl + Alt + C] shortcut
</blockquote>

The **Packet Comments** panel was used to view and edit a test comment for packet **#23**.

<p align="left">
  <img src="images/wireshark-packet-analysis-and-filtering-21.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="800"><br>
  <em>Figure 21</em>
</p>

<blockquote>
I learned how to add comments to packets in Wireshark to document important findings or suspicious activity during analysis. Similar to marking, commenting helps highlight specific packets for further investigation or for other analysts reviewing the same capture.
</blockquote>

##### 🔷 Phase 3.6 — Export transferred objects from protocol streams

The **Export Objects** feature was reviewed as a method to extract files transferred over supported protocol streams. This is operationally useful for forensics and incident response when transferred artifacts need validation or malware analysis.

<blockquote>
I tested the [Export Objects] feature ([File > Export Objects]), a feature that extracts downloadable content (e.g., HTTP files) embedded within packets.
</blockquote>

Supported protocol categories observed include:

- DICOM
- HTTP
- IMF
- SMB
- TFTP

<p align="left">
  <img src="images/wireshark-packet-analysis-and-filtering-22.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="800"><br>
  <em>Figure 22</em>
</p>

<blockquote>
I learned how Wireshark can extract and export files that were transferred over the network. This feature is especially valuable for security analysts, as it allows them to recover and examine shared or potentially malicious files for further investigation. 
</blockquote>

<blockquote>
I discovered that exporting objects is only available for certain protocol streams, including DICOM, HTTP, IMF, SMB, and TFTP. By accessing these streams, analysts can save transferred files locally to analyze their contents, verify suspicious activity, or gather evidence of data exfiltration. This capability makes Wireshark a powerful tool for both troubleshooting and digital forensics.
</blockquote>

##### 🔷 Phase 3.7 — Export selected/filtered packet subsets

Exporting packet subsets was exercised via **File → Export Specified Packets** to create smaller evidence files. This supports collaboration (sharing only relevant traffic) and deep-dive analysis (isolating incident scope without full dataset noise). This also allowed for the saving of filtered or selected packets into a new capture file.

<p align="left">
  <img src="images/wireshark-packet-analysis-and-filtering-23.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="800"><br>
  <em>Figure 23</em>
</p>

Export scope controls reviewed: **Captured** and **Displayed** packets. 
- **Captured** includes all packets in the file
- **Displayed** only includes those visible after applying filters

<blockquote>
This makes it easier to save and share only the relevant data needed for investigation while excluding unnecessary traffic. I also learned that Wireshark provides additional options to export.
</blockquote>

Additional export options reviewed:

- **Selected packets only**: exports only packets manually selected in the Packet List Pane (example selection: packets **3–13**).
- **Marked packets only**: exports packets previously marked during analysis using the **[Edit]** menu or right-click option. This helps isolate packets flagged as important or suspicious during the investigation.
- **Range**: exports a defined packet number range (for example, 50–150). This is helpful when capturing a continuous portion of traffic without exporting the entire file.

<p align="left">
  <img src="images/wireshark-packet-analysis-and-filtering-24.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="800"><br>
  <em>Figure 24</em>
</p>

This gives analysts flexibility when isolating specific parts of network activity. Overall, this feature helps streamline the analysis process and focus on packets tied directly to an incident or event of interest.

<blockquote>
I learned how to export specific packets from a capture file in Wireshark for focused analysis. Since capture files can contain thousands of packets, it’s often necessary to isolate only the suspicious or relevant packets to investigate an incident more efficiently. 
</blockquote>


##### 🔷 Phase 3.8 — Adjust time display format for temporal correlation

Time display formatting was adjusted to support investigations requiring consistent timestamps that correlate with external logs.

- Default mode observed: **Seconds Since Beginning of Capture**
- Adjusted mode used: **UTC Date and Time of Day** via **View → Time Display Format**

<blockquote>
I experimented with adjusting the "Time Display Format", switching between default and UTC timestamps for better temporal analysis.
</blockquote>

<blockquote>
However, this isn’t always ideal for investigations that require exact timestamps or time correlation with other systems. Using the **[View → Time Display Format]** menu, I switched to the **UTC Date and Time of Day**, which provided clearer, standardized timestamps. This feature helps analysts better align packet activity with external logs or events during incident analysis.
</blockquote>

<p align="left">
  <img src="images/wireshark-packet-analysis-and-filtering-25.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="800"><br>
  <em>Figure 25</em>
</p>

<blockquote>
I learned how to change the time display format in Wireshark to make packet analysis easier and more accurate. By default, Wireshark shows time as **Seconds Since Beginning of Capture**, which reflects when each packet was captured relative to the start of the recording. 
</blockquote>

##### 🔷 Phase 3.9 — Review Expert Information for anomaly surfacing

Wireshark’s **Analyze → Expert Information** feature was reviewed to understand how Wireshark automatically categorizes notable conditions and potential anomalies.

This tool categorizes findings into different severity levels, indicates the protocols, and displays the number of occurrences:

- Chat (Blue) for normal information
- Note (Cyan) for notable events
- Warn (Yellow) for warnings
- Error (Red) for serious problems like malformed packets.

Wireshark also groups these detections under categories such as **Checksum**, **Comment**, **Deprecated**, **Malformed**, **Protocol**, and **Sequence**. This helps analysts quickly identify specific types of issues. 

The expert info can be viewed through the **[Analyze → Expert Information]** menu or in the lower-left status bar, where a summary window lists the packet number, protocol group, and total occurrences. This feature is especially helpful for spotting irregular behavior and prioritizing which packets need deeper investigation.

Category groupings reviewed:

- Checksum
- Comment
- Deprecated
- Malformed
- Protocol
- Sequence

<p align="left">
  <img src="images/wireshark-packet-analysis-and-filtering-26.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="800"><br>
  <em>Figure 26</em>
</p>

<blockquote>
There are around 8 – 10 major groups, but Wireshark dynamically shows only the ones relevant to the traffic you’re analyzing. In the screenshot above, for example, I was only seeing Malformed, Protocol, Sequence, and Comment, which are the most common ones in typical TCP/HTTP captures.
</blockquote>

##### 🔷 Phase 3.10 — Self Test 1: Extract a transmitted image and compute its MD5 hash

A specific HTTP-delivered image transfer was identified at packet **#39765**, where an **HTTP 200 OK** response returned a **JPEG** payload. Wireshark reassembly was used to reconstruct the full image content across multiple TCP segments, and the JPEG file structure was observable in the Packet Details pane (Start of Image `0xFFD8`, quantization tables, Start of Scan, etc.).

In short: the client sent an "HTTP GET", the server replied with the "JPEG" payload, Wireshark reassembled the TCP stream, and the packet (and reassembled bytes) show the full `JPEG` content ready for export.

<blockquote>
In this self test, I observed packet number 39765, and saw an "HTTP 200 OK" response from a remote server returning a "JPEG" image to my host. The image was split across multiple TCP segments, so Wireshark reassembled those segments and decoded the file structure (you can see the Start of Image (0xFFD8), quantization tables, Start of Scan, etc.) in the "Packet Details" pane.
</blockquote>

<p align="left">
  <img src="images/wireshark-packet-analysis-and-filtering-27.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="800"><br>
  <em>Figure 27</em>
</p>

(Phase 3.10, Step 1) — Export the JPEG bytes from the packet details tree

To extract the image bytes, the **JPEG File Interchange Format** item under the Packet Details Pane was selected and **Export Packet Bytes** was used.

<p align="left">
  <img src="images/wireshark-packet-analysis-and-filtering-28.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="800"><br>
  <em>Figure 28</em>
</p>

(Phase 3.10, Step 2) — Save exported image as peter_test.jpg and validate reconstruction

The raw image data was saved as `peter_test.jpg` to desktop.

<p align="left">
  <img src="images/wireshark-packet-analysis-and-filtering-29.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="800"><br>
  <em>Figure 29</em>
</p>

<blockquote>
This method exports only the bytes from that specific protocol layer, effectively reconstructing the image as it was transmitted over the network. Once saved, I could open the image locally to verify that it was successfully captured and properly reconstructed.
</blockquote>

(Phase 3.10, Step 3) — Compute the MD5 hash of the exported image

A Bash terminal was opened in the directory containing the exported file and the MD5 hash was computed.

`md5sum peter_test`

<p align="left">
  <img src="images/wireshark-packet-analysis-and-filtering-30.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="800"><br>
  <em>Figure 30</em>
</p>

<blockquote>
I could also run "sha256sum peter_test" to retrieve the SHA256 hash. I recorded the resulting hash in my workflow execution excercise notes and used it to verify the integrity of the saved file by re-running the same command later which produced the same hash to verify the file was unchanged.
</blockquote>

<blockquote>
MD5 and SHA-256 are different hashing algorithms with key differences in their security and hash output size. SHA-256 is a more secure algorithm that produces a 256-bit hash, while MD5 is older, faster, and produces a 128-bit hash.
</blockquote>

##### 🔷 Phase 3.11 — Self Test 2: Locate an HTTP-delivered .txt object and read it

A text file was known to exist in the capture. The workflow used Wireshark’s HTTP object export functionality rather than manual packet inspection.

(Phase 3.11, Step 1) — Open HTTP Export Objects list

**File > Export Objects > HTTP** was used to view all HTTP objects contained in the capture.

<blockquote>
I knew there was a txt file in the capture file, so I navigated to **[File > Export Objects > HTTP]** which allowed me to export all HTTP objects found in this file.
</blockquote>

<p align="left">
  <img src="images/wireshark-packet-analysis-and-filtering-31.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="800"><br>
  <em>Figure 31</em>
</p>

(Phase 3.11, Step 2) — Filter exported objects list to .txt

In the "Text Filter" field, `.txt` was entered to filter the list of HTTP objects to just `.txt` files (or text-file objects).

<p align="left">
  <img src="images/wireshark-packet-analysis-and-filtering-32.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="800"><br>
  <em>Figure 32</em>
</p>

(Phase 3.11, Step 2) — Save the .txt file locally as peter_note.txt

The `.txt` file was saved as `peter_note.txt`.

<p align="left">
  <img src="images/wireshark-packet-analysis-and-filtering-33.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="800"><br>
  <em>Figure 33</em>
</p>

(Phase 3.11, Step 4) — Read exported file contents using cat

Instead of previewing the file in Wireshark, or jumping to a packet number as demonstrated in **Step 11-b** to view it in the **Packet Details Pane**, a Bash terminal was used to read file contents directly.

The following command to read its contents:

```bash
cat peter_note.txt
```

<p align="left">
  <img src="images/wireshark-packet-analysis-and-filtering-34.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="800"><br>
  <em>Figure 34</em>
</p>

Wireshark’s navigation tools make packet inspection much more manageable. Being able to jump directly to relevant packets or mark them for comparison is extremely useful for forensic analysis. Exporting objects or filtered data creates a more efficient workflow for isolating specific traffic without cluttering the main capture file.

<blockquote>
I learned how to move through large captures effectively, mark and comment on key packets, and export relevant data. These functions are essential for documenting and sharing findings in professional investigations.
</blockquote>

</details>

<details>
<summary><strong>▶ Phase 4 — Display Filtering and Session Reconstruction</strong><br>
→ isolating traffic of interest and reconstructing complete conversations
</summary><br>

This phase focuses on isolating traffic of interest and reconstructing complete conversations.

<blockquote>
This section was about understanding and applying packet filtering within Wireshark to isolate traffic of interest. I wanted to practice using display filters to view only relevant protocols or hosts.
</blockquote>

##### 🔷 Phase 4.1 — Apply display filters via Apply as Filter

The **Analyze → Apply as Filter** workflow was used to generate a filter expression directly from a selected field. The source field (source IP) of **Packet #1** was selected in the Packet List Pane, then **Apply as Filter → Selected** was applied.

<blockquote>
I practiced using Wireshark’s [Analyze] > [Apply as Filter] feature to isolate specific network traffic from a capture file. While analyzing packets, I selected the "Source" field (source IP) of Packet #1 from the "Packet List Pane" and right-clicked to choose [Apply as Filter → Selected] (alternatively available from the [Analyze] menu). 
</blockquote>

<p align="left">
  <img src="images/wireshark-packet-analysis-and-filtering-35.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="800"><br>
  <em>Figure 35</em>
</p>

Wireshark automatically generated the appropriate display filter based on the selected field and applied it to the capture. Wireshark generated and applied:

`ip.src == 145.254.160.237`

This same expression could be entered manually into the display filter bar.

<p align="left">
  <img src="images/wireshark-packet-analysis-and-filtering-36.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="800"><br>
  <em>Figure 36</em>
</p>

<blockquote>
The generated filter expression was "ip.src == 145.254.160.237", which I could have also typed manually into the display filter bar tp achieve the same result. Similarly, I could apply filters to other fields such as destination IP addresses, timestamps, or any other protocol-specific values to narrow down the traffic even further.
</blockquote>

After filtering, only matching packets remained visible in the Packet List Pane. The status bar was used to compare total vs displayed packet counts to understand filter scope impact.

<blockquote>
This made it easier to focus on relevant traffic for deeper analysis. I also noted that Wireshark’s status bar shows both the total number of packets in the capture and the number currently displayed after filtering—helpful for quickly gauging how much traffic the filter excluded.
</blockquote>


##### 🔷 Phase 4.2 — Apply a conversation filter to isolate a full TCP session

Conversation filtering was used to capture the full packet flow for a specific session rather than isolating a single field.

<blockquote>
I learned how to create [Conversation Filters] to follow specific TCP or UDP streams, showing all packets related to one session. While the [Apply as Filter] option is useful for isolating a single field—such as a specific IP address, timestamp, or destination value, the [Conversation Filter] goes a step further by capturing the entire flow of related packets.
</blockquote>

For testing, a TCP packet (**Packet #2**) was selected and **Conversation Filter** was opened from the right-click menu (also available under Analyze). Multiple protocol options were visible, including Ethernet, IPv4, TCP, and others.

<blockquote>
Since the selected packet was a TCP segment over IPv4, only the relevant filters (IPv4 and TCP) were active, while unsupported protocols like IPv6 and UDP were greyed out. 
</blockquote>

<p align="left">
  <img src="images/wireshark-packet-analysis-and-filtering-37.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="800"><br>
  <em>Figure 37</em>
</p>

After selecting **[TCP]**, Wireshark automatically applied a filter showing only packets belonging to that specific TCP conversation between the two endpoints.

<p align="left">
  <img src="images/wireshark-packet-analysis-and-filtering-38.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="800"><br>
  <em>Figure 38</em>
</p>

This automatically displayed only the packets that shared the same IP addresses and port numbers, effectively showing the complete conversation between the two endpoints. This method makes it easier to follow communication streams, identify request–response patterns, and analyze the overall interaction between hosts without the distraction of unrelated traffic.

##### 🔷 Phase 4.3 — Highlight a conversation without filtering using Colorize Conversation

Colorize Conversation was used to visually trace a conversation while leaving unrelated packets visible. A TCP packet (**Packet #3**) was selected and the conversation was colorized via **Colorize Conversation → TCP → Color 4**.

<blockquote>
I learned how to visually highlight related packets in Wireshark using the **[Colorize Conversation]** feature. Instead of applying a display filter that hides unrelated traffic, this feature assigns a unique color to all packets belonging to the same communication flow, allowing me to easily follow the conversation while keeping the rest of the capture visible.
</blockquote>

<p align="left">
  <img src="images/wireshark-packet-analysis-and-filtering-39.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="800"><br>
  <em>Figure 39</em>
</p>

<blockquote>
As shown in the screenshot, I right-clicked on a TCP packet (packet #3) and navigated to [Colorize Conversation → TCP → Color 4]. 
</blockquote>

Wireshark highlighted all packets in the TCP stream (between `source 145.254.160.237` and `destination 65.208.228.223`) using the selected color.

<p align="left">
  <img src="images/wireshark-packet-analysis-and-filtering-40.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="800"><br>
  <em>Figure 40</em>
</p>

Reset behavior was noted: **View → Colorize Conversation → Reset Colorization** restores default coloring.

<blockquote>
This feature works alongside Wireshark’s Coloring Rules, but it overrides them temporarily to display the manually applied color. If I wanted to revert to the default view, I could simply use [View → Colorize Conversation → Reset Colorization].
</blockquote>

Overall, the Colorize Conversation option is a quick and non-intrusive way to highlight all packets within a conversation without filtering or hiding others—useful when analyzing multiple simultaneous connections in a busy network capture.


##### 🔷 Phase 4.4 — Build filters iteratively using Prepare as Filter

[Prepare as Filter] was used to construct a filter expression without applying it immediately, enabling controlled edits and incremental composition.

<blockquote>
I used [Prepare as Filter] to build a filter expression before activating it, giving me more flexibility. I practiced using Wireshark’s [Prepare as Filter] feature to build custom display filters step by step.
</blockquote>

Prepare as Filter was used to construct a filter expression without applying it immediately, enabling controlled edits and incremental composition.

A source IP field from **Packet #5** was selected and **Prepare as Filter → Selected** was applied.

<p align="left">
  <img src="images/wireshark-packet-analysis-and-filtering-41.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="800"><br>
  <em>Figure 41</em>
</p>

This automatically generated the corresponding filter expression and displayed it in the **Display Filter** toolbar without applying it immediately. This produced `ip.src == 65.208.228.223` in the filter bar without executing it.

<p align="left">
  <img src="images/wireshark-packet-analysis-and-filtering-42.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="800"><br>
  <em>Figure 42</em>
</p>

<blockquote>
As shown in the screenshot, I right-clicked on a packet and selected [Prepare as Filter → Selected]. Wireshark automatically generated the filter expression "ip.src == 65.208.228.223" and displayed it in the filter bar without executing it. This allowed me to adjust or expand the expression before applying it. Unlike [Apply as Filter], this option lets me modify or expand the expression before executing it. 
</blockquote>

To narrow the capture down further by including packets associated with the destination address. The destination IP was right-clicked and **[Prepare as Filter → …and Selected]** was selected.

<p align="left">
  <img src="images/wireshark-packet-analysis-and-filtering-43.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="800"><br>
  <em>Figure 43</em>
</p>


[Selected] -> Displays filter for the field I clicked

[Not Selected] -> Creates excludes field, meaning packets that don't match the selected value
  
[...and Selected] -> Appends the new field to the existing one using the AND operator, so Wireshark displays packets that meet both criteria

[...or Selected] -> Appends the new field with an OR operator, showing packets that match one of the filter fields

[...and Not Selected] -> Appends the condition to exclude certain traffic while keeping others, using the AND NOT operator

[...or Not Selected] -> Appends the condition that includes packets that match either the original field or anything that does not match the new one
</blockquote>

Final composed filter: `(ip.src == 65.208.228.223) && (ip.dst == 145.254.160.237)`

This appended the destination IP (`ip.dst == 145.254.160.237`) to the existing expression, which could have also been entered manually.

<p align="left">
  <img src="images/wireshark-packet-analysis-and-filtering-44.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="800"><br>
  <em>Figure 44</em>
</p>

After finalizing the expression, the filter was applied by pressing [Enter] to apply the filter and view only the matching packets in the **Packet List Pane**.

<p align="left">
  <img src="images/wireshark-packet-analysis-and-filtering-45.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="800"><br>
  <em>Figure 45</em>
</p>

<blockquote>
This method gave me greater control over how filters were built and applied, making it ideal for constructing precise and complex filtering logic without immediately altering the current packet view.
</blockquote>

##### 🔷 Phase 4.5 — Add protocol fields as columns using Apply as Column

**[Apply as Column]** was used to create a new "Packet List Pane" column based on a selected field. A "Source Port" field from "Packet #6" was selected in the "Packet List Pane" and applied as a column.

<blockquote>
I explored Wireshark’s [Apply as Column] feature, which allowed me to add custom fields as columns in the "Packet List Pane" for easier comparison and visibility. By default, the packet list shows basic information such as "No.*", "Time", "Source", "Destination", "Protocol", "Length", and "Info", but adding custom columns helps highlight specific values of interest.
</blockquote>

To test this, the field "Source Port" was right-clicked for packet #6 within the "Packet Details Pane" and **[Apply as Column]** was selected (also accessible from **[Analyze → Apply as Column]**). 

<p align="left">
  <img src="images/wireshark-packet-analysis-and-filtering-46.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="800"><br>
  <em>Figure 46</em>
</p>

Wireshark then created a new column at the end of the packet list, showing the selected field’s (**Source Port**) value for every packet in the capture.

<p align="left">
  <img src="images/wireshark-packet-analysis-and-filtering-47.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="800"><br>
  <em>Figure 47</em>
</p>

<blockquote>
I could apply other fields such as TTL (Time To Live) or HTTP hostnames as columns to monitor patterns in traffic behavior. Columns can also be rearranged, hidden, or disabled by clicking the column headers and adjusting the visibility settings. Overall, this feature is extremely useful for customizing the view to focus on specific protocol details or identifiers—especially during traffic correlation, anomaly detection, or performance analysis.
</blockquote>


##### 🔷 Phase 4.6 — Reconstruct session content using Follow TCP Stream

Follow Stream was used to reconstruct the full application-layer conversation as continuous text rather than individual packets.

<blockquote>
I practiced using the [Follow TCP Stream] feature, which reconstructs an entire conversation (e.g., HTTP request/response). This view displayed both client and server data in plain text, color-coded by direction.
</blockquote>

<blockquote>
I used Wireshark’s [Follow Stream] feature to reconstruct and analyze data exchanged at the application level. While Wireshark normally displays traffic in packet form, following a stream allowed me to view the full conversation between client and server as it appeared in raw form. This helps visualize the data in a continuous sequence rather than as individual packets, making it easier to understand the overall context of the communication.
</blockquote>

To test this, a TCP packet (Packet #7) was selected and **[Follow TCP Stream]** was used.

<p align="left">
  <img src="images/wireshark-packet-analysis-and-filtering-48.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="800"><br>
  <em>Figure 48</em>
</p>

Wireshark opened a separate dialogue window showing the entire conversation, with client-originated packets highlighted in red and server-originated packets in blue. This color distinction clearly identified which side each message came from. The stream window displayed the full conversation with client-originated data highlighted in red and server-originated data highlighted in blue.

<blockquote>
I learned I could also search for key terms from the **Find** text bar.  
</blockquote>

<p align="left">
  <img src="images/wireshark-packet-analysis-and-filtering-49.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="800"><br>
  <em>Figure 49</em>
</p>

Wireshark automatically applied a display filter to isolate only packets in the selected stream. To restore all packets, the filter was cleared using the “X” control on the right side of the Display Filter bar.

<blockquote>
I noticed that the packet count in the status bar changed to reflect only the packets from that stream. To return to the full capture view, I clicked the “X” button on the right side of the **Display Filter** bar, which cleared the filter and restored all packets.
</blockquote>

<p align="left">
  <img src="images/wireshark-packet-analysis-and-filtering-50.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="800"><br>
  <em>Figure 50</em>
</p>

**[Follow Stream]** was explicitly recognized as particularly useful for unencrypted protocols (for example, HTTP) where sensitive content may be visible in plaintext.

Overall, this tool is essential for reconstructing and understanding the content and sequence of communications at the application layer.

Filtering drastically improves visibility in large datasets. The ability to highlight or isolate specific streams helped me identify communication patterns, such as repeated requests between hosts. The **[Follow Stream]** feature was especially powerful because it reassembled conversations at the application level, allowing me to read HTTP requests and responses like chat logs.

<blockquote>
I learned how to construct and apply Wireshark filters efficiently. Understanding display filters and stream following is essential for analyzing targeted communications and identifying potential issues or malicious behaviors in network traffic.
</blockquote>

</details>


---

### Results & Interpretation

This workflow confirms that Wireshark can be used as a repeatable, evidence-first process for protocol understanding, session reconstruction, and artifact extraction when working from stored packet captures.

This workflow also demonstrated the core capabilities of Wireshark and how it simplifies packet analysis through its graphical interface. I learned to capture, dissect, and filter network traffic while becoming familiar with the structure of PCAP files. Each exercise provided deeper insight into how different layers of network communication interact.

- **Offline analysis is a viable default.** Many environments do not permit live sniffing due to interface constraints, permissions, or access limitations on hardened endpoints and jump hosts. Practicing with stored PCAP/PCAPNG artifacts ensures the workflow remains usable in restricted incident-response conditions. It also improves repeatability because the same dataset can be reviewed by multiple analysts and re-validated later.

- **Byte-level validation improves confidence in interpretation.** Wireshark’s decoded fields are extremely useful, but defensible analysis often requires validating how a field is represented in raw bytes. The repeated use of field selection (Details pane) and byte highlighting (Bytes pane) reinforces that the interpretation is grounded in the actual captured data. This approach is especially valuable when documenting findings, troubleshooting unusual parsing behavior, or explaining packet structure to stakeholders.

- **Session reconstruction becomes the “story layer” for reporting.** Large captures are difficult to interpret from the packet list alone, even with strong filtering. Conversation filters and Follow Stream views provide a coherent narrative (request → response, client → server) that maps more naturally to investigation questions. This makes it easier to communicate what happened, in what order, and what content was exchanged.

- **Annotation and export enable collaboration without losing evidentiary context.** Marking packets provides a fast, temporary way to keep investigative focus while exploring; packet comments provide durable, in-file documentation that persists for future reviewers. Exporting objects and exporting specified packets allow analysts to share a minimal, relevant subset (for example, a single HTTP stream or extracted file) while retaining the original capture as the authoritative evidence source.

- **Expert Information provides triage hints, not conclusions.** Expert Information flags anomalies and noteworthy conditions, but those signals need analyst validation in context (protocol, stream, timing, and surrounding packets). Treating Expert Information as a prioritization mechanism helps focus review without over-trusting automated labels. This reduces the risk of false positives and supports more accurate conclusions.


---

### Operational & Defensive Takeaways

Wireshark is an indispensable tool for both network troubleshooting and cybersecurity investigations. It allowed me to visualize how data moves through various layers, identify anomalies, and reconstruct conversations in real time. Compared to command-line tools, Wireshark offers an intuitive way to interpret complex network behavior.

Wireshark becomes most valuable in operations when it is treated as an evidence workbench rather than just a packet viewer. The workflow above shows a repeatable method: confirm capture context, narrow to the session that matters, validate protocol behavior at the field and byte level, and then extract sharable artifacts that can be reviewed by others.

- **Establish capture context early (before interpreting packets).** Capture metadata such as timestamps, interface information, packet counts, and file hashes helps prevent misinterpretation and supports chain-of-custody style documentation. In practice, this is the difference between confidently saying “this HTTP response occurred at X time in this capture” versus guessing. The Capture File Properties view is also where it becomes obvious whether a capture is partial or merged, which influences how conclusions should be phrased.

- **Use session-level scoping to reduce noise without losing evidence.** Conversation filters and Follow Stream convert a noisy packet list into an interpretable request/response narrative. This is operationally important because most incident questions are session questions (“what happened in this connection?”), not packet questions. Keeping both approaches available (filtering vs. colorizing) allows analysts to either isolate the session entirely or keep broader context visible when multiple sessions overlap.

- **Validate key claims by correlating decoded fields to raw bytes.** Wireshark’s automatic decoding is extremely helpful, but investigations are stronger when the “what Wireshark says” view can be traced to the “what was on the wire” bytes. The repeated use of field selection and byte highlighting builds that muscle: source/destination addresses, ports, flags, and application payload boundaries can all be confirmed directly. This habit matters when explaining findings to engineers, writing reports, or troubleshooting edge cases like malformed traffic.

- **Treat Expert Information as triage guidance, not a verdict.** Expert Information can quickly surface malformed packets, sequence anomalies, and protocol warnings, which is useful for prioritization in large captures. However, those signals need to be validated in the packet details and stream context because “warning” categories can be triggered by benign artifacts (capture loss, offloading, retransmits, etc.). Operationally, the feature should be used to generate a review queue, not to label traffic as malicious.

- **Prefer portable artifacts for collaboration and escalation.** Marking and commenting are documentation mechanisms inside the capture: marks help personal triage during a session, comments persist for reviewers. Export Specified Packets creates a reduced PCAP that can be shared without the full noise of the original capture, and Export Objects allows retrieval of transferred files (e.g., HTTP-delivered content) for malware triage. Pairing exported artifacts with hashes (MD5/SHA-256) supports integrity checks when files move between systems or analysts.

---

### Reuse Pack (Quick Reference)

#### ▶ Wireshark panes (how to use them together)

- **Packet List Pane:** Use this pane to scan and triage at speed: identify protocol mix, source/destination patterns, and time clustering. It is also the primary navigation surface for “jumping around” a capture once a hypothesis exists (for example, focusing on a specific server IP or port). Treat it as the index of the evidence set.

- **Packet Details Pane:** Use this pane to interpret a selected packet as layered protocol structures (Ethernet → IP → TCP → HTTP). This is where fields like TTL, flags, sequence/ack numbers, content type, and response codes can be validated. It is the core pane for explaining *why* a packet is significant.

- **Packet Bytes Pane:** Use this pane to validate claims by mapping decoded fields to raw hex/ASCII. When documenting findings, this supports defensibility because it shows the exact bytes on the wire that correspond to a field. It is also useful for spotting recognizable magic bytes (for example, JPEG Start of Image) and verifying reassembled payload content.

#### ▶ Practical navigation (controls that reduce time-to-answer)

- **Go to Packet:** Use deterministic jumps when a packet number is known from another reference point (for example, Expert Information, exported object list, or collaborator notes). This avoids scrolling and helps reviewers land on the same artifact quickly.

- **Edit → Find Packet:** Use content-based search when packet numbers are not known. Select the correct input type (display filter, hex, string, regex) and ensure the search scope targets the correct pane (List, Details, or Bytes), otherwise searches can miss the intended data.

- **Mark packet(s):** Use marking as a temporary “bookmarking” mechanism during triage and comparison (for example, flagging the boundaries of a session or packets that represent a key transition). Because marks clear on close, they are best used for short-lived working sets.

- **Packet Comment:** Use comments for durable documentation embedded directly in the capture file. This supports collaboration and later review because notes remain attached to the exact packet(s) they reference.

#### ▶ Evidence extraction (how to preserve and share what matters)

- **File → Export Objects:** Use this to recover transferred files from supported protocols (HTTP, SMB, TFTP, etc.). After exporting, treat the file as an artifact: store it with a clear name, compute hashes, and validate integrity before deeper analysis.

- **File → Export Specified Packets:** Use this to create smaller, purpose-built PCAPs containing only displayed, selected, marked, or ranged packets. This is useful for sharing with peers, attaching to tickets, or isolating a single session for deeper tooling.

- **Capture File Properties:** Use this to verify capture context (duration, packet count, interface, file format) and to record file hashes for chain-of-custody style documentation. This helps ensure analysis conclusions are grounded in a known dataset and supports reproducibility.

#### ▶ Filtering and reconstruction (how to isolate a conversation)

- **Apply as Filter:** Use this for quick isolation of a value discovered during inspection (for example, a specific source IP). It generates the correct expression automatically, reducing syntax errors and speeding up iteration.

- **Conversation Filter:** Use this when the unit of analysis is the full session (same endpoints and ports), not a single field. This is especially useful for understanding request/response behavior and identifying session boundaries.

- **Prepare as Filter:** Use this when building more complex logic and when filter accuracy matters. It allows incremental construction (AND/OR/NOT) and review before application, reducing the risk of accidentally hiding relevant packets.

- **Follow → TCP Stream:** Use this to reconstruct application-layer conversation content and to produce a readable narrative of the session. It is particularly valuable for unencrypted protocols like HTTP, where the request/response and transferred objects may be directly observable.

---

### What I Learned (Skills Demonstrated)

Through this workflow, I learned how to:
- Navigate Wireshark’s GUI and analyze captured packets.
- Dissect protocols across all OSI layers.
- Apply and customize display filters to isolate traffic of interest.
- Follow conversations and export relevant data for documentation.

Overall, this workflow exercise strengthened my foundational understanding of network analysis and gave me the confidence to use Wireshark for real-world packet investigation scenarios.

- Practical Wireshark evidence handling: loading PCAPNG files, merging captures, and validating capture metadata and hashes.
- Protocol reasoning across OSI layers: interpreting Ethernet, IP, TCP, and HTTP fields and understanding how layers nest.
- Field-to-byte correlation: validating decoded protocol fields through direct byte highlighting.
- Dataset navigation: using packet numbers, search primitives, marking, and persistent comments to manage large captures.
- Evidence extraction and integrity practices: exporting objects from traffic streams and validating artifacts with cryptographic hashes.
- Precision filtering and conversation analysis: applying display filters, building logical expressions, isolating conversations, and reconstructing sessions with Follow Stream.

