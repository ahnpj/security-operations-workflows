# Tool Usage Notes — Targeted Packet Capture and Traffic Filtering Using tcpdump

This document explains how `tcpdump` and supporting Linux shell utilities were used to perform targeted packet capture, offline replay, and protocol-level inspection. The emphasis is on deliberate scoping of network telemetry rather than indiscriminate capture, mirroring how analysts collect evidence during incident response and network investigations.

All tooling used in this execution is native to Linux systems and requires no graphical interfaces or third‑party analyzers. This reflects real-world scenarios where analysts often have only SSH access to servers, network appliances, or cloud instances during triage and must rely on lightweight command‑line tools.

> **Workflow vs Execution vs Writeup (Terminology Used Here)**  
> - **Workflows** refer to operational security tasks such as network telemetry collection, traffic scoping, and validation of suspected communication paths.  
> - **Executions** refer to the hands-on performance of those tasks using live interfaces, packet capture utilities, and stored packet artifacts.  
> - **Writeups** document how capture strategies were applied, how filters were constructed, and how results were interpreted in an investigative context.

---

### Execution Platform and Operating Environment

#### ▶ Remote Linux Host (Cloud-Based Virtual Machine)

**Purpose:** Provide a realistic server environment for practicing command-line packet capture and protocol analysis.  
**How It Was Used:** All packet captures and offline analysis were executed directly on the VM using SSH terminal sessions. Network traffic was observed on the active Ethernet interface and stored locally for replay.  
**Operational Relevance:** Mirrors common SOC and IR scenarios where analysts must investigate network behavior on production servers without graphical tooling.

Environment characteristics during execution:

- **Operating System:** Ubuntu Linux (20.04 LTS)  
- **Kernel:** Linux 5.15.x (cloud-optimized kernel build)  
- **Shell:** Bash  
- **Access Method:** SSH remote terminal  
- **Primary Interface:** `ens5` (cloud network interface)  
- **Addressing:** Dynamically assigned private IP ranges typical of cloud VPC networks  

Cloud environments generate background traffic related to metadata services, monitoring agents, and internal routing, which influences baseline packet patterns and reinforces the need for filtering.

---

### Interface Enumeration and Capture Prerequisites

Before capturing traffic, analysts must confirm which interfaces are active and carrying traffic.

#### ▶ `ip address show` — Interface Identification

```bash
ip a s
```

**Purpose:** Display all network interfaces and assigned IP addresses.  
**How It Was Used:** Identify `ens5` as the active Ethernet interface carrying inbound and outbound traffic.  
**Operational Relevance:** Prevents capturing on inactive, loopback, or management-only interfaces, which would produce misleading results.

Correct interface selection is critical in cloud environments where interface naming conventions differ across providers and instance types.

---

### Baseline Capture and Telemetry Validation

Initial captures should confirm that traffic is observable before applying narrow filters.

#### ▶ Bounded Capture on Active Interface

```bash
sudo tcpdump -i ens5 -c 5 -n
```

**Purpose:** Validate that packet capture is functioning while keeping output manageable.  
**How It Was Used:** Confirmed that live packets were visible on `ens5` without generating excessive terminal output.  
**Operational Relevance:** Establishes baseline telemetry availability before escalating to longer or more targeted captures.

Command controls:

- `sudo` — required for raw packet access  
- `-i ens5` — binds capture to the selected interface  
- `-c 5` — stops capture automatically after five packets  
- `-n` — disables DNS and service-name resolution to reduce noise and latency

This pattern is commonly used during triage to avoid flooding consoles or writing oversized capture files.

---

### Writing and Replaying Packet Artifacts

Packet captures are most useful when preserved as artifacts that can be replayed, shared, and correlated with other telemetry sources.

#### ▶ Writing Packets to File

```bash
sudo tcpdump -i ens5 -w data.pcap
```

**Purpose:** Store raw packets for offline inspection and evidence preservation.  
**How It Was Used:** Demonstrated how live traffic can be written to `.pcap` format for reuse.  
**Operational Relevance:** Enables later analysis using both CLI and GUI tools and supports collaborative investigations.

`.pcap` files are portable and widely supported across network forensic tools.

#### ▶ Reading Packets from File

```bash
tcpdump -r traffic.pcap
```

