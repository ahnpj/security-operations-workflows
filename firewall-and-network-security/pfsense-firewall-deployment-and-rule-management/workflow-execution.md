# pfSense Firewall Rule Management and Traffic Filtering

### Overview

This workflow demonstrates the deployment, configuration, and administration of a pfSense firewall within a virtualized environment. The objective was to gain practical experience with firewall concepts including interface configuration, gateway management, traffic filtering, aliases, rule processing order, packet forwarding, and network traffic validation.

Rather than relying solely on host-based controls, I deployed a dedicated network firewall and routed workstation traffic through it. This provided hands-on experience with how traffic traverses a firewall, how rules are evaluated, and how security policies are enforced at the network layer.

The exercise culminated in the creation of custom firewall rules that selectively blocked access to a specified external domain while permitting all other outbound traffic.

---

### Purpose and Analyst Focus

#### Purpose

The purpose of this workflow was to better understand how firewalls operate within a network environment and how administrators use them to control traffic flow between trusted and untrusted networks.

While many modern security controls operate directly on endpoints, network firewalls remain a foundational security technology used to:

- Enforce security policies
- Restrict unauthorized communications
- Segment networks
- Monitor traffic flows
- Reduce attack surface
- Centralize network security controls

This workflow focused on understanding the operational side of firewall administration rather than simply deploying a virtual appliance.

#### Analyst Focus

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

### What This Workflow Demonstrates

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

### Network Security Relevance

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

### Environment and Execution Context

<details>
<summary><strong>▶ Environment & Platform</strong></summary>

#### Host Platform

- Windows Host System
- Oracle VirtualBox

#### Firewall Platform

- pfSense Community Edition

#### Virtualization Configuration

- BSD
- FreeBSD (64-bit)
- 1024 MB RAM
- VDI Disk Format
- Bridged Adapter Networking

</details>

<details>
<summary><strong>▶ Tooling Used</strong></summary>

#### Primary Tool

**pfSense**

Used to:

- Manage interfaces
- Configure gateways
- Create aliases
- Create firewall rules
- Monitor traffic
- Enforce network policy

#### Supporting Tools

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

## Step-by-Step Execution

This section documents the pfSense firewall workflow in the same order I performed it. The goal was to deploy a firewall appliance, configure it as a traffic control point, create filtering policies, route workstation traffic through the firewall, and validate that the rule behavior worked as expected.

The workflow begins with deploying the pfSense virtual machine because the firewall appliance must exist before interfaces, gateways, aliases, or rules can be configured. It then moves into interface assignment, web console access, gateway configuration, alias creation, firewall rule creation, host routing changes, and traffic filtering validation.

> **Note:** Each section is collapsible. Click the ▶ arrow to expand and view the detailed steps.

<details>
<summary><strong>▶ Phase 1 — Deploy pfSense Firewall Appliance</strong><br>
→ creating a dedicated virtual firewall for traffic filtering
</summary><br>

This phase focused on deploying the pfSense firewall appliance that would later be used to inspect, route, and filter network traffic.

<blockquote>
I began by deploying pfSense because I wanted a dedicated firewall appliance capable of acting as a traffic control point between my workstation and the network. Before creating firewall rules or modifying traffic flow, the firewall itself first needed to be installed and configured.
</blockquote>

##### 🔷 Phase 1.1 — Download and Prepare the pfSense ISO

I started by downloading the pfSense Community Edition installation image.

The download was provided as a compressed `.iso.gz` file, so before creating the virtual machine I extracted the archive using 7-Zip to obtain the underlying `.iso` installation image.

This ISO would later be mounted within VirtualBox and used to install the pfSense operating system onto a virtual disk.

<p align="left">
  <img src="images/pfsense-firewall-rule-management-01.png"
       alt="Downloading and extracting the pfSense installation image"
       style="border: 2px solid #444; border-radius: 6px;"
       width="900"><br>
  <em>Figure 1: Downloading and preparing the pfSense installation image.</em>
</p>

##### 🔷 Phase 1.2 — Create the pfSense Virtual Machine

Next, I opened VirtualBox and selected:

```text
New
```

I named the virtual machine:

```text
BTL1 Firewall Exercise
```

Because pfSense is built on FreeBSD, I configured the operating system settings as:

```text
Type: BSD
Version: FreeBSD (64-bit)
```

Selecting the correct operating system type helps VirtualBox apply compatible default settings and virtual hardware configurations for the guest operating system.

<p align="left">
  <img src="images/pfsense-firewall-rule-management-02.png"
       alt="Creating the pfSense virtual machine"
       style="border: 2px solid #444; border-radius: 6px;"
       width="900"><br>
  <em>Figure 2: Creating the pfSense virtual machine in VirtualBox.</em>
</p>

##### 🔷 Phase 1.3 — Assign Memory and Create a Virtual Disk

After creating the virtual machine, I assigned:

```text
1024 MB RAM
```

The firewall would only be used for administrative testing and traffic filtering demonstrations, so this amount of memory was sufficient for the lab environment.

I then created a new virtual hard disk using:

```text
VirtualBox Disk Image (VDI)
```

This virtual disk would serve as the storage location where the pfSense operating system would be installed.

<p align="left">
  <img src="images/pfsense-firewall-rule-management-03.png"
       alt="Assigning memory and creating the virtual disk"
       style="border: 2px solid #444; border-radius: 6px;"
       width="900"><br>
  <em>Figure 3: Assigning memory and creating the virtual disk.</em>
</p>

##### 🔷 Phase 1.4 — Configure Bridged Networking

Before installing pfSense, I reviewed the network adapter configuration.

By default, VirtualBox commonly uses NAT networking. NAT would allow the virtual machine to access the internet through the host system, but it would not behave like an independent device on the network.

Because my goal was to simulate a dedicated firewall appliance, I navigated to:

```text
Settings
    >
Network
    >
Attached To
    >
Bridged Adapter
```

and changed the adapter from NAT to Bridged Adapter mode.

