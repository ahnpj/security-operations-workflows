# Targeted Packet Capture and Traffic Filtering Using tcpdump

### Overview

This execution demonstrates targeted packet capture and traffic inspection using `tcpdump` to identify, isolate, and analyze network communications at the packet level. The focus is on capturing specific traffic flows, filtering noise, and interpreting packet data to determine whether observed network activity aligns with expected behavior or indicates potential compromise.

The execution models how analysts use lightweight, command-line packet capture tooling during network triage, threat hunting, and investigation support. It emphasizes precision filtering, capture validation, and protocol-level interpretation to reconstruct communication patterns and identify suspicious connections without relying on graphical analysis tools.

> **Workflow vs Execution vs Writeup (Terminology Used Here)**  
> - **Workflows** refer to operational tasks such as onboarding telemetry and validating parsing behavior.  
> - **Executions** refer to hands-on configuration and validation using real data and Splunk services.  
> - **Writeups** document configuration decisions, troubleshooting steps, and validation results.

> 👉 For a **detailed, step-by-step walkthrough of how this workflow was executed — complete with screenshots**, see the **[Step-by-Step Execution Walkthrough](#step-by-step-execution)** section below.

---

### Purpose and Analyst Focus

#### ▶ Purpose

The purpose of this workflow is to perform targeted packet capture and traffic inspection using `tcpdump` to collect and analyze network traffic directly from a monitored interface. The workflow focuses on capturing specific protocol activity, filtering traffic based on investigative criteria, and validating captured data to support network triage and incident investigation.

The workflow emphasizes how packet-level visibility helps analysts identify suspicious communication patterns, validate potential indicators of compromise, and isolate relevant traffic during alert triage or network-based investigations. It also demonstrates how targeted capture strategies reduce noise and allow analysts to collect only the traffic necessary to answer investigative questions efficiently.

**Note:** Tcpdump is built on the **libpcap** library, which is responsible for capturing packets from network interfaces. On Windows systems, the equivalent library is **WinPcap**. Both provide low-level access to network data, allowing analysts to observe traffic in real time. In this workflow execution, I performed multiple exercises that involved capturing live traffic, saving packet data to `.pcap` files, filtering specific protocols, and learning advanced filtering expressions.

#### ▶ Analyst Focus

The analyst focus is on using packet capture tooling to isolate, inspect, and interpret network communications at the protocol and session level. This includes applying capture filters to control data collection, validating packet contents to confirm network behavior, and using captured traffic to support investigative scoping and detection validation.

The workflow reflects responsibilities commonly performed by SOC analysts, incident responders, and detection engineers when investigating suspicious network activity, validating alerts, or performing host and network triage. It reinforces the importance of understanding protocol behavior, traffic filtering logic, and packet inspection techniques when determining whether observed communications represent legitimate system activity or potential malicious behavior.

---

### What This Workflow Demonstrates

This workflow demonstrates progressive packet capture techniques, moving from baseline validation to increasingly precise filtering and protocol inspection.

Operational capabilities exercised include:

- validating capture prerequisites by identifying active interfaces  
- performing bounded captures to confirm traffic flow without generating excessive output  
- writing packet data to `.pcap` files for reuse and sharing  
- filtering traffic by host, port, and protocol to isolate relevant activity  
- combining multiple filter conditions using logical operators (`and`, `or`, `not`)  
- applying packet-length thresholds to identify unusually large payloads  
- inspecting protocol header bytes using offset-based filters  
- evaluating TCP connection state using flag-based bitwise logic  
- selecting appropriate display modes (summary, link-layer, ASCII, hex) for payload interpretation  

Rather than treating filtering and display options as isolated features, the workflow models how capture scope, filter logic, and output formatting work together to support efficient analysis.

Each execution stage builds on prior findings, reinforcing a pattern of:

**validate → narrow → isolate → replay → interpret**

which reflects how packet capture is used during active investigations and retrospective analysis alike.

---

### Investigation and Detection Relevance

Packet capture remains one of the most direct ways to observe network behavior when logs, sensors, or intrusion detection systems provide incomplete context.

The techniques demonstrated in this workflow map directly to investigative scenarios such as:

- confirming whether hosts are communicating with specific destinations  
- validating whether suspected services are actively transmitting data  
- identifying handshake anomalies or repeated connection attempts  
- detecting unexpected ARP behavior on local networks  
- extracting DNS queries to identify external dependencies or suspicious domains  

Understanding how to apply precise BPF filters is especially important in incident response, where analysts must quickly reduce noise and focus only on traffic relevant to the hypothesis being tested.

The workflow also reinforces how packet captures become durable evidence artifacts. Saved `.pcap` files can be:

- replayed consistently across tools  
- shared with teammates or escalated to forensic teams  
- correlated with firewall, proxy, or EDR telemetry  

making packet capture a foundational capability across detection engineering, SOC triage, and network forensics disciplines.

---

### Environment and Execution Context

This section documents the host environment, access method, and operational constraints under which packet capture and filtering were performed, providing important context for interface behavior, traffic patterns, and capture output.

**Note:** Each section is collapsible. Click the ▶ arrow to expand and view details on software, tools, environment, data sources, and more.

<details>
<summary><strong>▶ Environment & Platform</strong><br>
</summary><br>
  
All commands were executed on a remote Linux host accessed via SSH using a standard terminal session.

Environment characteristics:

- **Operating System:** Ubuntu 20.04.6 LTS  
- **Kernel Version:** 5.15.0-1066-aws (indicating cloud-hosted infrastructure)  
- **Environment Type:** Virtual machine  
- **Access Method:** SSH remote login  
- **Shell:** Bash  
- **Network Interface Observed:** `ens5`  
- **IP Addresses Observed:** dynamically assigned private addresses (e.g., `10.201.x.x`, `10.11.x.x`)  

The cloud-hosted nature of the environment influences traffic patterns, including:

- internal service communication  
- infrastructure-level management traffic  
- dynamic addressing  

which is representative of many production Linux server environments.

<p align="left">
  <img src="images/tcpdump_packet_capture_and_filtering_01.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="800"><br>
  <em>Figure 1</em>
</p>

</details>

<details>
<summary><strong>▶ Tooling and Constraints</strong><br>
</summary><br>

All packet capture and analysis was performed using native Linux utilities without third-party tooling or graphical packet analyzers.

Primary tooling:

- `tcpdump` for capture and filtering  
- `ip` for interface enumeration  
- standard shell utilities (`wc`, pipes) for quick quantitative pivots  

No graphical interfaces, protocol analyzers, or packet visualization tools were used. This constraint reflects realistic scenarios where analysts may only have terminal access to servers or network devices during investigations.

The workflow intentionally avoids reliance on:

- GUI-based packet inspection
- external capture agents
- automated detection tooling

to ensure that all techniques remain portable across environments where only base system utilities are available.

</details>

<details>
<summary><strong>▶ Data Sources Analyzed</strong><br>
</summary><br>
  
The workflow relies on multiple categories of packet evidence and command output:

- **Live interface traffic** observed via `tcpdump` on the selected interface (`ens5`).
- **Captured packet files (`.pcap`)** used for offline analysis and repeatable inspection:
  - Example files referenced: `http.pcap`, `https.pcap`, `traffic.pcap`, and `data.pcap` (used as a naming example for basic capture testing).
- **Terminal output and `tcpdump` capture summaries** produced when stopping capture (`Ctrl + C`), including:
  - Packets captured
  - Packets received by filter
  - Packets dropped by kernel
- **Packet header and payload representation** via `tcpdump` display modes:
  - Default summary output
  - Quick output (`-q`)
  - Link-layer headers (`-e`)
  - ASCII (`-A`)
  - Hex (`-xx`)
  - Combined hex + ASCII (`-X`)

These outputs collectively support both qualitative interpretation (what kind of traffic is present) and quantitative assessment (how much traffic matches specific conditions).

</details>

<details>
<summary><strong>▶ Workflow Map (High-Level)</strong><br>
</summary><br>
            
The workflow follows a progressive investigative sequence designed to mirror practical packet-capture methodology:

1. **Validate capture prerequisites** by identifying available interfaces and selecting the one carrying traffic.  
2. **Perform a bounded live capture** to confirm capture functionality and avoid overwhelming output (packet count limits + numeric output).  
3. **Apply targeted filtering expressions** to isolate traffic by host, port, and protocol; stop and review summaries to confirm scope.  
4. **Pivot to offline analysis** by reading from `.pcap` files and using command piping (`wc`) for quick counts and repeatability.  
5. **Apply advanced BPF logic** including packet-length filters, byte-offset filtering, and TCP flag analysis to infer protocol behavior.  
6. **Tune output formatting** to support analyst interpretation (headers, MAC addresses, plaintext visibility, binary payload inspection).

Each phase is structured around investigative intent rather than tool features, reinforcing that capture strategy should be driven by analytical questions, not by command syntax alone.

</details>

---

### Step-by-Step Execution

This section documents the execution in the same order an analyst would realistically perform it during targeted network traffic triage using `tcpdump`. It begins with interface discovery and baseline traffic visibility, then progresses into filtered packet capture focused on specific hosts, protocols, and ports.

Each step captures both what was executed (capture commands, filters, and packet output) and why it was executed (the investigative question being answered). The execution progresses from broad traffic observation into precision filtering and protocol interpretation, mirroring how analysts isolate suspicious network activity and validate whether traffic patterns represent legitimate communications or potential indicators of compromise.

**Note:** Each section is collapsible. Click the ▶ arrow to expand and view details on software, tools, environment, data sources, and more.

<details>
<summary><strong>▶ 1) — Basic Packet Capture (Interface Validation + Bounded Capture)</strong><br>
→ performing packet captures using Tcpdump, specifying which network interface to listen on, and saving the captured data for later review.
</summary><br>