**Purpose:** Replay previously captured traffic without relying on live conditions.  
**How It Was Used:** All advanced filtering and display-mode analysis stages were performed using stored capture files.  
**Operational Relevance:** Allows repeatable analysis and consistent reproduction of investigative results.

Offline replay is especially useful when investigating short-lived network events.

---

### Host, Port, and Protocol Scoping

Effective packet capture requires narrowing scope to traffic relevant to the investigative hypothesis.

#### ▶ Host-Based Filtering

```bash
sudo tcpdump host example.com -w http.pcap
```

**Purpose:** Capture only packets where the source or destination matches a specific host.  
**How It Was Used:** Demonstrated how domain-based filters constrain capture scope.  
**Observed Outcome:** Zero packets captured due to absence of matching traffic, validating filter correctness rather than indicating failure.

Empty captures are meaningful when testing hypotheses and verifying that traffic is not present.

#### ▶ Port-Based Filtering (DNS Example)

```bash
sudo tcpdump -i ens5 port 53 -n
```

**Purpose:** Isolate traffic associated with a specific service.  
**How It Was Used:** Observed DNS query and response behavior when present.  
**Operational Relevance:** Useful for validating external dependencies, suspicious domain lookups, or tunneling over DNS.

Port filters are commonly paired with protocol detection logic in IDS and SIEM rules.

#### ▶ Protocol-Based Filtering (ICMP Example)

```bash
sudo tcpdump -i ens5 icmp -n
```

**Purpose:** Capture only ICMP traffic such as ping requests and replies.  
**How It Was Used:** Demonstrated how protocol isolation works in low-activity environments.  
**Observed Outcome:** No ICMP traffic during capture window, reinforcing correct filter behavior.

ICMP filtering is useful for detecting scanning activity and connectivity testing by attackers.

#### ▶ Logical Operator Combinations

```bash
tcpdump -i ens5 host example.com and tcp port 443 -w https.pcap
```

**Purpose:** Apply multiple conditions simultaneously using BPF logic.  
**How It Was Used:** Demonstrated how HTTPS traffic to specific hosts could be isolated.  
**Operational Relevance:** Enables precise hypothesis-driven packet collection.

Supported operators include `and`, `or`, and `not`, allowing complex filter construction.

---

### Offline Analysis and Quantitative Pivots

Once traffic is captured, analysts often perform fast quantitative checks before deeper inspection.

#### ▶ Counting Packets from a Source Host

```bash
tcpdump -r traffic.pcap src 192.168.124.1 -n | wc -l
```

**Purpose:** Estimate packet volume originating from a specific IP.  
**How It Was Used:** Identify dominant senders within the capture file.  
**Operational Relevance:** Helps identify beaconing hosts, scanners, or data exfiltration sources.

Line counts serve as a quick proxy for packet volume in summary output modes.

#### ▶ Counting Protocol-Specific Packets

```bash
tcpdump -r traffic.pcap icmp -n | wc
```

**Purpose:** Quantify protocol usage within the capture.  
**How It Was Used:** Determine relative volume of ICMP traffic.  
**Operational Relevance:** Supports detection of scanning or diagnostic abuse.

---

### ARP and Local Network Correlation

ARP analysis enables mapping between IP addresses and physical MAC identities.

#### ▶ Identify ARP Requestors

```bash
tcpdump -r traffic.pcap arp and host 192.168.124.137
```

**Purpose:** Determine which host requested MAC resolution for a target IP.  
**How It Was Used:** Identified the querying host for the target IP address.  
**Operational Relevance:** Supports lateral movement analysis and device attribution on local segments.

#### ▶ Extract MAC Addresses

```bash
tcpdump -r traffic.pcap arp -e
```

**Purpose:** Display Ethernet headers including MAC addresses.  
**How It Was Used:** Identify sending and receiving hardware addresses in ARP exchanges.  
**Operational Relevance:** Critical for investigations where IP spoofing or address reuse is suspected.

---

### Packet Size-Based Filtering

Packet length can indicate file transfers, tunneling, or abnormal payloads.

#### ▶ Live Large-Packet Filtering

```bash
tcpdump -i ens5 greater 1000
```

**Purpose:** Display packets exceeding size thresholds.  
**How It Was Used:** Identify traffic potentially associated with data transfer.  
**Operational Relevance:** Supports detection of bulk transfers and potential exfiltration.

#### ▶ Offline Large-Packet Identification

```bash
tcpdump -r traffic.pcap 'greater 15000' -n
```