This allowed the pfSense appliance to obtain its own IP address on the local network and communicate as a standalone device rather than sharing the host's network identity.

<blockquote>
This step was important because a firewall must exist within the traffic path to enforce policy. Bridged networking allowed pfSense to behave more like a real network appliance and made later routing and gateway changes possible.
</blockquote>

<p align="left">
  <img src="images/pfsense-firewall-rule-management-04.png"
       alt="Configuring bridged adapter networking"
       style="border: 2px solid #444; border-radius: 6px;"
       width="900"><br>
  <em>Figure 4: Configuring Bridged Adapter networking.</em>
</p>

##### 🔷 Phase 1.5 — Mount the Installation ISO

After configuring the virtual machine, I started it from VirtualBox.

When prompted to select an optical disk, I browsed to the extracted pfSense ISO file and mounted it as the installation media.

This allowed the virtual machine to boot directly into the pfSense installer.

<p align="left">
  <img src="images/pfsense-firewall-rule-management-05.png"
       alt="Mounting the pfSense ISO"
       style="border: 2px solid #444; border-radius: 6px;"
       width="900"><br>
  <em>Figure 5: Mounting the pfSense ISO as installation media.</em>
</p>

##### 🔷 Phase 1.6 — Install pfSense

After booting from the ISO, the pfSense installation wizard launched successfully.

I proceeded through the installer using the default configuration options provided by pfSense.

The installer copied the operating system to the virtual disk and prepared the appliance for first-time configuration.

Once installation completed successfully, I shut down the virtual machine and removed the mounted ISO from the virtual optical drive.

Removing the ISO prevents the installer from launching again during future boots and allows the system to boot directly into the installed operating system.

<p align="left">
  <img src="images/pfsense-firewall-rule-management-06.png"
       alt="Completing pfSense installation"
       style="border: 2px solid #444; border-radius: 6px;"
       width="900"><br>
  <em>Figure 6: Completing the pfSense installation.</em>
</p>

##### 🔷 Phase 1.7 — Phase 1 Findings

| Item                    | Finding                              |
| ----------------------- | ------------------------------------ |
| Firewall Platform       | pfSense Community Edition            |
| Virtualization Platform | VirtualBox                           |
| Operating System Type   | BSD                                  |
| Version                 | FreeBSD (64-bit)                     |
| Network Mode            | Bridged Adapter                      |
| Purpose                 | Dedicated virtual firewall appliance |

<blockquote>
At the conclusion of this phase, the pfSense appliance was successfully deployed and ready for interface assignment and network configuration.
</blockquote>

</details>

<details>
<summary><strong>▶ Phase 2 — Configure Initial Interface Assignment</strong><br>
→ assigning the network interface used for management and traffic forwarding
</summary><br>

This phase focused on assigning the network interface that pfSense would use for management access and future traffic routing.

<blockquote>
After installing pfSense, I needed to assign a usable network interface before the firewall could be administered through the web interface. Without a configured interface, the appliance would not be reachable on the network and subsequent configuration tasks would not be possible.
</blockquote>

##### 🔷 Phase 2.1 — Boot Into pfSense

After removing the installation ISO, I powered on the virtual machine again.

This time the system booted directly into the pfSense console menu rather than the installer.

The console displayed several administrative options including interface assignment, IP address configuration, diagnostics, and password management.

<p align="left">
  <img src="images/pfsense-firewall-rule-management-07.png"
       alt="Booting into the pfSense console menu"
       style="border: 2px solid #444; border-radius: 6px;"
       width="900"><br>
  <em>Figure 7: Booting into the pfSense console menu.</em>
</p>

##### 🔷 Phase 2.2 — Begin Interface Assignment

From the console menu, I selected:

```text
Option 1
Assign Interfaces
```

The system then prompted:

```text
Should VLANs be set up now?
```

I entered:

```text
n
```

because VLAN segmentation was outside the scope of this workflow.

My objective was to establish a simple firewall deployment before introducing additional segmentation concepts.

<p align="left">
  <img src="images/pfsense-firewall-rule-management-08.png"
       alt="Beginning interface assignment"
       style="border: 2px solid #444; border-radius: 6px;"
       width="900"><br>
  <em>Figure 8: Starting interface assignment and declining VLAN configuration.</em>
</p>

##### 🔷 Phase 2.3 — Assign the WAN Interface

When prompted to assign the WAN interface, I entered:

```text
em0
```

This assigned the available virtual network adapter as the firewall's WAN interface.

When prompted for the LAN interface, I left the field blank and pressed Enter.

The workflow used a simplified single-interface deployment, so a dedicated LAN interface was not required.

When prompted to remove the LAN IP address, I entered:

```text
y
```

to remove the unused LAN configuration.

<p align="left">
  <img src="images/pfsense-firewall-rule-management-09.png"
       alt="Assigning em0 as the WAN interface"
       style="border: 2px solid #444; border-radius: 6px;"
       width="900"><br>
  <em>Figure 9: Assigning em0 as the WAN interface.</em>
</p>

##### 🔷 Phase 2.4 — Verify Interface Configuration

After interface assignment completed, pfSense displayed the active interface configuration.

I reviewed the output and confirmed that:

```text
WAN
    >
em0
```

was active and had successfully received an IP address from the local network.

This IP address would later be used to access the pfSense web management console.

Example:

```text
WAN (em0)
    >
192.168.1.x
```

<p align="left">
  <img src="images/pfsense-firewall-rule-management-10.png"
       alt="Verifying assigned interface information"
       style="border: 2px solid #444; border-radius: 6px;"
       width="900"><br>
  <em>Figure 10: Reviewing assigned interface information and confirming the management IP address.</em>
</p>

##### 🔷 Phase 2.5 — Confirm Management Accessibility

Because the WAN interface now had a valid IP address, I confirmed that the appliance was reachable from the local network.

This represented the first point at which browser-based administration would become possible.

