# Analyst Notes — Targeted Packet Capture and Traffic Filtering Using tcpdump

This file captures analytical observations, investigative reasoning, and workflow-specific takeaways recorded while performing targeted packet capture and offline analysis using `tcpdump`.

These notes complement the formal execution walkthrough by focusing on *why* certain pivots were made, what the output implied, and how similar techniques would apply in real SOC and incident response scenarios.

> **Workflow vs Execution vs Writeup (Terminology Used Here)**  
> - **Workflows** refer to operational tasks such as onboarding telemetry and validating parsing behavior.  
> - **Executions** refer to hands-on configuration and validation using real data and Splunk services.  
> - **Writeups** document configuration decisions, troubleshooting steps, and validation results.

---

## Observations During Execution

Identifying the correct interface (`ens5`) was more important than expected. Even though the host only had a small number of interfaces, confirming which one carried active traffic prevented wasted capture attempts and reinforced that interface naming varies widely across environments (cloud VMs, on‑prem servers, containers, etc.).

Using a bounded capture (`-c 5`) at the beginning was an effective validation step. It confirmed that capture permissions, interface selection, and traffic flow were all correct without flooding the terminal with output or collecting unnecessary data. This felt similar to validating log ingestion before building SIEM detection logic.

Filtering by host using `example.com` resulted in zero packets, which initially looked like failure but actually validated correct filter behavior. This reinforced that empty results are meaningful: either no traffic matches the filter or the hypothesis being tested is incorrect. In investigations, both outcomes matter.

When filtering DNS traffic on port `53`, the output volume dropped significantly, which demonstrated how much background noise exists on typical server interfaces. Even in quiet environments, unfiltered captures can quickly become overwhelming.

Offline analysis using `traffic.pcap` changed the workflow from reactive to repeatable. Being able to re-run the same filters multiple times removed dependency on real-time traffic and made it easier to answer targeted questions such as source counts, protocol distribution, and host behavior.

The ARP pivot was especially useful for understanding local network relationships. Identifying which host was requesting a MAC address for another IP demonstrated how Layer 2 data supports host identification even when IP-level telemetry is incomplete.

TCP flag filtering highlighted how connection behavior can be inferred without application-layer visibility. SYN-only and SYN/ACK filters reinforced how scanning, handshake failures, and connection attempts can be detected using header metadata alone.

---

## Challenges and Troubleshooting

The biggest challenge was interpreting output when no packets matched filters. Several commands produced zero results due to limited background activity in the environment. Instead of assuming misconfiguration, validating filters against offline data confirmed that syntax was correct and that the environment simply lacked traffic.

Understanding how packet counts from `wc -l` relate to actual packet volume required care. Each line of tcpdump output typically corresponds to one packet, but verbose modes or multi-line output can distort counts. This means counts should be treated as estimates unless exact metrics are required.

TCP flag syntax was initially confusing. The difference between:

- exact matches (`== tcp-syn`)
- bitwise checks (`& tcp-syn != 0`)

became much clearer after seeing how SYN-only versus SYN-present filters behave differently during handshake analysis.

Header byte offset filters were the most conceptually difficult. Understanding what `ether[0] & 1` represents required reviewing how multicast addressing is encoded at the Ethernet layer, which reinforced that advanced filters require protocol knowledge, not just syntax memorization.

---

## Investigative Reasoning and Workflow Design

The workflow followed a clear investigative progression:

1. **Validate capture capability**
2. **Constrain capture scope**
3. **Preserve artifacts**
4. **Replay and analyze offline**
5. **Apply deeper protocol logic**
6. **Select display formats for interpretation**

This mirrored real-world network triage patterns where analysts must first confirm visibility, then narrow focus, and only later move into deep protocol inspection.

Host and port filters were applied before TCP flag filters intentionally. Understanding which systems and services were communicating provided necessary context before analyzing connection behavior.

Using offline captures for advanced filtering avoided the risk of missing short-lived events during live capture and allowed repeatable verification of hypotheses.

Display-mode testing was placed last because payload and header visualization only becomes useful after relevant packets have already been isolated.

---

## Limitations of This Workflow

This workflow does not include:

- TLS decryption or application-layer protocol decoding
- Correlation with firewall, proxy, or EDR logs
- Detection of malicious payloads or C2 signatures
- Network segmentation or lateral movement simulation

As a result, the workflow focuses on traffic visibility and filtering mechanics rather than full intrusion analysis.

The environment also did not generate high traffic volumes, which limited the opportunity to observe real scanning patterns, handshake failures, or throughput-heavy flows.

---

## Ideas for Expansion or Automation

Future enhancements could include:

- Automating capture + filter pipelines for common investigations
- Extracting packet metadata into structured logs for SIEM ingestion
- Combining tcpdump with Zeek or Suricata for protocol-level telemetry
- Simulating scanning or brute-force traffic to test flag-based detection logic
- Building scripts to extract DNS domains and IPs automatically from pcaps

These additions would bridge manual packet inspection with detection engineering and response automation.

---

## Cross-Workflow Connections

This workflow strongly supports:

- Firewall log investigations
- VPN traffic validation
- Endpoint network triage workflows
- SIEM detection validation exercises

Understanding packet-level behavior improves confidence when interpreting higher-level telemetry across security tools.
