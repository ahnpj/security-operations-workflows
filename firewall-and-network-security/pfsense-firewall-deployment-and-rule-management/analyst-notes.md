# Analyst Notes — pfSense Firewall Rule Management and Traffic Filtering

This file captures informal observations, conceptual explanations, confusion points, and operational takeaways recorded during execution of the pfSense firewall administration workflow.

These notes preserve context that did not fully belong inside the formal workflow execution write-up but was useful for understanding how firewalls operate, how network traffic is routed, how security policies are enforced, and how multiple networking concepts interact to create a functioning security control.

> **Workflow vs Execution vs Conceptual Review (Terminology Used Here)**
>
> * **Workflows** refer to structured network security tasks such as firewall deployment, routing configuration, gateway administration, traffic filtering, and policy validation.
> * **Executions** refer to the hands-on configuration and administration performed within pfSense.
> * **Conceptual Reviews** refer to the interpretation of firewall behavior, routing decisions, policy enforcement, and network traffic flow.

---

### Observations During Execution

One of the biggest realizations during this workflow was how closely firewall administration is tied to networking fundamentals.

Before completing this workflow, it was easy to think of a firewall as simply a device that blocks or allows traffic.

However, the exercise demonstrated that firewalls rely heavily on:

* interfaces,
* routing,
* gateways,
* traffic flow,
* network topology,
* policy evaluation.

The workflow reinforced that a firewall cannot enforce security policies on traffic that never reaches it.

Understanding where traffic travels is often just as important as understanding which rules are configured.

---

### Understanding Firewalls

Initially it was tempting to think of firewall administration primarily in terms of creating rules.

However, the workflow demonstrated that rule creation is only one component of firewall operation.

A firewall is responsible for:

```text
Receiving Traffic
Inspecting Traffic
Evaluating Policies
Forwarding Traffic
```

Each stage depends on proper network configuration.

A firewall may have perfectly configured rules and still fail to function correctly if routing or gateway settings are incorrect.

This reinforced an important lesson:

```text
Policy Enforcement
        Depends On
Proper Traffic Flow
```

---

### Understanding Interfaces

One of the earliest tasks involved assigning network interfaces.

At first this appeared to be a simple configuration step.

However, it highlighted how firewalls separate and control traffic between different network boundaries.

Examples include:

```text
WAN
LAN
DMZ
Guest Network
Server Network
```

Each interface represents a different trust zone.

The firewall evaluates traffic moving between these zones and applies security policies accordingly.

This reinforced the concept that firewalls are not simply filtering devices.

They are traffic control points positioned between networks.

---

### Understanding WAN vs LAN

Another realization involved understanding the practical difference between WAN and LAN interfaces.

Initially it was easy to think of these as simply labels.

However, they represent fundamentally different trust relationships.

In most environments:

```text
WAN = Less Trusted
LAN = More Trusted
```

The firewall sits between these networks and evaluates communications moving between them.

This explains why many firewall policies are built around the question:

```text
Which interface did the traffic enter from?
```

rather than:

```text
Which device generated the traffic?
```

---

### Understanding Routing

One of the most valuable learning points involved routing.

Initially, it seemed that firewall rules would be responsible for most connectivity decisions.

However, routing proved equally important.

A useful realization was:

```text
Firewall Rules Determine
Whether Traffic Is Allowed

Routing Determines
Where Traffic Goes
```

These are related but separate functions.

Traffic can be permitted by a firewall rule yet still fail because the firewall does not know where to send it.

This distinction became particularly important when configuring gateway settings.

---

### Understanding Default Gateways

The role of the default gateway became much clearer during this workflow.

Before the exercise, a default gateway could easily be viewed as just another IP address configured on a device.

However, the gateway ultimately determines the path traffic follows when the destination exists outside the local network.

The workflow reinforced the idea that:

```text
Host
    >
Default Gateway
    >
Destination
```

is often the most important path to understand when troubleshooting connectivity issues.

Many apparent firewall problems can actually originate from routing problems.

---

### Understanding Bridged Networking

Another concept that became clearer during execution was bridged networking.

Initially it seemed like a virtualization setting with little operational significance.

However, bridged networking allowed the pfSense appliance to function as an independent device on the network.

This meant the firewall received:

* its own IP address,
* its own gateway,
* its own network presence.

Without this configuration, many of the traffic flow concepts demonstrated throughout the workflow would have been difficult to observe.

---

### Understanding Aliases

Aliases initially appeared to be a convenience feature.

However, the workflow demonstrated why aliases are commonly used in production environments.

Without aliases:

```text
Rule 1 > IP Address
Rule 2 > IP Address
Rule 3 > IP Address
```

If the address changes, every rule must be updated.

With aliases:

```text
Rule 1 > Alias
Rule 2 > Alias
Rule 3 > Alias
```

Only the alias requires modification.

This reinforced another administrative principle:

```text
Good Administration
        =
Reducing Future Work
```

Aliases improve maintainability and reduce the likelihood of configuration errors.

---

### Understanding Firewall Rules

The firewall rules themselves were relatively straightforward.

However, the workflow highlighted that rules are only effective when they are evaluated correctly.

The primary rule types encountered included:

```text
Pass
Block
Reject
```

Initially these appeared similar.

However:

```text
Pass
    >
Allow Traffic

Block
    >
Silently Drop Traffic

Reject
    >
Drop Traffic And Notify Sender
```

Understanding the distinction helps explain why different environments may choose different enforcement actions.

---

### Understanding Rule Order

Perhaps the most important lesson from the workflow involved rule evaluation order.

Initially it was tempting to assume the firewall reviewed all rules simultaneously.

The workflow demonstrated that rules are evaluated sequentially.

Example:

```text
Rule 1 > Block redhunt.net
Rule 2 > Allow All
```

produces a very different result than:

```text
Rule 1 > Allow All
Rule 2 > Block redhunt.net
```

The first matching rule wins.

This reinforced an important firewall administration principle:

```text
Specific Rules
        Before
Broad Rules
```

---

### Understanding Traffic Flow

The most valuable conceptual takeaway involved visualizing how traffic actually moved through the environment.

Before configuration:

```text
Host
    >
Router
    >
Internet
```

After configuration:

```text
Host
    >
pfSense
    >
Router
    >
Internet
```

This small change fundamentally altered how traffic was handled.

Because traffic now traversed the firewall, policies could be enforced before communications reached their destination.

This reinforced the idea that firewall placement is just as important as firewall configuration.

---

### Understanding Centralized Security Controls

The workflow also highlighted the benefits of centralized policy enforcement.

Without a firewall:

```text
Endpoint A
Endpoint B
Endpoint C
```

would each require their own controls.

With a firewall:

```text
Firewall
    >
Enforces Policy For All Systems
```

This centralization reduces administrative overhead and provides more consistent enforcement.

It also explains why firewalls remain widely used despite the growth of endpoint security technologies.

---

### Challenges and Confusion Encountered

The most confusing portions of the workflow included:

* understanding the difference between routing and filtering,
* understanding why gateway configuration mattered,
* visualizing traffic flow before and after deployment,
* understanding why aliases are useful,
* understanding rule evaluation order,
* distinguishing between firewall problems and routing problems,
* understanding the relationship between interfaces and policy enforcement.

These confusion points ultimately became useful learning opportunities because they required deeper understanding of how network security technologies operate.

---

### Cross-Workflow Connections

This workflow connects to several other security operations and infrastructure security workflows.

It connects to network security because firewalls are often the first line of defense between trusted and untrusted networks.

It connects to incident response because firewall logs frequently provide evidence of malicious communications.

It connects to SIEM and log analysis because firewall telemetry is commonly forwarded to centralized monitoring platforms.

It connects to network segmentation because firewalls are frequently used to restrict communications between different environments.

It connects to identity and access management because firewall policies often work alongside identity-based access controls to restrict access to resources.

---

### Summary

This workflow reinforced that effective firewall administration requires understanding more than just firewall rules.

Interface configuration, routing, gateways, aliases, traffic flow, and policy evaluation all contribute to successful security enforcement.

The project demonstrated how centralized network controls can be used to inspect, filter, and regulate communications between systems while maintaining required connectivity.

Perhaps the most important lesson was that firewall behavior cannot be understood solely through policy configuration. Effective administration requires understanding how traffic moves through the environment, how routing decisions are made, and how security controls are applied throughout that process.