Without a configured interface, the web console could not be reached and subsequent firewall configuration tasks would not be possible.

<blockquote>
Although interface assignment was relatively straightforward, it reinforced an important networking concept: firewall policies only matter after the appliance is properly connected to the network. Before rules can inspect or filter traffic, the firewall must first have a usable interface capable of receiving that traffic.
</blockquote>

##### 🔷 Phase 2.6 — Phase 2 Findings

| Item              | Finding                       |
| ----------------- | ----------------------------- |
| VLANs Configured  | No                            |
| WAN Interface     | em0                           |
| LAN Interface     | Not Assigned                  |
| Management Access | Available                     |
| Next Objective    | Access Web Management Console |

<blockquote>
At the conclusion of this phase, the firewall was reachable on the network and ready for browser-based administration through the pfSense web management interface.
</blockquote>

</details>

<details>
<summary><strong>▶ Phase 3 — Access the pfSense Web Management Console</strong><br>
→ completing initial firewall setup through the browser interface
</summary><br>

This phase focused on accessing the pfSense web management interface and completing the initial appliance configuration using the setup wizard.

<blockquote>
Once the firewall had a functioning network interface and reachable IP address, I moved from the console-based configuration menu to the browser-based management interface. Most day-to-day firewall administration is performed through the web console, so establishing management access was a critical step before configuring policies, gateways, and traffic filtering behavior.
</blockquote>

##### 🔷 Phase 3.1 — Access the pfSense Web Console

After identifying the IP address assigned to the WAN interface, I opened a web browser on the host system and navigated to the firewall's management address.

Example:

```text
https://192.168.1.x
```

The pfSense login page loaded successfully, confirming that the appliance was reachable across the network.

<p align="left">
  <img src="images/pfsense-firewall-rule-management-11.png"
       alt="Accessing the pfSense login portal"
       style="border: 2px solid #444; border-radius: 6px;"
       width="900"><br>
  <em>Figure 11: Accessing the pfSense web management portal.</em>
</p>

##### 🔷 Phase 3.2 — Authenticate to the Firewall

I logged into the management interface using the default credentials:

```text
Username: admin
Password: pfsense
```

Because this was a newly installed appliance, the default administrative credentials were still active.

Successful authentication redirected me to the initial setup wizard.

<p align="left">
  <img src="images/pfsense-firewall-rule-management-12.png"
       alt="Logging into pfSense"
       style="border: 2px solid #444; border-radius: 6px;"
       width="900"><br>
  <em>Figure 12: Authenticating to the pfSense management interface.</em>
</p>

##### 🔷 Phase 3.3 — Launch the Setup Wizard

After authentication, pfSense automatically presented the configuration wizard.

I selected:

```text
Next
```

to begin the initial configuration process.

The wizard provides a guided method for configuring core firewall settings including hostname information, DNS settings, time synchronization, interface addressing, and administrative credentials.

<p align="left">
  <img src="images/pfsense-firewall-rule-management-13.png"
       alt="Starting the pfSense setup wizard"
       style="border: 2px solid #444; border-radius: 6px;"
       width="900"><br>
  <em>Figure 13: Starting the pfSense configuration wizard.</em>
</p>

##### 🔷 Phase 3.4 — Configure General System Settings

Within the wizard, I reviewed the general configuration settings including hostname, domain information, and DNS configuration.

The lab used:

```text
8.8.8.8
```

as the DNS server.

DNS configuration is important because the firewall must be able to resolve hostnames when performing updates, package installations, diagnostics, and certain administrative functions.

<p align="left">
  <img src="images/pfsense-firewall-rule-management-14.png"
       alt="Reviewing general configuration settings"
       style="border: 2px solid #444; border-radius: 6px;"
       width="900"><br>
  <em>Figure 14: Reviewing general system and DNS configuration settings.</em>
</p>

##### 🔷 Phase 3.5 — Review Time Configuration

Next, I reviewed the time synchronization settings presented by the wizard.

I left the default values in place.

Although time synchronization may appear minor, accurate timestamps are extremely important when working with firewall logs, SIEM integrations, incident investigations, and event correlation activities.

Incorrect time settings can make troubleshooting and log analysis significantly more difficult.

<p align="left">
  <img src="images/pfsense-firewall-rule-management-15.png"
       alt="Reviewing time configuration"
       style="border: 2px solid #444; border-radius: 6px;"
       width="900"><br>
  <em>Figure 15: Reviewing time synchronization settings.</em>
</p>

##### 🔷 Phase 3.6 — Configure Static WAN Addressing

The wizard then prompted for WAN interface configuration.

By default, the interface was configured for DHCP addressing.

For the purposes of the workflow, I changed the addressing method to:

```text
Static
```

I then configured the WAN address using the same address currently being used to access the web management console.

I also configured:

```text
Subnet Mask: 24
```

and specified the gateway value provided within the lab instructions.

<blockquote>
At this stage I was beginning to establish how traffic would eventually flow through the appliance. Correct interface addressing and gateway configuration are prerequisites for successful routing and policy enforcement later in the workflow.
</blockquote>

<p align="left">
  <img src="images/pfsense-firewall-rule-management-16.png"
       alt="Configuring static WAN addressing"
       style="border: 2px solid #444; border-radius: 6px;"
       width="900"><br>
  <em>Figure 16: Configuring static WAN addressing.</em>
</p>

##### 🔷 Phase 3.7 — Configure Administrative Credentials

The final section of the wizard prompted me to configure a new administrative password.

I replaced the default password with a new administrator password and continued through the remaining wizard steps.

Although this was a lab environment, changing default credentials remains a fundamental security practice because default passwords are widely known and frequently targeted.

<p align="left">
  <img src="images/pfsense-firewall-rule-management-17.png"
       alt="Changing the administrator password"
       style="border: 2px solid #444; border-radius: 6px;"
       width="900"><br>
  <em>Figure 17: Configuring a new administrator password.</em>
</p>

##### 🔷 Phase 3.8 — Review the Dashboard