**Purpose:** Identify hosts transmitting unusually large frames.  
**How It Was Used:** Attribute oversized packets to specific IPs.  
**Operational Relevance:** Helps surface anomalies that may bypass typical IDS thresholds.

---

### Header Byte and Bitwise Filtering

Advanced filtering sometimes requires inspecting specific header bits.

#### ▶ Multicast Detection via Ethernet Header

```bash
sudo tcpdump -i ens5 'ether[0] & 1 != 0'
```

**Purpose:** Identify packets sent to multicast addresses.  
**How It Was Used:** Combined with host filters to isolate multicast traffic.  
**Technical Basis:** Evaluates least significant bit of destination MAC address.

Bitwise operations enable protocol-aware filtering beyond standard field matching.

---

### TCP Flag Analysis

TCP flag states reveal connection behavior and potential scanning or DoS activity.

#### ▶ SYN-Only Packets

```bash
tcpdump -i ens5 'tcp[tcpflags] == tcp-syn'
```

**Purpose:** Capture initial connection attempts only.  
**How It Was Used:** Attempted to observe handshake initiation.  
**Observed Outcome:** No matches due to low traffic volume.

#### ▶ SYN Present (Bitwise Match)

```bash
tcpdump -i ens5 'tcp[tcpflags] & tcp-syn != 0'
```

**Purpose:** Capture packets where SYN is set regardless of other flags.  
**How It Was Used:** Observe broader handshake-related traffic.

#### ▶ SYN or ACK Present

```bash
tcpdump -i ens5 'tcp[tcpflags] & (tcp-syn|tcp-ack) != 0'
```

**Purpose:** Capture packets involved in connection establishment and acknowledgment.  
**How It Was Used:** Visualize handshake flow behavior.

#### ▶ RST-Only Packets from Capture File

```bash
tcpdump -r traffic.pcap 'tcp[tcpflags] == tcp-rst' | wc -l
```

**Purpose:** Count abrupt connection resets.  
**How It Was Used:** Quantify RST-only packets in offline analysis.  
**Operational Relevance:** Helps identify rejected connections or scanning artifacts.

---

### Display and Interpretation Modes

Output modes affect how analysts interpret packet contents.

#### ▶ Basic Summary Output

```bash
tcpdump -r traffic.pcap -c 2
```

Displays timestamps, IPs, ports, flags, and packet lengths.

#### ▶ Quick Mode

```bash
tcpdump -r traffic.pcap -q -c 2
```

Shows only flow-level metadata, useful for fast scanning.

#### ▶ Link-Layer Headers

```bash
tcpdump -r traffic.pcap -e -c 2
```

Adds MAC addresses and Ethernet framing, supporting Layer‑2 investigations.

#### ▶ ASCII Payload View

```bash
tcpdump -r traffic.pcap -A -c 2
```

Useful for plaintext protocols; encrypted payloads appear unreadable as expected.

#### ▶ Hexadecimal Payload View

```bash
tcpdump -r traffic.pcap -xx -c 2
```

Supports low-level protocol and payload inspection.

#### ▶ Combined Hex and ASCII

```bash
tcpdump -r traffic.pcap -X -c 2
```

Allows correlation of raw bytes with readable segments without switching modes.

---

### Operational Safety and Evidence Handling

- Avoid unfiltered long captures on production systems.  
- Use `.pcap` files to preserve raw evidence.  
- Document capture windows and filter logic.  
- Minimize collection of sensitive payloads.

Proper scoping supports both privacy protection and efficient investigation.

---

### Extension into Detection and Monitoring

Packet capture supports detection engineering by validating:

- IDS rule logic  
- SIEM network correlations  
- Zeek behavioral telemetry  
- Firewall and proxy logs

Tcpdump often serves as the ground-truth reference when validating whether detections align with observed traffic.

---

### Summary of Tools, Platforms, and Data Sources

- **Platform:** Remote Linux virtual machine  
- **Shell:** Bash  
- **Packet Capture Tool:** tcpdump (libpcap-based)  
- **Supporting Utilities:** ip, wc, shell pipes  
- **Data Sources:** Live network interfaces and stored `.pcap` files  
- **Execution Method:** Interactive CLI capture and offline replay  
- **Analysis Method:** BPF filtering, protocol inspection, and quantitative pivots

These tools collectively support targeted network triage and forensic validation workflows commonly used in SOC and incident response operations.

