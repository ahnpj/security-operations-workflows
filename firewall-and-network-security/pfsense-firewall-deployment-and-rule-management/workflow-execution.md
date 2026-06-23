# pfSense Firewall Rule Management and Traffic Filtering

## Overview

This workflow demonstrates the deployment, configuration, and administration of a pfSense firewall within a virtualized environment. The objective was to gain practical experience with firewall concepts including interface configuration, gateway management, traffic filtering, aliases, rule processing order, packet forwarding, and network traffic validation.

Rather than relying solely on host-based controls, I deployed a dedicated network firewall and routed workstation traffic through it. This provided hands-on experience with how traffic traverses a firewall, how rules are evaluated, and how security policies are enforced at the network layer.

The exercise culminated in the creation of custom firewall rules that selectively blocked access to a specified external domain while permitting all other outbound traffic.

---

## Purpose and Analyst Focus

### Purpose

The purpose of this workflow was to better understand how firewalls operate within a network environment and how administrators use them to control traffic flow between trusted and untrusted networks.

While many modern security controls operate directly on endpoints, network firewalls remain a foundational security technology used to:

- Enforce security policies
- Restrict unauthorized communications
- Segment networks
- Monitor traffic flows
- Reduce attack surface
- Centralize network security controls

This workflow focused on understanding the operational side of firewall administration rather than simply deploying a virtual appliance.

### Analyst Focus

This workflow focused on:

- Deploying a pfSense firewall appliance
- Configuring network interfaces
- Understanding WAN and LAN roles
- Configuring gateway routing
- Creating aliases
- Creating firewall rules
- Understanding rule evaluation order
- Routing workstation traffic through a firewall
- Validating rule enforcement
- Understanding how centralized filtering differs from endpoint filtering

---

## What This Workflow Demonstrates

This workflow demonstrates how to:

- Deploy pfSense in VirtualBox
- Configure bridged networking
- Configure WAN interfaces
- Access the pfSense management console
- Configure routing and gateways
- Create firewall aliases
- Create allow and block rules
- Understand top-down rule evaluation
- Redirect workstation traffic through a firewall
- Validate traffic filtering behavior
- Understand real-world firewall administration concepts

---

## Network Security Relevance

Firewalls are one of the most widely deployed security controls in enterprise environments.

While products such as Palo Alto, FortiGate, Cisco ASA, and Check Point are common within organizations, the concepts demonstrated in this workflow apply regardless of vendor:

- Interfaces
- Zones
- Gateways
- Routing
- Stateful inspection
- Rule evaluation
- Network segmentation
- Policy enforcement

Understanding these concepts provides a foundation for working with enterprise firewall technologies.

---

## Environment and Execution Context

<details>
<summary><strong>▶ Environment & Platform</strong></summary>

### Host Platform

- Windows Host System
- Oracle VirtualBox

### Firewall Platform

- pfSense Community Edition

### Virtualization Configuration

- BSD
- FreeBSD (64-bit)
- 1024 MB RAM
- VDI Disk Format
- Bridged Adapter Networking

</details>

<details>
<summary><strong>▶ Tooling Used</strong></summary>

### Primary Tool

**pfSense**

Used to:

- Manage interfaces
- Configure gateways
- Create aliases
- Create firewall rules
- Monitor traffic
- Enforce network policy

### Supporting Tools

- VirtualBox
- Windows Command Prompt
- Browser-based pfSense Management Console

</details>

<details>
<summary><strong>▶ Workflow Map (High-Level)</strong></summary>

1. Deploy pfSense virtual appliance
2. Configure bridged networking
3. Install pfSense
4. Configure interfaces
5. Access management console
6. Configure gateway settings
7. Create aliases
8. Create block rule
9. Create allow-all rule
10. Route workstation traffic through pfSense
11. Validate traffic filtering
12. Review findings

</details>

---

# Step-by-Step Execution

<details>
<summary><strong>▶ Phase 1 — Deploy pfSense Firewall Appliance</strong></summary>