After the wizard completed, pfSense reloaded its configuration and redirected me to the dashboard.

The dashboard provided a centralized view of:

* Interface status
* System information
* Resource utilization
* Uptime
* Assigned IP addresses
* Firewall status

Reviewing the dashboard confirmed that the firewall was operational and ready for policy configuration.

<p align="left">
  <img src="images/pfsense-firewall-rule-management-18.png"
       alt="Reviewing the pfSense dashboard"
       style="border: 2px solid #444; border-radius: 6px;"
       width="900"><br>
  <em>Figure 18: Reviewing the pfSense dashboard after initial configuration.</em>
</p>

##### 🔷 Phase 3.9 — Phase 3 Findings

| Item                    | Finding     |
| ----------------------- | ----------- |
| Management Interface    | Accessible  |
| Authentication          | Successful  |
| Setup Wizard            | Completed   |
| DNS Configuration       | Configured  |
| WAN Addressing          | Configured  |
| Administrative Password | Updated     |
| Dashboard Status        | Operational |

<blockquote>
At the conclusion of this phase, the firewall appliance was fully manageable through the browser-based interface and ready for policy creation, alias management, and traffic filtering configuration.
</blockquote>

</details>

<details>
<summary><strong>▶ Phase 4 — Create a Firewall Alias for the Target Destination</strong><br>
→ simplifying policy administration through reusable firewall objects
</summary><br>

This phase focused on creating a reusable firewall alias that would later be referenced by traffic filtering rules.

<blockquote>
Before creating firewall policies, I wanted to create an alias for the target destination. Aliases improve readability and simplify administration because firewall rules can reference a named object rather than relying on hardcoded IP addresses throughout multiple policies.
</blockquote>

##### 🔷 Phase 4.1 — Identify the Target IP Address

Before creating the alias, I needed to identify the IP address associated with the target domain.

From the host system, I opened a command prompt and executed:

```cmd
ping redhunt.net
```

The goal was not to test connectivity but rather to resolve the hostname into an IP address that could be used within firewall policy objects.

Once the address was displayed, I stopped the command.

<p align="left">
  <img src="images/pfsense-firewall-rule-management-19.png"
       alt="Resolving redhunt.net"
       style="border: 2px solid #444; border-radius: 6px;"
       width="900"><br>
  <em>Figure 19: Resolving the target hostname to an IP address.</em>
</p>

##### 🔷 Phase 4.2 — Navigate to the Alias Configuration Page

Within the pfSense web console, I navigated to:

```text
Firewall
    >
Aliases
```

The Aliases page allows administrators to create reusable objects representing hosts, networks, ports, or groups of addresses.

Using aliases makes policy management easier because administrators can modify the underlying object without editing every rule that references it.

<p align="left">
  <img src="images/pfsense-firewall-rule-management-20.png"
       alt="Opening the aliases page"
       style="border: 2px solid #444; border-radius: 6px;"
       width="900"><br>
  <em>Figure 20: Navigating to the Firewall Aliases page.</em>
</p>

##### 🔷 Phase 4.3 — Create the Alias

I selected the option to create a new alias.

For the alias name, I entered:

```text
RedHuntDOTNet
```

I then populated the alias with the IP address identified during the previous step.

This created a reusable firewall object that could later be referenced by filtering rules.

<p align="left">
  <img src="images/pfsense-firewall-rule-management-21.png"
       alt="Creating the RedHuntDOTNet alias"
       style="border: 2px solid #444; border-radius: 6px;"
       width="900"><br>
  <em>Figure 21: Creating the RedHuntDOTNet firewall alias.</em>
</p>

##### 🔷 Phase 4.4 — Save and Apply the Alias

After verifying the alias configuration, I saved the object and applied the changes.

The alias was now available throughout the firewall and could be referenced within future rules.

<blockquote>
Although this was a simple alias containing a single IP address, the same concept scales to large enterprise environments where aliases may contain hundreds or thousands of hosts, networks, or service definitions.
</blockquote>

<p align="left">
  <img src="images/pfsense-firewall-rule-management-22.png"
       alt="Saving and applying alias changes"
       style="border: 2px solid #444; border-radius: 6px;"
       width="900"><br>
  <em>Figure 22: Saving and applying the firewall alias configuration.</em>
</p>

##### 🔷 Phase 4.5 — Review Alias Availability

After saving the configuration, I verified that the alias appeared within the list of available firewall objects.

This confirmed that the alias was ready to be referenced during firewall rule creation.

<p align="left">
  <img src="images/pfsense-firewall-rule-management-23.png"
       alt="Reviewing configured aliases"
       style="border: 2px solid #444; border-radius: 6px;"
       width="900"><br>
  <em>Figure 23: Verifying that the alias was successfully created.</em>
</p>

##### 🔷 Phase 4.6 — Phase 4 Findings

| Item          | Finding                                  |
| ------------- | ---------------------------------------- |
| Target Domain | redhunt.net                              |
| Alias Created | RedHuntDOTNet                            |
| Alias Type    | Host                                     |
| Purpose       | Destination object for firewall policies |
| Status        | Available for rule creation              |

<blockquote>
At the conclusion of this phase, a reusable firewall object representing the target destination had been created and was ready to be incorporated into traffic filtering policies.
</blockquote>

</details>

<details>
<summary><strong>▶ Phase 5 — Create Firewall Rules and Understand Rule Evaluation</strong><br>
→ creating traffic filtering policies and enforcing destination-based restrictions
</summary><br>

This phase focused on creating firewall policies that would selectively block traffic to a specific destination while still allowing general internet connectivity.

<blockquote>
With the firewall deployed, configured, and accessible through the web console, I moved into policy creation. My objective was to block access to a specific destination while allowing all other outbound traffic. This provided an opportunity to explore how firewall rules are created, how pfSense evaluates traffic, and why rule order is critical to successful policy enforcement.
</blockquote>

##### 🔷 Phase 5.1 — Navigate to the Firewall Rules Page