**Goal:** Understand how to perform basic packet captures using Tcpdump, specify which network interface to listen on, and save the captured data for later review.

##### 🔷 1.1 — Identify available network interfaces

The workflow begins by enumerating network interfaces to determine which interface should be monitored. This reduces the risk of capturing on an inactive interface and confirms interface naming in the environment.

Command executed:

- `ip a s` (short for `ip address show`)

This output included interfaces such as:

- `lo` for loopback
- `ens5` for Ethernet

<p align="left">
  <img src="images/tcpdump_packet_capture_and_filtering_02.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="800"><br>
  <em>Figure 2</em>
</p>

##### 🔷 1.2 — Start a bounded capture on the selected interface

A live capture was initiated on `ens5` to confirm the interface is active and that packet capture is functioning correctly. Capture scope was intentionally bounded to keep output reviewable and to avoid excessive data collection.

Command executed:

- `sudo tcpdump -i ens5 -c 5 -n`

<p align="left">
  <img src="images/tcpdump_packet_capture_and_filtering_03.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="800"><br>
  <em>Figure 3</em>
</p>

**Note:** The command `sudo tcpdump -i ens5 -c 5 -n` told the system to listen on the `ens5` network interface, capture five packets, and display them without converting IPs to hostnames. Running it with sudo gave the necessary root permissions to access the network interface.

Command behavior (what each element enforces operationally):

- `sudo` — required to access packet capture on the interface with the necessary privileges.
- `tcpdump` — starts the capture session.
- `-i ens5` — binds capture to the `ens5` interface.
- `-c 5` — captures exactly five packets, then stops automatically (bounded scope).
- `-n` — prevents hostname resolution (keeps output numeric and avoids DNS-related delays/noise).

Observed outcome:

After execution, the output The output displayed five TCP packets exchanged between local IPs. This confirmed that the `ens5` interface was active and that active and that live traffic could be captured and displayed.

##### 🔷 1.3 — Operational notes captured from this stage

While the bounded capture above validated baseline functionality, the workflow also recognizes the following practical tuning patterns for real use:

- To save packets to a file for later analysis, I used the `-w` flag, such as `sudo tcpdump -i ens5 -w data.pcap`. The file extension `.pcap` allows compatibility with other tools like Wireshark.
- I learned how to read previously captured packets using `tcpdump -r data.pcap`, which replays packets in readable form.
- I used the `-c` option, which stopped recording after a specific number of packets, and
- I used `-n` (tells tcpdump not to resolve DNS) or `-nn` (tells tcpdump not to resolve port names) to prevent hostname and port name lookups, showing only the numeric value.
- Using `-v`, `-vv`, or `-vvv` helped display extra details such as TTL values, window sizes, and protocol flags. For the purposes of this workflow excerise, I used a capture file named `data.pcap` to test these features and better understand how tcpdump works for basic packet analysis.

> Note: The lines above reflect command behaviors and patterns referenced in the workflow context. They remain in the workflow as part of the operational knowledge captured from this stage, including the example `data.pcap` naming used for testing and explanation.

I found that Tcpdump provides full control over how much data I capture and display. It can quickly becomes overwhelming if I do not use filters or limit the capture. Saving captures to files is helpful for detailed analysis later, especially if I need to share results or correlate with intrusion detection tools.
- Using `-n` and `-c` made the capture process much more efficient, and verbosity levels provided flexibility depending on how deep I wanted to go into packet details.

> **Personal Learning Note:**
> I learned how to start and stop packet captures, choose interfaces, and save or replay packet data. These basic Tcpdump skills are the foundation for more advanced filtering and analysis techniques that I used later in the workflow execution.

</details>

<details>
<summary><strong>▶ 2) — Filtering Expressions (Host + Port + Protocol + Offline Review)</strong><br>
→ narrowing capture scope to specific targets on specific hosts, ports, or protocols using Tcpdump filtering expressions
</summary><br>

**Goal:** Focus captures on specific hosts, ports, or protocols using Tcpdump filtering expressions. Without filters, packet captures can be massive and difficult to analyze.