The first phase focused on deploying a dedicated firewall appliance within a virtualized environment.

A new virtual machine was created using VirtualBox and configured with FreeBSD as the operating system type. The pfSense installation ISO was mounted and used to initialize the appliance.

The goal of this phase was to establish a dedicated firewall platform capable of inspecting and controlling traffic independently from the host system.

### Screenshot Placeholder

[INSERT FIGURE 1 – VirtualBox VM Configuration]

### Key Takeaways

- pfSense can operate as a standalone firewall appliance.
- Virtualization provides a safe environment for experimentation.
- Firewall administration concepts can be practiced without dedicated hardware.

</details>

<details>
<summary><strong>▶ Phase 2 — Configure Interfaces and Routing</strong></summary>

After installation, network interfaces were configured.

The WAN interface was assigned to em0 and configured to communicate with the local network.

At this stage it was important to understand the role of interfaces within a firewall.

### WAN Interface

The WAN interface represents the network facing untrusted traffic.

In enterprise environments this is typically the Internet-facing interface.

### LAN Interface

The LAN interface typically represents trusted internal resources.

For this exercise the focus remained on the WAN-facing configuration.

### Screenshot Placeholder

[INSERT FIGURE 2 – Interface Assignment]

### Key Takeaways

- Firewalls make forwarding decisions based on interfaces.
- Interfaces create logical traffic boundaries.
- Proper interface assignment is required before policy enforcement can occur.

</details>

<details>
<summary><strong>▶ Phase 3 — Configure Management Access and Gateway Settings</strong></summary>

After interface configuration, the pfSense web console became accessible.

The management interface was accessed through a browser using the IP address assigned to the firewall.

The initial setup wizard was completed and gateway settings were reviewed.

### Why Gateways Matter

A firewall may successfully receive traffic, but without a valid upstream gateway it will be unable to forward traffic toward its destination.

The gateway effectively tells the firewall where traffic should go when the destination is not located on a directly connected network.

### Screenshot Placeholder

[INSERT FIGURE 3 – pfSense Dashboard]

### Screenshot Placeholder

[INSERT FIGURE 4 – Gateway Configuration]

### Key Takeaways

- Firewalls require valid routing information.
- Gateway configuration directly impacts connectivity.
- Routing mistakes often appear as firewall issues.

</details>

<details>
<summary><strong>▶ Phase 4 — Create Firewall Aliases</strong></summary>

Before creating filtering rules, an alias was created for the target domain.

Aliases provide administrative simplicity by allowing administrators to reference logical names instead of individual IP addresses throughout multiple firewall rules.

### Benefits of Aliases

- Easier maintenance
- Improved readability
- Reduced administrative overhead
- Faster policy updates

Instead of referencing a raw IP address repeatedly, the alias could be reused throughout firewall policies.

### Screenshot Placeholder

[INSERT FIGURE 5 – Alias Configuration]

### Key Takeaways

- Aliases improve scalability.
- Aliases simplify policy administration.
- Aliases reduce future rule modification effort.

</details>

<details>
<summary><strong>▶ Phase 5 — Create Traffic Filtering Rules</strong></summary>

This phase focused on creating firewall policies.

Two primary rules were created:

### Rule 1 — Block Target Domain

This rule denied all traffic destined for the specified domain.

Configuration included:

- Action: Block
- Protocol: Any
- Source: Internal Network
- Destination: Alias

### Rule 2 — Allow All Traffic

A second rule permitted all remaining outbound communications.

This rule ensured Internet connectivity while still allowing the first rule to block the designated destination.

### Understanding Rule Evaluation

Firewall rules are processed from top to bottom.

Because of this:

1. Block Rule
2. Allow-All Rule

produced a different outcome than:

1. Allow-All Rule
2. Block Rule

The ordering of rules directly impacts enforcement behavior.

### Screenshot Placeholder

[INSERT FIGURE 6 – Block Rule]

### Screenshot Placeholder