To begin creating policies, I navigated to:

```text
Firewall
    >
Rules
```

This opened the firewall rule management interface where traffic filtering policies can be created, modified, reordered, enabled, or disabled.

Before creating any rules, I reviewed the current configuration to understand the existing policy state.

<p align="left">
  <img src="images/pfsense-firewall-rule-management-24.png"
       alt="Opening the firewall rules page"
       style="border: 2px solid #444; border-radius: 6px;"
       width="900"><br>
  <em>Figure 24: Navigating to the Firewall Rules page.</em>
</p>

##### 🔷 Phase 5.2 — Review Firewall Rule Components

Before creating a policy, I reviewed the available rule properties presented by pfSense.

Some of the most important fields included:

- Action
- Interface
- Protocol
- Source
- Destination
- Logging
- Description

Understanding these fields is important because they determine how the firewall evaluates and processes traffic.

For example:

```text
Source
    >
Who is initiating the connection?

Destination
    >
Where is the traffic attempting to go?
```

The firewall compares these attributes against configured policies and determines whether traffic should be allowed or blocked.

##### 🔷 Phase 5.3 — Create the Block Rule

I selected the option to add a new rule.

My goal was to block traffic destined for the alias created during the previous phase.

I configured the rule using the following settings:

```text
Action: Block
Protocol: Any
Source: 192.168.1.0/24
Destination: RedHuntDOTNet
```

This configuration instructed the firewall to block any traffic originating from the local network when attempting to communicate with the destination represented by the alias.

I intentionally selected:

```text
Protocol: Any
```

because I wanted to block all communication to the destination regardless of protocol.

<blockquote>
At this point, I was no longer working with theoretical firewall concepts. I was creating an actual policy that would later influence real network traffic passing through the appliance.
</blockquote>

<p align="left">
  <img src="images/pfsense-firewall-rule-management-25.png"
       alt="Creating the block rule"
       style="border: 2px solid #444; border-radius: 6px;"
       width="900"><br>
  <em>Figure 25: Creating a rule to block traffic to the RedHuntDOTNet alias.</em>
</p>

##### 🔷 Phase 5.4 — Understand Why Additional Rules Are Required

After creating the block rule, I reviewed how pfSense evaluates traffic.

At this stage, the firewall effectively contained:

```text
Block Traffic to RedHuntDOTNet
```

However, I realized that blocking one destination alone would not provide normal internet access.

Traffic that does not match a valid allow rule may be denied depending on the firewall policy structure.

This meant I also needed a rule allowing general outbound connectivity.

##### 🔷 Phase 5.5 — Create the Allow-All Rule

To permit normal internet access, I created a second rule.

The objective was to allow all traffic that did not match the previous block rule.

I configured the rule using the following settings:

```text
Action: Pass
Protocol: Any
Source: 192.168.1.0/24
Destination: Any
```

This rule created a broad allow policy for outbound traffic.

<p align="left">
  <img src="images/pfsense-firewall-rule-management-26.png"
       alt="Creating the allow-all rule"
       style="border: 2px solid #444; border-radius: 6px;"
       width="900"><br>
  <em>Figure 26: Creating the allow-all rule.</em>
</p>

##### 🔷 Phase 5.6 — Review Rule Processing Order

After both rules were created, I reviewed the order in which they appeared.

The desired order was:

```text
Block RedHuntDOTNet
    >
Allow All Traffic
```

This ordering matters because pfSense evaluates rules from top to bottom.

When traffic is inspected, the firewall stops processing once a matching rule is found.

If the allow-all rule appeared first, traffic destined for RedHuntDOTNet would match the allow rule before reaching the block rule.

The block policy would never be enforced.

<blockquote>
This reinforced one of the most important firewall concepts: a correct rule placed in the wrong location may behave incorrectly.
</blockquote>

<p align="left">
  <img src="images/pfsense-firewall-rule-management-27.png"
       alt="Reviewing firewall rule order"
       style="border: 2px solid #444; border-radius: 6px;"
       width="900"><br>
  <em>Figure 27: Reviewing firewall rule evaluation order.</em>
</p>

##### 🔷 Phase 5.7 — Apply the Configuration Changes

After confirming the rule order, I selected:

```text
Apply Changes
```

This activated the new firewall policies and made them available for enforcement.

At this stage, the policies existed on the firewall, but traffic was not yet being routed through the appliance.

Traffic validation would occur later in the workflow.

<p align="left">
  <img src="images/pfsense-firewall-rule-management-28.png"
       alt="Applying firewall configuration changes"
       style="border: 2px solid #444; border-radius: 6px;"
       width="900"><br>
  <em>Figure 28: Applying the firewall policy changes.</em>
</p>

##### 🔷 Phase 5.8 — Phase 5 Findings

| Item | Finding |
|--------|---------|
| Block Rule | Created |
| Allow Rule | Created |
| Alias Used | RedHuntDOTNet |
| Protocol Scope | Any |
| Rule Evaluation | Top-down processing |
| Policy Status | Active |

<blockquote>
At the conclusion of this phase, the firewall policies required for traffic filtering had been created and activated. The next step involved ensuring the appliance could correctly route traffic toward the internet.
</blockquote>

</details>

<details>
<summary><strong>▶ Phase 6 — Configure and Validate Gateway Routing</strong><br>
→ establishing the traffic path required for firewall enforcement
</summary><br>

This phase focused on reviewing gateway configuration and ensuring pfSense could successfully forward traffic to the upstream router.

<blockquote>
Creating firewall rules alone is not enough to control traffic. The firewall must also understand where allowed traffic should be sent. Before redirecting workstation traffic through pfSense, I needed to verify that the appliance had a valid path to the internet.
</blockquote>

##### 🔷 Phase 6.1 — Review Existing Host Network Configuration

Before modifying any settings, I collected information about the existing network configuration from the host system.

I opened a command prompt and executed:

```cmd
ipconfig /all
```