##### 🔷 2.1 — Filter capture by host (domain)

A host-based filter was applied to capture only traffic to and from a specific domain. Captured packets were written to a `.pcap` file for offline review.

Command executed:

- `sudo tcpdump host example.com -w http.pcap`

<p align="left">
  <img src="images/tcpdump_packet_capture_and_filtering_04.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="800"><br>
  <em>Figure 4</em>
</p>

How this capture is constrained:

- `tcpdump` is the command that starts the packet capture session
- `host example.com` filters packets to those where the source or destination matches `example.com`.
- `-w http.pcap` writes the capture to `http.pcap` for later analysis and sharing.

**Note:** In this case, `tcpdump` is listening on the network interface `ens5`, but since there was no actual traffic to or from `example.com`, no packets were recorded. `example.com` is just a placeholder domain used for demonstration. If this has been a live, active domain that my computer was communicating with, `tcpdump` would have displayed real-time capture activity. 

Capture termination and summary review:

Capture was stopped using `Ctrl + C` which also produced a short capture summary of all packets that were captured. The short summary includes the number of packets captured, number of packets received by the filter, and the number of packets dropped be the kernel. 

The capture file (`http.pcap`) represents a portable artifact that can later be opened in tools such as Wireshark for deeper inspection (IP addresses, ports, HTTP requests, etc.) if traffic is present.

<p align="left">
  <img src="images/tcpdump_packet_capture_and_filtering_05.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="800"><br>
  <em>Figure 5</em>
</p>

Directional refinement options for host filtering:

Alternatively filter by direction using
- `src host` to focus only on packets originating from a host.
- `dst host` to focus only on packets destined to a host.

##### 🔷 2.2 — Filter live capture by port (DNS example)

A port-based filter was applied to focus on DNS traffic, which commonly uses port `53`. Name resolution was disabled to preserve numeric output and avoid introducing DNS lookups during observation.

Command executed:

- `sudo tcpdump -i ens5 port 53 -n`

<p align="left">
  <img src="images/tcpdump_packet_capture_and_filtering_06.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="800"><br>
  <em>Figure 6</em>
</p>

Filter enforcement details:

- `tcpdump` is the command that starts the packet capture session
- `-i ens5` specifies the network interface to listen on
- `port 53` specifies the port number
- `-n` basically stops `tcpdump` from resolving IP addresses or port numbers into names, so I see numberic IPs instead

Capture termination and summary review:

I terminated capture using `Ctrl + C` which also produced a short capture summary of all packets that were captured.

<p align="left">
  <img src="images/tcpdump_packet_capture_and_filtering_07.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="800"><br>
  <em>Figure 7</em>
</p>

Directional refinement options for port filtering:

Alternatively use:
- `src port` to focus on packets originating from a port.
- `dst port` to focus on packets destined for a port.

##### 🔷 2.3 — Filter live capture by protocol (ICMP example)

Protocol filtering can isolate traffic patterns such as ping requests and replies. ICMP is often relevant for connectivity troubleshooting, scanning detection, and baseline host behavior review.

I filtered by protocol using the command below to capture only ICMP traffic, which showed ping requests and replies:

- `sudo tcpdump -i ens5 icmp -n`

<p align="left">
  <img src="images/tcpdump_packet_capture_and_filtering_08.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="800"><br>
  <em>Figure 8</em>
</p>

Filter enforcement details:

- `tcpdump` is the command that starts the packet capture session
- `-i ens5` specifies the network interface to listen on
- `icmp` specifies the protocol so that the capture only shows ICMP packets
- `-n` basically stops `tcpdump` from resolving IP addresses or hostnames into names, so I see numberic versions instead

Capture termination and summary review:

Again, I terminated capture using `Ctrl + C` which also produced a short capture summary of all packets that were captured.

<p align="left">
  <img src="images/tcpdump_packet_capture_and_filtering_09.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="800"><br>
  <em>Figure 9</em>
</p>

Observed outcome context:

- The environment did not generate ICMP packets during this window, so capture output showed `0` packets.
- In an active environment, ICMP filtering would display real-time ICMP traffic if present.

**Note:** If this has been a live network interface that my computer was communicating with, `tcpdump` would have displayed real-time capture activity. For demonstration only, it captured 0 packets. I could also combine multiple filters with logical operators like `and`, `or`, and `not` to be more specific. For example, `tcpdump tcp and port 80` captures only HTTP packets, `tcpdump udp or icmp` captured UDP or ICMP traffic if at least one of the conditions is true, and adding other conditions like `not port 22` excludes SSH traffic.

##### 🔷 2.4 — Combine filters using logical operators

Filters can be combined to narrow capture scope further. Logical operators supported in `tcpdump` filtering include `and`, `or`, and `not`.

I could also create a longer filter with multiple conditions such as:

`tcpdump -i ens5 host example.com and tcp port 443 -w https.pcap`. 

This will capture and filter traffic going to and coming from `example.com` that uses `tcp` and `port 443`, which is for filtering HTTPS traffic.

- `tcpdump` will start the capture session
- `-i ens5` will specify the network interface to listen on
- `host example.com` captures traffic going to and coming from `example.com` since the `src port` or `dst port` wasn't defined
- `and` is the logical operator meaning both conditions must be true to capture the packet
- `tcp` specifies the protocol so that the capture only shows TCP packets
- `port 443` specifies the port number, which would be HTTPS
- `-w https.pcap` writes matching traffic to `https.pcap`.

Other examples referenced in workflow context:

- `tcpdump tcp and port 80` captures only TCP traffic on port 80 (HTTP).
- `tcpdump udp or icmp` captures UDP or ICMP traffic where either condition is true.
- `not port 22` excludes SSH traffic from a broader capture scope.

##### 🔷 2.5 — Filter packets from an existing `.pcap` file (source filtering + counting)

Offline analysis was performed by reading from a capture file (`traffic.pcap`) and applying filters to the stored packet data. This supports repeatability and reduces reliance on live traffic timing.

To analyze a previously captured packet file and filter network traffic originating from a specific IP address, I ran the command:

`tcpdump -r traffic.pcap src 192.168.124.1 -n | wc -l`. 

This approach:

- Reads packets from `traffic.pcap` (`-r`).
- Filters for packets where the **source** IP is `192.168.124.1`.
- Disables hostname resolution (`-n`) to keep output numeric.
- Pipes output to `wc -l` to count matching lines (as a quick proxy for packet count in output).

**Note:** I used the `tcpdump` command with the `-r` flag to read packets from an existing capture file (`traffic.pcap`) instead of capturing live traffic. The filter src `192.168.124.1` limited the output to only packets sent from the source IP address `192.168.124.1`. The `-n` option prevented hostname resolution which kept the IPs in numeric form. By piping the output into `wc -l`, I counted how many packets in the file matched this filter, giving me a quick summary of how many transmissions came from that source host.