[INSERT FIGURE 7 – Allow Rule]

### Screenshot Placeholder

[INSERT FIGURE 8 – Rule Ordering]

### Key Takeaways

- Rule order matters.
- Firewalls evaluate rules sequentially.
- Specific rules should typically appear before broad rules.

</details>

<details>
<summary><strong>▶ Phase 6 — Route Host Traffic Through pfSense</strong></summary>

After policy creation, workstation traffic was redirected through the firewall.

This required updating network settings on the host system so that pfSense became the default gateway.

### Traffic Flow Before

Host > Router > Internet

### Traffic Flow After

Host > pfSense > Router > Internet

This modification ensured that all outbound traffic would first be inspected by the firewall before reaching external destinations.

### Screenshot Placeholder

[INSERT FIGURE 9 – Host Network Configuration]

### Key Takeaways

- Default gateways determine packet paths.
- Traffic must traverse a firewall before policies can be enforced.
- Gateway changes can significantly alter network behavior.

</details>

<details>
<summary><strong>▶ Phase 7 — Validate Rule Enforcement</strong></summary>

The final phase focused on validation.

After traffic began traversing the firewall, testing was performed to confirm:

- General Internet access remained functional.
- The blocked destination was inaccessible.
- Firewall policies behaved as expected.

Successful validation demonstrated that rule ordering, aliases, routing, and gateway configuration were functioning correctly.

### Screenshot Placeholder

[INSERT FIGURE 10 – Successful Filtering Validation]

### Key Takeaways

- Validation is critical after implementing policy changes.
- Connectivity testing confirms rule effectiveness.
- Verification reduces the risk of misconfiguration.

</details>

---

# Firewall Rule Processing Explained

One of the most important concepts reinforced during this workflow was rule evaluation order.

Firewalls do not evaluate every rule simultaneously.

Instead, they process traffic sequentially from top to bottom until a matching rule is identified.

Example:

1. Block redhunt.net
2. Allow all traffic

Result:

Traffic destined for redhunt.net is blocked while all other traffic is permitted.

This demonstrates why administrators typically place specific rules above broader rules.

---

# Routing and Gateway Analysis

A firewall performs two major functions:

1. Security Enforcement
2. Traffic Forwarding

Security policies determine whether traffic is permitted.

Routing determines where traffic should be sent.

A firewall may correctly allow traffic yet still fail to deliver it if routing information is incorrect.

This workflow reinforced the relationship between routing and security policy enforcement.

---

# Findings and Validation

The firewall successfully:

- Inspected workstation traffic
- Enforced custom filtering policies
- Blocked the designated destination
- Allowed all remaining outbound traffic
- Forwarded traffic to the upstream gateway
- Maintained Internet connectivity

The testing confirmed that firewall policies were operating as intended.

---

# What I Learned (Skills Demonstrated)

Through this workflow, I gained practical experience with:

- Firewall deployment
- pfSense administration
- Network interface configuration
- Gateway management
- Routing concepts
- Firewall aliases
- Policy creation
- Rule evaluation order
- Traffic filtering
- Network troubleshooting
- Validation testing
- Security policy enforcement

I also strengthened my understanding of how centralized network controls differ from host-based controls.

Rather than configuring policies on every endpoint individually, a firewall can enforce security policies at a central inspection point.

---

# Final Conclusion

This workflow demonstrated the deployment and administration of a pfSense firewall within a virtualized environment.

The project provided hands-on experience with firewall interfaces, routing, gateways, aliases, traffic filtering, and policy enforcement. By routing workstation traffic through the firewall and implementing custom rules, it was possible to observe how traffic is inspected, evaluated, and either permitted or denied based on administrator-defined policies.

The exercise reinforced several foundational network security concepts, including rule evaluation order, centralized traffic control, gateway routing, and policy validation. While pfSense was used as the platform, the concepts practiced throughout this workflow are directly transferable to enterprise firewall technologies and provide a strong foundation for future work involving network security administration, security operations, and infrastructure defense.