This allowed me to identify:

- Host IP address
- Default gateway
- DNS server
- Local subnet

Understanding the current configuration helped establish the existing traffic path.

Example:

```text
Host
    >
Router
    >
Internet
```

<p align="left">
  <img src="images/pfsense-firewall-rule-management-29.png"
       alt="Reviewing host network configuration"
       style="border: 2px solid #444; border-radius: 6px;"
       width="900"><br>
  <em>Figure 29: Reviewing the existing host network configuration.</em>
</p>

##### 🔷 Phase 6.2 — Understand the Desired Traffic Path

The goal of the workflow was to place pfSense directly within the network path.

The desired flow would eventually become:

```text
Host
    >
pfSense
    >
Router
    >
Internet
```

This would allow pfSense to inspect and enforce policy on outbound traffic.

However, before redirecting traffic through the appliance, I needed to ensure pfSense itself knew how to reach the router.

##### 🔷 Phase 6.3 — Review Gateway Configuration

Within the pfSense management interface, I navigated to the gateway configuration section.

I reviewed the existing gateway settings configured during the setup wizard.

During review, I noticed that the originally configured gateway value did not match the actual upstream router being used within the environment.

Because gateways determine where traffic is forwarded, an incorrect gateway would prevent successful routing regardless of firewall policy configuration.

<p align="left">
  <img src="images/pfsense-firewall-rule-management-30.png"
       alt="Reviewing gateway configuration"
       style="border: 2px solid #444; border-radius: 6px;"
       width="900"><br>
  <em>Figure 30: Reviewing gateway configuration within pfSense.</em>
</p>

##### 🔷 Phase 6.4 — Configure the Correct Upstream Gateway

Using the information gathered earlier, I configured the correct router address as the upstream gateway.

Example:

```text
Gateway
    >
192.168.1.254
```

This ensured that any traffic permitted by firewall rules would be forwarded toward the correct network device.

<blockquote>
This step reinforced the distinction between routing and filtering. Firewall rules determine whether traffic is allowed. Gateways determine where allowed traffic is sent next.
</blockquote>

<p align="left">
  <img src="images/pfsense-firewall-rule-management-31.png"
       alt="Configuring the upstream gateway"
       style="border: 2px solid #444; border-radius: 6px;"
       width="900"><br>
  <em>Figure 31: Configuring the correct upstream gateway.</em>
</p>

##### 🔷 Phase 6.5 — Validate the Routing Path

After updating the gateway, I reviewed the configuration and confirmed that pfSense now possessed a valid path toward the internet.

At this stage:

```text
pfSense
    >
Router
    >
Internet
```

was operational.

The remaining task would be redirecting workstation traffic so that it traversed the firewall before reaching the router.

##### 🔷 Phase 6.6 — Phase 6 Findings

| Item | Finding |
|--------|---------|
| Host Configuration Reviewed | Yes |
| Default Gateway Identified | Yes |
| Upstream Router Identified | Yes |
| pfSense Gateway Updated | Yes |
| Routing Path Validated | Yes |
| Next Objective | Route workstation traffic through pfSense |

<blockquote>
At the conclusion of this phase, pfSense possessed a valid route to the internet and was ready to become the primary traffic path for the workstation.
</blockquote>

</details>


<details>
<summary><strong>▶ Phase 7 — Route Workstation Traffic Through pfSense</strong><br>
→ positioning the firewall directly within the traffic path
</summary><br>

This phase focused on modifying the workstation's network configuration so that all outbound traffic would traverse the pfSense firewall before reaching the internet.

<blockquote>
At this point, the firewall had a valid interface, active policies, and a functioning upstream gateway. However, the workstation was still communicating directly with the router. In order for the firewall to inspect and enforce policy, I needed to place pfSense directly within the traffic path.
</blockquote>

##### 🔷 Phase 7.1 — Open the Windows Network Configuration Panel

To modify the workstation's network settings, I opened the Run dialog and entered:

```text
ncpa.cpl
```

This launched the Windows Network Connections panel.

From here, I located the active network adapter currently providing internet connectivity.

<p align="left">
  <img src="images/pfsense-firewall-rule-management-32.png"
       alt="Opening Windows Network Connections"
       style="border: 2px solid #444; border-radius: 6px;"
       width="900"><br>
  <em>Figure 32: Opening the Windows Network Connections panel.</em>
</p>

##### 🔷 Phase 7.2 — Access IPv4 Properties

I right-clicked the active network adapter and selected:

```text
Properties
```

Within the adapter settings, I located:

```text
Internet Protocol Version 4 (TCP/IPv4)
```

and opened its configuration window.

This interface allows administrators to manually configure IP addressing, DNS servers, and gateway information.

<p align="left">
  <img src="images/pfsense-firewall-rule-management-33.png"
       alt="Opening IPv4 properties"
       style="border: 2px solid #444; border-radius: 6px;"
       width="900"><br>
  <em>Figure 33: Accessing IPv4 configuration settings.</em>
</p>

##### 🔷 Phase 7.3 — Replace the Existing Default Gateway

Prior to making any changes, the workstation's traffic path resembled:

```text
Host
    >
Router
    >
Internet
```

In this configuration, traffic bypassed the firewall entirely.

To redirect traffic through pfSense, I manually configured the network adapter and replaced the existing default gateway with the pfSense appliance address.

Example:

```text
Default Gateway
    >
192.168.1.251
```

This instructed the operating system to send outbound traffic to pfSense rather than directly to the router.

<p align="left">
  <img src="images/pfsense-firewall-rule-management-34.png"
       alt="Changing the default gateway"
       style="border: 2px solid #444; border-radius: 6px;"
       width="900"><br>
  <em>Figure 34: Configuring the workstation to use pfSense as the default gateway.</em>
</p>

##### 🔷 Phase 7.4 — Review the New Traffic Path

After applying the configuration changes, the traffic path became:

```text
Host
    >
pfSense
    >
Router
    >
Internet
```