<p align="left">
  <img src="images/tcpdump_packet_capture_and_filtering_10.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="800"><br>
  <em>Figure 10</em>
</p>

Command components (explicit mapping):

- `tcpdump -r traffic.pcap` read packets from a saved capture file named `traffic.pcap` instead of live traffic
- `src 192.168.124.1` filtered the output to show only packets originating from the IP address `192.168.124.1`
- `-n` disabled hostname lookups so IPs stay numeric.
- `| wc` piped the output into the word count (`wc`) command, which counts the number of lines, words, and characters in the output.

<p align="left">
  <img src="images/tcpdump_packet_capture_and_filtering_11.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="800"><br>
  <em>Figure 11</em>
</p>

Observed output interpretation:

- `910` lines → roughly estimates about 910 packets displayed by `tcpdump` for the specified filter.
- `17415` words printed.
- `140616` characters printed.

The results showed that there were `910` number of lines, which roughly estimates to about 910 packets displayed by `tcpdump`, `17415` total number of words printed in the `traffic.pcap` file, and `140616` total number of individual characters printed in that same file.

For packet analysis, the most relevant number in this output is the first value (`910`), which is reflects the number of packets from `192.168.124.1` in the `traffic.pcap` file.

##### 🔷 2.6 — Additional offline analysis practice (ICMP + ARP + DNS domain extraction)

This sub-stage applies protocol and host filters to `traffic.pcap` to extract specific indicators and validate parsing approaches.

I analyzed captured network traffic using `tcpdump` by filtering specific protocols and identifying key network details such as packet counts, IP addresses, and DNS queries.

(Step 1) — Count ICMP packets in `traffic.pcap`

I analyzed packets in `traffic.pcap` that were using the `ICMP` protocol. To do so, I ran the following command:

`sudo tcpdump -r traffic.pcap icmp -n | wc`

This command:

- Reads packets from `traffic.pcap` (`-r`).
- Filters for ICMP traffic.
- Disables hostname resolution (`-n`).
- Pipes output to `wc` to count lines/words/chars.

**Note:** I used the `-r` flag to read packets from a saved capture file (`traffic.pcap`) and filtered for the `ICMP` protocol, which includes ping requests and replies. The `-n` flag disabled hostname lookups to display numeric IPs. Piping the output into `wc` allowed me to count the results. 

<p align="left">
  <img src="images/tcpdump_packet_capture_and_filtering_12.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="800"><br>
  <em>Figure 12</em>
</p>

Observed output interpretation:

- `26` lines → estimates about 26 ICMP packets displayed by `tcpdump`.
- `358` words printed.
- `2722` characters printed.

The results showed that there were `26` number of lines, which  estimates to about 26 packets displayed by `tcpdump`, `358` total number of words printed in the `traffic.pcap` file, and `2722` total number of individual characters printed in that same file.

(Step 2) — Identify the host requesting a MAC address (ARP pivot)

I analyzed packets in `traffic.pcap` to look for the IP address of the host that asked for the MAC address of `92.168.124.137`. To do so, I ran the following command:

`sudo tcpdump -r traffic.pcap arp and host 192.168.124.137`

This filter:

- Restricts output to ARP traffic (`arp`).
- Further narrows to ARP frames associated with `192.168.124.137`.

I filtered the capture file to display ARP (Address Resolution Protocol) traffic related to the host `192.168.124.137`. ARP is used to map IP addresses to physical MAC addresses on the local network.

<p align="left">
  <img src="images/tcpdump_packet_capture_and_filtering_13.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="800"><br>
  <em>Figure 13</em>
</p>

Observed result:

The ARP request indicated that host `192.168.124.148` was asking for the MAC address of `192.168.124.137`.

(Step 3) — Extract the first DNS query hostname from the capture

I identified the hostname (not IP) that appeared in the first DNS query in the `traffic.pcap` file. To do so, I ran the following command:

`sudo tcpdump -r traffic.pcap port 53 -A`

This command:

- Filters to DNS traffic (`port 53`).
- Uses `-A` to display ASCII output, enabling visibility into domain names queried.

This command filters the capture to show `DNS` traffic (which uses `port 53`) and prints the data in ASCII (`-A`) for readability. This allows viewing the actual domain names being queried.

<p align="left">
  <img src="images/tcpdump_packet_capture_and_filtering_14.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="800"><br>
  <em>Figure 14</em>
</p>

The first DNS query observed in this capture included:

`07:18:24.058626 IP ip-192-168-124-137.eu-west-1.compute.internal.33672 > ip-192-168-124-1.eu-west-1.compute.internal.domain: 39913+ A? mirrors.rockylinux.org. (40)`

This packet capture entry showed that the `mirrors.rockylinux.org` was the hostname that appeared in the first DNS query in the `traffic.pcap` file.

##### 🔷 2.7 — Operational notes from this stage

Filtering made a huge difference in how readable and manageable the packet data was. Instead of seeing thousands of lines of unrelated traffic, I could focus on the specific interactions I cared about. For example, filtering ICMP packets showed how ping operates at the network layer, while filtering port 53 helped me visualize DNS resolution. Logical operators allowed me to build complex yet very precise queries.

I learned how to construct efficient filters to capture only what I needed. In real-world network investigations, this ability to narrow down traffic is crucial. It saves time and focuses analysis on relevant packets, whether for troubleshooting, intrusion detection, or malware analysis.

</details>

<details>
<summary><strong>▶ 3) — Advanced Filtering (Packet Size, Bitwise Logic, Byte Offsets, and TCP Flags)</strong><br>
→ experimenting with more advanced Tcpdump filters, including binary operations, packet length comparisons, and TCP flag analysis.
</summary><br>

This stage focuses on deeper protocol behavior and more precise filtering logic, including packet length conditions, bitwise evaluation of header bytes, and TCP flag-state analysis.

**Objective:**
Experiment with more advanced Tcpdump filters, including binary operations, packet length comparisons, and TCP flag analysis.

##### 🔷 3.1 — Filter packets by length (`greater` / `less`)

Packet size can be a useful discriminator for identifying unusually large payloads, potential file transfers, tunneling patterns, or other anomalies. `tcpdump` supports direct length filtering through the `greater` and `less` keywords.

I used filters like `greater [LENGTH]` and `less [LENGTH]` to display packets based on their size.

(Step 1) — Capture packets larger than a threshold on the default interface

I used length-based filters in tcpdump to capture packets according to their size.

Keywords `greater` and `less` allow filtering packets based on their byte length, regardless of source, destination, or protocol. For example, the following command captures all packets larger than `1000` bytes on the default interface.

`tcpdump greater 1000`

<p align="left">
  <img src="images/tcpdump_packet_capture_and_filtering_15.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="800"><br>
  <em>Figure 15</em>
</p>

Capture termination and summary review was performed using `Ctrl + C`:

<p align="left">
  <img src="images/tcpdump_packet_capture_and_filtering_16.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="800"><br>
  <em>Figure 16</em>
</p>

(Step 2) — Capture packets larger than a threshold on a specified interface

I specified an interface to capture packets larger than a defined threshold. To do so, I ran the following command which captured captured all packets on the `ens5` interface that are larger than 1000 bytes.

`tcpdump -i ens5 greater 1000`

This binds the same length filter to `ens5` specifically.

<p align="left">
  <img src="images/tcpdump_packet_capture_and_filtering_17.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="800"><br>
  <em>Figure 17</em>
</p>

I pressed `Ctrl + C` to stop the live capture to see a short summary of all packets captured on the `ens5` interface:

<p align="left">
  <img src="images/tcpdump_packet_capture_and_filtering_18.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="800"><br>
  <em>Figure 18</em>
</p>

(Step 3) — Capture packets larger than a threshold involving a specific host

I specified an interface and a host to capture packets larger than the defined threshold.
  
I modified the filter command by only capturing packets that are larger than 1000 bytes involving the host `example.com` on the `ens5` interface by using the following command: 

`tcpdump -i ens5 host example.com and greater 1000`

This applies both a host filter and a packet-length filter on `ens5`.

<p align="left">
  <img src="images/tcpdump_packet_capture_and_filtering_19.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="800"><br>
  <em>Figure 19</em>
</p>

Capture termination and summary review was performed using `Ctrl + C` to see a short summary of all packets captured involving the host `example.com` on the `ens5` interface that are larger than 1000 bytes:

<p align="left">
  <img src="images/tcpdump_packet_capture_and_filtering_20.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="800"><br>
  <em>Figure 20</em>
</p>


(Step 4) — Capture large TCP packets involving a specific host (multi-condition filter)

I specified an interface, host, and protocol to capture packets larger than the defined threshold using the "AND" operator

**Note:** I also discovered that I can capture only TCP packets larger than 1000 bytes on a specific network involving a specific domain. 

I modified my command filter as such: 

`tcpdump -i ens5 tcp and host example.com and greater 1000`, 

This captured all TCP packets that are greater than 1000 bytes on the `ens5` network involved the host `example.com`. This restricts capture to TCP packets larger than 1000 bytes involving `example.com` on `ens5`.

<p align="left">
  <img src="images/tcpdump_packet_capture_and_filtering_21.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="800"><br>
  <em>Figure 21</em>
</p>

Capture termination and summary review was performed using `Ctrl + C` to see a short summary of all `TCP` packets captured involving the host `example.com` on the `ens5` interface that are larger than 1000 bytes:

<p align="left">
  <img src="images/tcpdump_packet_capture_and_filtering_22.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="800"><br>
  <em>Figure 22</em>
</p>


##### 🔷 3.2 — Review binary operations used in protocol-level filtering

Advanced filtering sometimes requires reasoning about bits within headers. Three binary operations were reviewed to understand how tcpdump processes bits. These operations are often used in protocol-level filtering: 

- The `&` operation takes two bits and returns 0 unless both inputs are 1.
- The `|` operation takes two bits and returns 1 unless both inpurts are 0.
- The `!` operation takes only one bit and inverts it. So an input of 1 returns 0, and an input of 0 gives 1.

These operations are frequently used with byte-offset filters to isolate specific header flags or address types.

(Step 1) — Filter using header byte offsets (`proto[expr:size]`)

Header-byte inspection enables very precise filtering using the syntax `proto[expr:size]`.

**Note:** I explored the concept of header bytes and learned that I could filter based on specific byte positions using the syntax `proto[expr:size]`. This allowed for very detailed inspection, such as targeting parts of the Ethernet or IP header.

Interpretation of the syntax (as used in this workflow):

- `proto` refers to the protocol being inspected
- `expr` is the byte offset, where `0` refers to the first byte
- `size` indicates the number of bytes, which can be either `1`, `2`, or `4` and is `1` by default. This field is optional.

This approach is commonly used for advanced parsing such as checking Ethernet or IP header fields.

(Step 2) — Combine bitwise header byte filters with host filters (multicast example)

A filter was applied to display packets sent to multicast addresses by evaluating the destination MAC address type in the Ethernet header. 

`ether[0] & 1 != 0`

This checks the first byte (`[0]`) in the Ethernet header (`ether`) and uses a binary AND operation (`&`) with `1` to see if the result is not (`!=`) `0`. This is to see if the packet was sent to a multicast group instead of a single device.

I experimented with combining bitwise filters and host filters in tcpdump to capture more specific packet types. Filters like `ether[0] & 1 != 0` can be used alongside host filters to refine captures. 

To capture packets sent to and from `example.com` whose Ethernet destination address is a multicast address, the follow command was ran:

`sudo tcpdump -i ens5 'host example.com and ether[0] & 1 != 0'` 

<p align="left">
  <img src="images/tcpdump_packet_capture_and_filtering_23.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="800"><br>
  <em>Figure 23</em>
</p>

Capture termination and summary review was performed using `Ctrl + C`:

<p align="left">
  <img src="images/tcpdump_packet_capture_and_filtering_24.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="800"><br>
  <em>Figure 24</em>
</p>


##### 🔷 3.3 — TCP flag analysis (handshake and connection-state inference)

To understand how TCP flags indicate the connection state and behavior of a TCP connection, such as connection setup (SYN), acknowledgment (ACK), termination (FIN), and resets (RST). The following were analyzed and reviewed:

- connection setup (SYN)
- acknowledgment (ACK)
- termination (FIN)
- resets (RST)

Analyzing TCP flags is useful for identifying handshake behavior, incomplete handshakes, retransmissions, abrupt resets, and to identify different stages of TCP communication and detect unusual patterns.

TCP flags are 1-bit in size and exist in TCP headers. These flags describe the state or purpose of a TCP packet during communication.

(Step 1) — Capture TCP packets with **only** SYN set (all other flags unset)

To captured TCP packets with only the SYN (Synchronization) flag in their headers with all other flags unset, I ran the command:
  
`tcpdump -i ens5 'tcp[tcpflags] == tcp-syn'`. 

This filter matches packets where the TCP flags field equals SYN exactly, meaning SYN is set and other flags are not set.

**Note:** I was able to isolate SYN packets, which represent the initial connection attempt, on the `ens5` network. This also filters for packets where only the SYN flag is present, as indicated by the `==` operator. 

<blockquote>
When I ran this tcpdump command to capture SYN packets, no packets appeared. This occurred because the test environment wasn’t generating any active TCP traffic at that moment. The filter specifically looks for TCP packets initiating new connections (SYN flags). Because there were no real network activity, no matches were found. In a live environment or during active network use, this command would display packets involved in TCP connection setup.
</blockquote>

<p align="left">
  <img src="images/tcpdump_packet_capture_and_filtering_25.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="800"><br>
  <em>Figure 25</em>
</p>

I pressed `Ctrl + C` to stop the capture and get a short summary of all packets captured:

<p align="left">
  <img src="images/tcpdump_packet_capture_and_filtering_26.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="800"><br>
  <em>Figure 26</em>
</p>


(Step 2) — Capture TCP packets with SYN set (SYN present, other flags allowed)

To capture TCP packets with at least the SYN (Synchronization) flag in their headers regardless of the presence of other flags, I used the expression: 

`tcpdump -i ens5 'tcp[tcpflags] & tcp-syn != 0'`. 

This filter matches packets where SYN is set, regardless of whether other flags are also set. The `!= 0` check indicates the SYN bit is present.

**Note:** I was able to isolate TCP packets with at least the SYN flag present, regardless if other flags were present, as indicated by the `!=` operator, and were making the initial connection attempt on the `ens5` network.

<p align="left">
  <img src="images/tcpdump_packet_capture_and_filtering_27.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="800"><br>
  <em>Figure 27</em>
</p>

Capture termination and summary review was performed using `Ctrl + C`:

<p align="left">
  <img src="images/tcpdump_packet_capture_and_filtering_28.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="800"><br>
  <em>Figure 28</em>
</p>

(Step 3) — Capture TCP packets with SYN **or** ACK set (handshake visibility)

To capture TCP packets with at least the SYN (Synchronization) or ACK (Acknowledge) flag in their headers regardless of the presence of other flags, the following expression was used:

`tcpdump -i ens5 'tcp[tcpflags] & (tcp-syn|tcp-ack) != 0'`. 

This matches packets where SYN or ACK is set. This is useful for observing handshake flow and general connection establishment/acknowledgment traffic.

**Note:** I was able to isolate TCP packets with at least the SYN or ACK flags present, regardless if other flags were present, as indicated by the `!=` operator, and were making the initial connection attempt on the `ens5` network. This filter identifies traffic involved in establishing (SYN) or acknowledging (ACK) TCP connections. This includes the handshake process between two hosts. It helped me observe the flow of connection initiation and acknowledgment packets on the network interface `ens5`.

<p align="left">
  <img src="images/tcpdump_packet_capture_and_filtering_29.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="800"><br>
  <em>Figure 29</em>
</p>

<blockquote>
I took this screenshot before running the command because once I executed it, the terminal (Linux Bash) displayed a continuous stream of live packet captures. To avoid flooding the screen, I pressed Ctrl +C shortly after starting the capture to stop the output.
</blockquote>

I pressed `Ctrl + C` to stop the live capture and get a short capture summary of all packets captured:

<p align="left">
  <img src="images/tcpdump_packet_capture_and_filtering_30.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="800"><br>
  <em>Figure 30</em>
</p>

(Step 4) — Retrieve TCP flag information from a `.pcap` file (RST-only counting + large packet origin)

Offline analysis was used to answer targeted questions from an existing capture file.

To analyze an existing capture file instead of live network traffic to count how many packets had only the TCP Reset (RST) flag set, the following command was ran:

`sudo tcpdump -r traffic.pcap 'tcp[tcpflags] == tcp-rst' | wc -l` 

This command:

- The `-r traffic.pcap` option tells tcpdump to read packets from the saved file rather than capturing live traffic
- The `'tcp[tcpflags] == tcp-rst'` isolates packets with the `RST` flag.
- The `| wc -l` part pipes the output through the word count command, returning the number of lines, which equals the total number of matching packets.

<p align="left">
  <img src="images/tcpdump_packet_capture_and_filtering_31.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="800"><br>
  <em>Figure 31</em>
</p>

To identify sender of packets larger than 15,000 bytes to find the IP address of the host that sent packets larger than 15,000 bytes, the follow expression was used:

`sudo tcpdump -r traffic.pcap 'greater 15000' -n` 

This command:

- The `traffic.pcap` file is the existing file that contains packet captures from a previous exercise
- The `'greater 15000'` filter displays only packets exceeding that size, and
- The `-n` option disables DNS resolution, ensuring that IP addresses are shown directly instead of hostnames

<p align="left">
  <img src="images/tcpdump_packet_capture_and_filtering_32.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="800"><br>
  <em>Figure 32</em>
</p>


##### 🔷 3.4 Operational notes from this stage

This stage revealed how powerful Tcpdump can be when analyzing lower-level protocol behavior. By filtering specific TCP flags, I could observe the TCP handshake (SYN, SYN-ACK, ACK) in action. This understanding is essential for identifying abnormal connection behavior or incomplete handshakes that may indicate scanning or denial-of-service attempts.

I learned how to perform deep-level packet analysis using binary logic and TCP flag filtering. These skills are particularly valuable for cybersecurity investigations where recognizing network patterns—such as repeated SYN packets without ACKs—can reveal potential attacks.

</details>

<details>
<summary><strong>▶ 4) — Displaying Packets (Header and Payload Interpretation Modes)</strong><br>
→ customizing Tcpdump’s output to better interpret packet data and displaying options to support interpretation of packet data
</summary><br>

This stage focuses on selecting `tcpdump` display options to support interpretation of packet data. The workflow uses a stored capture file for repeatability and controlled analysis.

**Goal:** Focus on customizing Tcpdump’s output to better interpret packet data. I learned how to display packet headers and payloads in multiple formats, including ASCII and hexadecimal.

**Note:** I used the "traffic.pcap" file. This file was used to practice analyzing small packet captures and verifying capture integrity and content in a controlled, minimal dataset.

##### 🔷 4.1 — Display basic packet information (sample from a capture file)

To display basic packet information without any arguments or specifications to review captured data, the following command was used:

`tcpdump -r traffic.pcap -c 2` 

This command reads and displays the first two packets from a previously captured packet file (`traffic.pcap`). Output fields include:

- **Timestamp** - when the packet was captured
- **Source and destination IP addresses** - who sent and who received the packet
- **Protocol type** - TCP, UDP, ICMP, ARP, etc
- **Ports** - Source and destination ports for TCP traffic
- **Flags** - TCP flags like SYN, ACK, FIN
- **Packet Length** - Total number of bytes in the frame
- **Additional Information** - Sometimes includes checksums or ICMP message types

**Note:** I added the `-c 2` option to limit the output to just two packets. This was because capture file (`traffic.pcap`) contained a large number of packets and I only needed to display a small sample for analysis.

<p align="left">
  <img src="images/tcpdump_packet_capture_and_filtering_33.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="800"><br>
  <em>Figure 33</em>
</p>

##### 🔷 4.2 — Display brief packet information (quick view)

To simplify the output of the two packets captured and displayed in **Step 1** above, the following command was used:

`tcpdump -r traffic.pcap -q -c 2`.

**Note:** I used `-q` for “quick” mode, which only showed source/destination IPs and ports. This is useful when scanning for high-level flow patterns without reading full metadata per packet.

<p align="left">
  <img src="images/tcpdump_packet_capture_and_filtering_34.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="800"><br>
  <em>Figure 34</em>