This was the desired architecture.

All outbound traffic generated by the workstation would now be forwarded to the firewall first. The firewall would evaluate traffic against its configured policies and determine whether the traffic should be allowed or blocked before forwarding it toward the router.

<blockquote>
This represented the most important architectural change within the workflow. Prior to this step, the firewall existed on the network but was not actively controlling workstation traffic. After this change, pfSense became the enforcement point for outbound communications.
</blockquote>

##### 🔷 Phase 7.5 — Verify Connectivity

After applying the network configuration, I verified that the workstation still maintained connectivity.

Because pfSense had already been configured with a valid upstream gateway, traffic could now successfully traverse:

```text
Host
    >
pfSense
    >
Router
    >
Internet
```

without interruption.

This confirmed that routing through the firewall was functioning correctly.

<p align="left">
  <img src="images/pfsense-firewall-rule-management-35.png"
       alt="Verifying workstation connectivity"
       style="border: 2px solid #444; border-radius: 6px;"
       width="900"><br>
  <em>Figure 35: Verifying connectivity after changing the default gateway.</em>
</p>

##### 🔷 Phase 7.6 — Phase 7 Findings

| Item | Finding |
|--------|---------|
| Windows Network Adapter Modified | Yes |
| Default Gateway Changed | Yes |
| pfSense Used as Gateway | Yes |
| Traffic Path Updated | Yes |
| Connectivity Maintained | Yes |
| Next Objective | Validate firewall policy enforcement |

<blockquote>
At the conclusion of this phase, workstation traffic was successfully traversing the firewall appliance. This positioned pfSense to actively inspect and enforce policy on outbound communications.
</blockquote>

</details>

<details>
<summary><strong>▶ Phase 8 — Validate Firewall Policy Enforcement</strong><br>
→ confirming that traffic filtering behaves as intended
</summary><br>

This phase focused on validating that the firewall policies created earlier were functioning correctly.

<blockquote>
Creating firewall rules does not guarantee successful enforcement. Validation is a critical step in any firewall administration workflow because it confirms that routing, gateway configuration, aliases, and policy logic are all working together as intended.
</blockquote>

##### 🔷 Phase 8.1 — Test General Internet Connectivity

Before testing the blocking rule, I first verified that normal internet connectivity remained available.

The purpose of this test was to confirm that the allow-all rule was functioning correctly and that routing through pfSense had not interrupted general network access.

I browsed to several websites and confirmed successful connectivity.

This indicated that:

- the workstation could reach pfSense,
- pfSense could reach the router,
- the router could reach the internet,
- the allow-all policy was functioning correctly.

<p align="left">
  <img src="images/pfsense-firewall-rule-management-36.png"
       alt="Testing general internet connectivity"
       style="border: 2px solid #444; border-radius: 6px;"
       width="900"><br>
  <em>Figure 36: Confirming general internet connectivity through pfSense.</em>
</p>

##### 🔷 Phase 8.2 — Test Access to the Blocked Destination

Next, I attempted to access:

```text
redhunt.net
```

This traffic was expected to match the block rule created earlier.

When the connection attempt reached the firewall, pfSense evaluated the traffic against the configured rule set.

Because the destination matched the:

```text
RedHuntDOTNet
```

alias, the traffic matched the block rule and was denied.

<p align="left">
  <img src="images/pfsense-firewall-rule-management-37.png"
       alt="Testing blocked destination access"
       style="border: 2px solid #444; border-radius: 6px;"
       width="900"><br>
  <em>Figure 37: Testing access to the blocked destination.</em>
</p>

##### 🔷 Phase 8.3 — Analyze the Result

The validation results demonstrated that the firewall policies behaved as intended.

Traffic destined for the blocked alias was denied, while unrelated internet traffic remained functional.

This confirmed several components simultaneously:

- Alias configuration was correct.
- Rule creation was successful.
- Rule ordering was correct.
- Gateway routing was functioning.
- Workstation traffic was traversing the firewall.
- Policy enforcement was active.

<blockquote>
Successful validation demonstrated that multiple independent configurations were functioning together. A failure in any one area—routing, gateway configuration, alias creation, or rule order—could have prevented the firewall from behaving as expected.
</blockquote>

##### 🔷 Phase 8.4 — Review Rule Processing Behavior

This validation also reinforced how firewall rule evaluation works.

The active rule order remained:

```text
Block RedHuntDOTNet
    >
Allow All Traffic
```

When traffic destined for the blocked alias arrived, the firewall evaluated the first rule, found a match, and immediately stopped processing.

Traffic destined for other locations did not match the block rule and therefore continued to the allow-all rule.

This behavior illustrates the importance of placing specific deny rules above broader allow rules.

<p align="left">
  <img src="images/pfsense-firewall-rule-management-38.png"
       alt="Reviewing firewall rule behavior"
       style="border: 2px solid #444; border-radius: 6px;"
       width="900"><br>
  <em>Figure 38: Reviewing firewall policy behavior during validation.</em>
</p>

##### 🔷 Phase 8.5 — Confirm the Final Architecture

At the conclusion of testing, the final traffic flow resembled:

```text
Host
    >
pfSense
    >
Router
    >
Internet
```

with the firewall acting as the centralized enforcement point.

This architecture allowed the firewall to inspect, permit, or deny traffic according to configured policy before forwarding approved traffic toward the internet.

##### 🔷 Phase 8.6 — Phase 8 Findings

| Item | Finding |
|--------|---------|
| Internet Connectivity | Successful |
| Blocked Destination | Denied |
| Alias Functionality | Confirmed |
| Rule Order | Correct |
| Policy Enforcement | Active |
| Firewall Validation | Successful |

<blockquote>
At the conclusion of this phase, the firewall deployment was fully operational. Traffic successfully traversed the appliance, general internet access remained available, and the destination-specific blocking policy was enforced as expected.
</blockquote>

</details>

---

### Final Validation

The workflow successfully demonstrated deployment, configuration, administration, and validation of a pfSense firewall appliance within a virtualized environment.

The final traffic path was:

```text
Host
    >
pfSense
    >
Router
    >
Internet
```

The final policy behavior was:

```text
Traffic to RedHuntDOTNet
    >
Blocked

All Other Outbound Traffic
    >
Allowed
```

The workflow reinforced several foundational network security concepts, including firewall deployment, interface assignment, gateway routing, alias management, traffic filtering, rule evaluation order, and policy enforcement. Together, these components demonstrated how a firewall can function as a centralized control point responsible for governing communication between systems and external networks.

---

---

### Firewall Rule Processing Summary

One of the most important concepts reinforced during this workflow was the way pfSense evaluates firewall policies.

Firewall rules are processed from top to bottom.

The final rule order was:

```text
Block RedHuntDOTNet
    >
Allow All Traffic
```

When traffic destined for the blocked destination arrived at the firewall, pfSense evaluated the first rule and identified a match. Because a matching rule had been found, rule processing stopped and the traffic was denied.

Traffic destined for any other location did not match the blocking rule. Those connections continued to the next rule and were allowed to proceed.

This demonstrated an important firewall administration concept:

```text
Specific Rules
    >
General Rules
```

More specific deny rules should typically appear above broader allow rules.

If the rule order had been reversed:

```text
Allow All Traffic
    >
Block RedHuntDOTNet
```

traffic destined for the blocked host would have matched the allow rule first and the block rule would never have been evaluated.

<details>
<summary><strong>▶ Why Rule Order Matters</strong><br>
</summary><br>

Firewall rules are evaluated sequentially.

The first matching rule determines the outcome of the connection attempt.

Because of this behavior:

- Specific deny rules should generally appear first.
- Broad allow rules should generally appear later.
- Incorrect rule placement can unintentionally bypass intended security controls.
- Rule review and validation are critical after any firewall policy change.

</details>

---

### Traffic Flow Summary

Before modifying the workstation network configuration, traffic bypassed the firewall entirely.

Initial traffic path:

```text
Host
    >
Router
    >
Internet
```

In this configuration, pfSense existed on the network but did not participate in the workstation's outbound traffic flow.

After updating the workstation's default gateway, the traffic path became:

```text
Host
    >
pfSense
    >
Router
    >
Internet
```

This positioned pfSense directly within the communication path and allowed the appliance to inspect and enforce policy on outbound connections.

<details>
<summary><strong>▶ Why Traffic Path Matters</strong><br>
</summary><br>

A firewall can only inspect traffic that actually traverses it.

Simply deploying a firewall appliance on a network does not automatically provide visibility or control.

Traffic must be routed through the firewall before policy enforcement becomes possible.

This workflow demonstrated how gateway configuration and routing decisions directly influence whether a firewall can perform its intended function.

</details>

---

### Execution Summary

| Phase | Primary Objective | Outcome |
|--------|---------|---------|
| Phase 1 | Deploy pfSense Appliance | Firewall successfully installed |
| Phase 2 | Assign Interfaces | WAN interface configured |
| Phase 3 | Configure Web Management | Initial setup completed |
| Phase 4 | Create Firewall Alias | Target destination alias created |
| Phase 5 | Create Firewall Rules | Block and allow policies configured |
| Phase 6 | Configure Gateway Routing | Upstream routing validated |
| Phase 7 | Redirect Workstation Traffic | Traffic successfully routed through pfSense |
| Phase 8 | Validate Policy Enforcement | Filtering behavior confirmed |

---

### Key Concepts Reinforced

<details>
<summary><strong>▶ Firewall Administration Concepts Demonstrated</strong><br>
</summary><br>

- Firewall deployment and installation
- Interface assignment
- WAN configuration
- Gateway management
- Static addressing
- Alias creation
- Policy administration
- Traffic filtering
- Rule evaluation order
- Routing fundamentals
- Default gateway configuration
- Traffic path analysis
- Policy validation
- Network troubleshooting
- Centralized security controls

</details>

<details>
<summary><strong>▶ Networking Concepts Reinforced</strong><br>
</summary><br>

- Host-to-router communication
- Host-to-firewall communication
- Default gateway behavior
- Layer 3 forwarding
- Network path analysis
- Routing dependencies
- Traffic inspection points
- Network appliance deployment
- Security boundary placement

</details>

<details>
<summary><strong>▶ Security Concepts Reinforced</strong><br>
</summary><br>

- Least privilege network access
- Destination-based filtering
- Centralized policy enforcement
- Security control validation
- Network traffic governance
- Firewall policy design
- Administrative change validation
- Defense-in-depth principles

</details>

---

### Lessons Learned

Several important operational concepts became clear throughout this workflow.

Creating firewall rules alone does not guarantee policy enforcement. Proper routing and gateway configuration must also exist before traffic can be inspected.

Aliases simplify firewall administration by allowing administrators to reference reusable objects rather than embedding individual IP addresses directly into policies.

Firewall rule order significantly impacts behavior. Even correctly written policies may fail if they are evaluated in the wrong sequence.

Most importantly, a firewall can only enforce policy on traffic that traverses the appliance. Proper network placement and gateway configuration are therefore just as important as the rules themselves.

---

### Final Validation

The workflow successfully demonstrated deployment, configuration, administration, and validation of a pfSense firewall appliance within a virtualized environment.

The final traffic path was:

```text
Host
    >
pfSense
    >
Router
    >
Internet
```

The final policy behavior was:

```text
Traffic to RedHuntDOTNet
    >
Blocked

All Other Outbound Traffic
    >
Allowed
```

The completed workflow demonstrated how a firewall can function as a centralized network security control capable of inspecting traffic, enforcing policy, and regulating communication between internal systems and external networks.

The execution reinforced foundational firewall administration concepts including deployment, interface assignment, gateway configuration, alias management, traffic filtering, routing dependencies, policy validation, and rule evaluation behavior that are commonly encountered in enterprise network security environments.