</p>

##### 🔷 4.3 — Include MAC addresses and link-layer headers

To now include MAC addresses and Ethernet headers to display link-level headers, the following command was used:

- `tcpdump -r traffic.pcap -e -c 2`

**Note:** I added the `-e` flag to the command from **Step 1**: `tcpdump -r traffic.pcap -e -c 2`. The `-e` flag displays link-layer headers, including MAC addresses. This supports local network pivots (e.g., ARP analysis and host identification at Layer 2).

<p align="left">
  <img src="images/tcpdump_packet_capture_and_filtering_35.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="800"><br>
  <em>Figure 35</em>
</p>

##### 🔷 4.4 — Display packets as ASCII

To view packet data in readable text, the following expression was used:

- `tcpdump -r traffic.pcap -A -c 2`

**Note:** I added `-A`, which printed the ASCII representation of the packet contents. This one was interesting because it also displayed all the bytes mapped to English letters, numbers, and symbols. This can be useful for plaintext protocols (e.g., HTTP) where payloads are human-readable. 

The output appeared mostly unreadable, filled with random letters, numbers, and symbols. This happened because the captured packets contained encrypted SSH traffic. When `-A` is used, tcpdump tries to show the raw payload data as ASCII text, but since encrypted or binary data can’t be represented as readable characters, it appears as gibberish. The readable parts, such as IP addresses, ports, and flags, still provide useful metadata for analysis.

<p align="left">
  <img src="images/tcpdump_packet_capture_and_filtering_36.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="800"><br>
  <em>Figure 36</em>
</p>

##### 🔷 4.5 — Display packets in hexadecimal format

To view packet data in hexidecimal format, the following command was used:

- `tcpdump -r traffic.pcap -xx -c 2`

**Note:** I added `-xx`, which prints packet bytes in hexadecimal form. This enables low-level inspection of packet contents and can support protocol parsing and header interpretation alongside payload bytes.

The results also show the IP and TCP headers in addition to the packet contents.

<p align="left">
  <img src="images/tcpdump_packet_capture_and_filtering_37.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="800"><br>
  <em>Figure 37</em>
</p>

##### 🔷 4.6 — Display packets using both hex and ASCII simultaneously

To combine both hex and ASCII output using `-X`, which showed packets in both formats simultaneously. I ran:

`tcpdump -r traffic.pcap -X -c 2`.

`-X` combines hex and ASCII output, allowing correlation between raw bytes and any readable segments without switching modes.

<p align="left">
  <img src="images/tcpdump_packet_capture_and_filtering_38.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="800"><br>
  <em>Figure 38</em>
</p>

##### 🔷 4.7 — Identify MAC address of a host that sent an ARP request

For testing, I sought to find the MAC address of any hosts that sent ARP requests. To achieve this, I ran:

`tcpdump -r traffic.pcap arp -e`

This filters `traffic.pcap` for ARP frames and includes Ethernet headers (`-e`) so MAC addresses are visible.

<p align="left">
  <img src="images/tcpdump_packet_capture_and_filtering_39.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="800"><br>
  <em>Figure 39</em>
</p>

Observed outcome interpretation:

- An ARP request asking “who has `192.168.124.137`” was broadcast to the network.
- In `tcpdump` output, the MAC address **before** the `>` symbol represents the source (sender), and the one **after** represents the destination.
- Using this convention, the host with MAC address `52:54:00:7c:d3:5b` was identified as the sender of the ARP request.
- The reply came from `52:54:00:23:60:2b`, confirming it as the device associated with `192.168.124.137`.

**Note:** I identified an ARP request asking “who has `192.168.124.137`” that was broadcast to the network. The MAC address before the `>` symbol in tcpdump’s output represents the source (sender), while the one after represents the destination. Using this format, I determined that the host with MAC address `52:54:00:7c:d3:5b` sent the ARP request. The reply came from `52:54:00:23:60:2b`, confirming it as the device associated with `192.168.124.137`.

</details>

---

### Results & Interpretation

Each display mode serves a different purpose. ASCII mode (`-A`) was useful when inspecting plaintext protocols like HTTP, while hexadecimal mode (`-xx`) provided insight into binary structures and headers. The combined `-X` mode made it easy to correlate header data with payload information. These display options help analysts interpret packets quickly, especially when verifying encoded data or identifying suspicious payloads.

This workflow produced evidence-backed confirmation that `tcpdump` can support targeted traffic collection and analysis through both live and offline methods.

Key results observed across the workflow stages:

- **Capture viability and interface selection**
  - Interface enumeration identified `ens5` as the relevant Ethernet interface in the environment.
  - A bounded capture (`-c 5`) confirmed live packet capture functionality and showed real traffic (TCP packets exchanged between local IPs), validating that `ens5` was active.

- **Filtering effectiveness and noise reduction**
  - Filters drastically improved output manageability compared to unfiltered captures.
  - Host filters demonstrated how capture scope can be constrained to traffic to/from a domain. In the placeholder-domain case (`example.com`), no packets were captured due to lack of matching traffic, which correctly demonstrated filter behavior rather than a tooling failure.
  - Port filtering on `53` showed how DNS traffic can be isolated, while protocol filtering (`icmp`) showed how the absence of traffic results in a correct “0 packets” outcome in a quiet environment.

- **Offline analysis repeatability and quantitative pivots**
  - Reading from a `.pcap` (`-r traffic.pcap`) enabled repeatable analysis independent of real-time traffic conditions.
  - Piping output to `wc` supported fast quantitative pivots, such as counting the number of packets matching a source IP filter (`wc -l`) or estimating ICMP packet volume (`wc`). The `910` line count served as a quick proxy for the number of packets displayed by `tcpdump` for that filter.

- **Protocol and network inference from capture contents**
  - ARP filtering identified the requesting host (`192.168.124.148`) for the MAC address of `192.168.124.137`.
  - DNS payload inspection (`-A` on port 53) revealed the first queried hostname in the capture as `mirrors.rockylinux.org`.
  - TCP flag filtering established a practical framework for observing handshake-related traffic:
    - SYN-only filters may show no output in a quiet environment (expected behavior).
    - Bitwise flag checks support broader handshake visibility (SYN present, SYN/ACK present).
  - Offline TCP flag analysis enabled counting RST-only packets and identifying hosts emitting unusually large packets (`greater 15000`).

- **Output-mode interpretation tradeoffs**
  - ASCII mode (`-A`) can surface plaintext protocol content, but encrypted traffic (SSH) appears as unreadable characters—while still providing useful metadata for correlation.
  - Hex (`-xx`) and combined hex+ASCII (`-X`) support deeper inspection, enabling an analyst to reason about low-level packet structures when required.
  - Link-layer headers (`-e`) enabled MAC-based pivots necessary for local network analysis.

---

### Operational & Defensive Takeaways

The following takeaways reflect how this workflow translates to SOC operations, incident response, and network troubleshooting:

1. **Start small, then narrow further.**  
   Bounded capture (`-c`) and numeric output (`-n`) should be default posture for initial validation. This prevents output floods, avoids DNS-related delays, and reduces the chance of collecting unnecessary data. Once capture viability is confirmed, filters can be added incrementally to converge on relevant traffic.

2. **Treat filtering as an evidence-scoping control.**  
   `tcpdump` filters directly determine what is collected and reviewed. In high-noise environments, capturing without filters can produce unusable output and oversized capture files. Host/port/protocol filters should be selected to align with the investigative question (e.g., “DNS resolution behavior,” “ICMP reachability,” “TCP handshake patterns,” “ARP identity mapping”).

3. **Use `.pcap` for repeatability, collaboration, and chain-of-analysis.**  
   Writing to `.pcap` (`-w`) produces portable artifacts that can be:
   - replayed consistently (`-r`),
   - shared with teammates,
   - analyzed in parallel (CLI + GUI tools),
   - and used for later correlation with detections or logs.

4. **TCP flag logic is a practical detection lens.**  
   Flag-state analysis supports interpretation of network behavior beyond “is there traffic.” For example:
   - repeated SYN patterns without corresponding ACKs can suggest scanning or incomplete handshakes,
   - RST patterns can indicate rejected connections or abrupt resets,
   - combined SYN/ACK filters can help observe handshake flow in active environments.

5. **Output mode selection matters as much as filter selection.**  
   - `-q` supports fast triage of flows.
   - `-e` enables link-layer pivots (critical for ARP and local network mapping).
   - `-A` is valuable for plaintext protocols but not reliable for encrypted payload interpretation.
   - `-xx` and `-X` support deeper inspection and protocol-level troubleshooting when needed.

---

### Reuse Pack (Quick Reference)

This section provides reusable command patterns that map directly to the workflow’s execution steps.

#### ▶ Interface and baseline capture

- Enumerate interfaces: `ip a s`
- Bounded capture (confirm traffic + prevent output floods):  
  `sudo tcpdump -i <interface> -c <packet_count> -n`

#### ▶ Capture to file and replay

- Write capture to `.pcap`:  
  `sudo tcpdump -i <interface> -w <file>.pcap`
- Read capture from `.pcap`:  
  `tcpdump -r <file>.pcap`
- Read and limit display:  
  `tcpdump -r <file>.pcap -c <N>`

#### ▶ Common filters

- Host filter: `tcpdump host <domain_or_ip>`
- Directional host filter: `tcpdump src host <ip>` / `tcpdump dst host <ip>`
- Port filter: `tcpdump port <port>`
- Directional port filter: `tcpdump src port <port>` / `tcpdump dst port <port>`
- Protocol filter: `tcpdump icmp` / `tcpdump tcp` / `tcpdump udp` / `tcpdump arp`
- Combine filters: `and` / `or` / `not`  
  Example: `tcpdump tcp and port 443 and host <host>`

#### ▶ Offline counts (fast pivots)

- Count matching packets (line count proxy):  
  `tcpdump -r <file>.pcap <filter> -n | wc -l`
- Count lines/words/chars (broader counts):  
  `tcpdump -r <file>.pcap <filter> -n | wc`

#### ▶ Packet size filters

- Capture packets larger than X bytes: `tcpdump greater <bytes>`
- Capture packets smaller than X bytes: `tcpdump less <bytes>`
- Combine with interface and host:  
  `tcpdump -i <interface> host <host> and greater <bytes>`

#### ▶ Byte-offset / bitwise filters

- Syntax: `proto[expr:size]`
- Example multicast test: `ether[0] & 1 != 0`
- Combined example: `sudo tcpdump -i <interface> 'host <host> and ether[0] & 1 != 0'`

#### ▶ TCP flag filters

- SYN only (exact match): `tcpdump -i <interface> 'tcp[tcpflags] == tcp-syn'`
- SYN present (bitwise): `tcpdump -i <interface> 'tcp[tcpflags] & tcp-syn != 0'`
- SYN or ACK present: `tcpdump -i <interface> 'tcp[tcpflags] & (tcp-syn|tcp-ack) != 0'`
- RST only from `.pcap` with count:  
  `sudo tcpdump -r <file>.pcap 'tcp[tcpflags] == tcp-rst' | wc -l`

#### ▶ Display modes

- Quick mode: `tcpdump -r <file>.pcap -q -c <N>`
- Link-layer headers / MAC addresses: `tcpdump -r <file>.pcap -e -c <N>`
- ASCII payload: `tcpdump -r <file>.pcap -A -c <N>`
- Hex payload: `tcpdump -r <file>.pcap -xx -c <N>`
- Hex + ASCII: `tcpdump -r <file>.pcap -X -c <N>`

---

### What I Learned (Skills Demonstrated)

This workflow demonstrates practical, transferable capabilities relevant to network triage, incident response, and detection validation:

- **Command-line packet capture proficiency** using `tcpdump` on a remote Linux host, including permission considerations (`sudo`) and interface selection (`ip a s`, `-i`).
- **Capture scoping and operational hygiene** by limiting packet counts (`-c`) and suppressing name resolution (`-n` / `-nn`) to reduce noise and improve performance.
- **Filter construction using BPF expressions** to isolate traffic by host, port, and protocol, and to refine intent using logical operators (`and`, `or`, `not`) and directional selectors (`src`, `dst`).
- **Offline packet analysis and repeatability** by reading `.pcap` files (`-r`), creating portable artifacts (`-w`), and using shell pipelines (`wc`, `wc -l`) for rapid quantification.
- **Advanced protocol-level reasoning** via packet length filters (`greater`, `less`), header byte inspection (`proto[expr:size]`), bitwise operations (`&`, `|`, `!`), and TCP flag-state analysis (`tcp[tcpflags]` expressions).
- **Interpretation of packet metadata and payload representations** by selecting appropriate display modes (`-q`, `-e`, `-A`, `-xx`, `-X`) and understanding limitations when traffic is encrypted (ASCII gibberish is expected for SSH payloads, while metadata remains usable).
- **Local network identity pivots** through ARP inspection with MAC visibility (`arp` + `-e`), supporting host identification and Layer-2 correlation.

---

### Operational takeaways from this workflow

Throughout this workflow execution exercise, I practiced using Tcpdump to capture, filter, and interpret network traffic from a command-line interface. I moved from basic captures to advanced filtering techniques that allowed me to identify very specific types of network activity. The experience reinforced how important it is to filter data effectively, since raw packet captures can be overwhelming without structure.

---

### Reflection
Tcpdump is lightweight but incredibly powerful. Unlike graphical tools, it provides immediate insight without requiring a large amount of system resources. By learning its syntax and options, I can now use it to diagnose connectivity issues, observe protocol behavior, and even detect potential malicious traffic. Combining Tcpdump with tools like Wireshark or Splunk could create a strong foundation for deeper network investigations.
