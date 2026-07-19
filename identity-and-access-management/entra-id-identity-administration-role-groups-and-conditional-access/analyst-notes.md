# Analyst Notes — Microsoft Entra ID Identity Administration, Role-Assignable Groups, Security Configuration Documentation, and Conditional Access Analysis

This document captures administrative reasoning, conceptual explanations, decisions made during the workflow, and observations about how each operation fits into real-world identity and access management practices.

---

## Phase 1 — User Administration

### Why an account can exist while sign-in is disabled

A user object in Entra ID is a directory record. It can exist, hold attributes, receive group memberships, be assigned licenses, and be referenced in policies regardless of whether authentication is allowed. Setting **Account enabled** to No prevents successful interactive sign-in but does not remove the object or any of its relationships.

This matters because organizations frequently need an identity to exist before it is ready to be used. Pre-provisioning allows licenses, group memberships, and role assignments to be configured before the first day of employment. Suspension allows an account to be frozen during an investigation without deleting the history, group relationships, or license assignments. Service identities may be created in a disabled state until credentials are issued through a controlled process.

The key distinction is between the object and its authentication capability. These are controlled separately in Entra ID.

### The difference between a user object, a license, a directory role, and a guest identity

These four things look similar because they all involve users, but they represent different layers of the identity system.

**User object:** The directory record that represents a person or service. It stores identifiers, profile attributes, group memberships, and authentication-related information. Creating a user object does not automatically grant access to anything.

**License:** A subscription product entitlement. Assigning a Microsoft 365 license links the user identity to a purchased subscription and enables the services included in that license. The license is managed through the Microsoft 365 admin center, not the Entra admin center. The user object and the license are separate concepts — a user can exist without a license, and a license does not grant administrative authority.

**Directory role:** Authorization to administer part of the Entra or Microsoft 365 environment. A directory role grants what the identity can manage. It is separate from group membership, application permission, and Azure resource roles. Assigning a role is a privileged action because it changes administrative capability in the tenant.

**Guest identity:** A directory representation of an external person. A guest can authenticate using an identity from another organization and access resources the host tenant has shared. Creating a guest invitation does not automatically grant application access, group membership, or administrative roles — those must be assigned separately.

Understanding which layer is being changed at each step prevents errors, reduces over-provisioning, and makes troubleshooting more efficient.

### Why usage location must be set before license assignment

Microsoft 365 services are subject to regional availability, data sovereignty regulations, and country-specific service plan components. Microsoft uses the usage location attribute to determine which service plan components can legally be provisioned for a given user. If usage location is not set, Microsoft cannot make this determination, and the license assignment will fail for the affected service plans.

This is a common source of confusion when administrators provision accounts quickly and defer profile completion. The symptom — a license that appears to assign successfully but doesn't enable expected services — is often traced back to a missing usage location.

### Active versus eligible role assignment

An **Active** assignment means the role is usable immediately. The assigned principal can exercise the role's permissions without any additional step.

An **Eligible** assignment means the principal is authorized to activate the role when needed, typically through Privileged Identity Management. They must explicitly activate it — providing justification, completing multi-factor authentication, and optionally getting approval — before the role's permissions become available. The activation lasts for a configured time window.

Eligible assignments are part of just-in-time privilege. Persistent administrative access is a standing target: if credentials are compromised, the attacker inherits the role immediately. Eligible assignment reduces this risk by limiting the time window during which the privilege is active.

### Permanent versus time-bound role assignment

A permanent assignment has no configured expiration. A time-bound assignment ends automatically at a specified date. Time-bound assignments reduce the risk of forgotten privileged access accumulating over time. In environments with strong governance, recurring time-bound assignments require renewal and therefore create a regular review opportunity.

### Why business-to-business guests are preferred over creating internal accounts

When an external collaborator — a consultant, auditor, vendor, or partner — needs access to organizational resources, one option is to create an internal user account with a generated credential. Another is to invite the person as a guest using their existing organizational identity.

Guest invitation is generally preferred because it avoids creating credentials that the host organization must manage and eventually clean up. The guest authenticates with their own organization's identity provider, which means password management, MFA enforcement, and credential lifecycle are the home organization's responsibility. The host organization controls only what the guest can access.

It also makes offboarding cleaner. Revoking a guest's access is one targeted action. Disabling and then deleting an internal account that was created for a short-term external engagement requires more steps and leaves more history to manage.

---

## Phase 2 — Group and Role Administration

### Why role assignability must be set during group creation

When creating a group in Microsoft Entra ID, there is an option to enable "Microsoft Entra roles can be assigned to the group." This setting creates a role-assignable group — a group type that receives additional protections and can be selected when assigning supported directory roles.

The critical constraint is that this setting cannot be enabled retroactively. A group created without role assignment enabled cannot be converted into a role-assignable group later. If the group is needed for role assignment, it must be recreated correctly.

The reason for this constraint is a security boundary. Role-assignable groups receive stronger change-control protections: only specific privileged roles can modify the membership and ownership of a role-assignable group. If ordinary groups could be converted into role-assignable groups after creation, it would create an escalation path — an attacker who can modify group membership might be able to elevate a non-privileged group into a privileged one.

### How group membership propagates a directory role

When a directory role is assigned to a role-assignable group, every member of that group inherits the role. The inheritance is automatic and immediate. No additional assignment is needed for individual members.

```text
Reports Reader role
        ↓ (role assigned to group)
InsightsOps group
        ↓ (membership)
Marcus Lee → receives Reports Reader
```

Adding a user to the group grants the role. Removing the user removes the inherited access. The group becomes the administrative control point for that role.

This model matters for operational efficiency: adding a new analyst to the team means adding them to one group rather than assigning the same role to each person individually. It also matters for audit purposes: the role assignment is documented at the group level, and group membership changes are the privileged events to monitor.

### Why group membership in a role-assignable group is a privileged action

Because membership in a role-assignable group propagates a directory role, the ability to change that group's membership is effectively the ability to grant or remove administrative access. A person with rights to manage group membership — including group owners — may be able to add themselves or others to the group and inherit the associated role.

This means:
- Group ownership of a role-assignable group should be restricted and audited.
- Membership changes should be logged and reviewed.
- The group should not have unnecessary owners.

### Owner versus member

An owner can manage the group itself — its membership, settings, and in some configurations its associated resources. A member belongs to the group and receives whatever access, licensing, application assignment, or policy scope is attached to the group.

A user can be both an owner and a member, but the roles are separate. Ownership does not automatically make a person a member, and membership does not automatically make a person an owner.

In the context of a role-assignable group, this distinction is especially important: an owner can grant others the role by adding them to the group, even without holding the role directly.

---

## Phase 3 — Password Security Documentation

### What smart lockout actually prevents and what it doesn't

Smart lockout temporarily blocks authentication attempts associated with suspicious activity after enough failed attempts. It reduces the risk of automated password-guessing attacks — where a script cycles through common passwords against a single account.

What smart lockout does not prevent:
- Password spray attacks, where one common password is tested across many accounts. Each account experiences only one attempt, so lockout doesn't trigger.
- Credential stuffing, where attacker-held credential pairs from data breaches are tested. These are not repeated guesses against one account.
- Attacks that happen more slowly than the lockout threshold.

Smart lockout is one defense layer, not a complete defense. Strong password policies, multi-factor authentication, Conditional Access risk-based policies, and password protection are all needed alongside it.

### Why custom banned passwords complement global protection

Microsoft already blocks globally common and weak passwords. The custom banned password list adds organization-specific terms. Attackers targeting a specific organization often guess words connected to that organization — its name, products, city, mascot, or marketing slogans. A custom list addresses this local risk that global protection does not cover.

The banned password enforcement works through normalization and substring matching, not exact string matching. Adding a banned word blocks common substitutions (replacing letters with numbers or symbols) and variations in the same family.

### Audit mode versus Enforced mode for on-premises password protection

On-premises Active Directory password protection extends Entra ID's banned password checks to domain controllers. When the DC receives a password change or reset, it checks the password against the global and custom banned list before accepting it.

**Audit mode** records whether the password would have been rejected without actually rejecting it. Users can still set weak passwords, but the events are logged. This allows administrators to measure impact — how many users would have been affected? — before enabling enforcement.

**Enforced mode** actually rejects banned passwords. Users who try to set a banned password receive an error and must choose a different one.

Organizations typically deploy Audit before Enforced to avoid unexpected user impact and identify integration issues before they cause failures.

### Why authentication method enablement and user registration are different things

Enabling an authentication method in the authentication method policy makes it available for users to register and use. It does not mean every user has registered it.

A user can only use an authentication method for self-service password reset if:
1. The method is enabled in policy, AND
2. The user has registered that specific method for their account.

This means a tenant can have Microsoft Authenticator enabled but have users who haven't registered it. Those users would fail to complete a password reset that requires Authenticator. The gap between policy availability and actual user registration is a common source of helpdesk escalations during self-service password reset deployments.

---

## Phase 4 — Conditional Access Named Locations

### What a named location is and what it isn't

A named location is a reusable condition object. It labels either a set of IP address ranges or a set of countries. By itself, it does nothing to access.

A Conditional Access policy must reference the named location before it affects sign-ins. The named location provides the geography or network condition; the policy provides the access decision.

```text
Named location alone       = no effect on sign-ins
Named location + CA policy = access decision based on location
```

This distinction prevents the common misunderstanding that "creating a named location blocks those countries." The block (or any other control) only exists when a policy references the named location and applies the appropriate grant or block control.

### The difference between country locations and IP-range locations

**Country-based locations** match sign-in attempts based on IP-to-country geolocation mapping. The administrator selects which countries are included. Optionally, they can include IP addresses that cannot be confidently mapped to any country.

**IP-range locations** match based on specific CIDR ranges defined by the administrator. These represent known networks — corporate offices, VPN exit points, data center egress addresses.

The two types serve different purposes. IP-range locations are useful for identifying known trusted or controlled networks. Country-based locations are useful for identifying high-risk geographies or, conversely, restricting access to expected locations.

### Why the "Include unknown countries/regions" option matters

Some IP addresses cannot be confidently mapped to a country by the geolocation database. This can include carrier-grade NAT addresses, new allocations not yet in the database, or misconfigured reverse-DNS records. Excluding unknown countries means sign-ins from these IPs would not match the named location. Including them means they would.

For a named location used to define trusted access (e.g., "domestic traffic"), including unknown countries could inadvertently trust traffic from ungeolocatable IPs. For a named location used to block high-risk traffic, excluding unknown countries could allow ungeolocatable IPs through even when the intent is broader coverage.

The appropriate setting depends on the intended use of the named location.

### Limitations of country-based detection

Country detection relies on IP geolocation. Several factors reduce its reliability:
- VPNs and proxies route traffic through a server in a different country, so the detected country is the exit node's country, not the user's physical location.
- Mobile carriers may route traffic through regional infrastructure, causing the apparent country to differ from the actual country.
- Cloud services and CDN traffic may show the cloud region as the source.
- Geolocation databases are not perfectly accurate.

Country conditions should be treated as probabilistic signals, not proof of physical presence. They are most useful as one signal among many in a Conditional Access policy, not as a standalone access control.

### Why Cancel was used instead of Save when reviewing named locations

The task required reading and documenting the current configuration. Opening a named location in edit mode and then selecting Save could potentially update the modification timestamp or introduce an unintended change even when no visible field was altered. Using Cancel preserves the exact configuration state and avoids any ambiguity about whether the tenant was modified.

This is a best practice for any read-only review in a production or assessment environment: always close edit panels without saving unless a change was required.

---

## Phase 5 — Conditional Access What If Analysis

### What Conditional Access evaluates and what it doesn't replace

Conditional Access is an authorization layer that evaluates sign-in context after authentication has already occurred. It does not replace authentication — it adds contextual requirements on top of it.

The evaluation uses signals:
- **Who** is signing in (user, role membership, group membership)
- **What** they are accessing (cloud app or resource)
- **Where** they are coming from (network location, country)
- **What device** they are using (platform, compliance state, filter)
- **What client** they are using (browser, native app, legacy protocol)
- **What risk level** applies (sign-in risk, user risk)

Based on these signals, a matching policy applies its grant or session controls — or blocks access entirely.

### The difference between On and Report-only

**On** means the policy is actively enforced. When a real sign-in matches an On policy, the grant controls are applied. If the user doesn't satisfy the controls, access is blocked or challenged.

**Report-only** means the policy evaluates against real sign-ins and logs what it would have done, but does not enforce. Users are not challenged or blocked.

Report-only is a deployment stage, not a permanent state. Organizations use it to measure impact — how many users would be affected, which sign-ins would be blocked, which conditions are triggering — before turning the policy On. During What If evaluation, Report-only policies appear as applicable if they match, but their "will apply" status reflects matching, not enforcement.

### Why a policy might not apply

A Conditional Access policy can fail to apply for several reasons:
- The user is not in the policy's included users or is in the excluded users list.
- The target resource is not in the policy's included apps.
- The device platform doesn't match.
- The location doesn't match.
- The client app type doesn't match.
- The risk level doesn't match.
- The policy is Off.

The What If tool identifies which category caused non-application. This is operationally useful: if a policy intended to protect all users isn't applying to a specific user, the non-application reason points to whether the issue is user scope, resource scope, or a condition mismatch.

### Why scope is as important as controls

A Conditional Access policy with strong controls — requiring compliant devices, phishing-resistant MFA, or blocking legacy protocols — provides no protection for users who are outside its assignment scope. An administrator who reviews only the grant controls without checking the user scope may incorrectly believe the policy protects everyone.

Conversely, a policy assigned to All users with a block control is very broad. If it is inadvertently applied to administrator accounts without an exclusion for emergency-access accounts, it could lock out all privileged access to the tenant — including the accounts that would normally disable or modify the policy.

Scope review — confirming who a policy actually targets and what it excludes — is a critical part of Conditional Access analysis.

### Why the What If tool is preferable to testing with live accounts

Testing Conditional Access by performing real sign-ins has several risks:
- Real sign-ins generate authentication events that may alert security monitoring.
- A policy error could block a test account in ways that are hard to reverse quickly.
- Live testing during business hours may affect production users if policies are misconfigured.
- Repeating tests for multiple user and scenario combinations is slow.

What If simulates the evaluation without creating real sign-in events, modifying policy configuration, or affecting user access. It is faster, safer, and can evaluate multiple scenarios rapidly. Its limitation is that it simulates based on current policy configuration — it doesn't test actual network connectivity, device compliance state, or real-time risk signals.

---

## General — Read-Only Documentation

### Why documentation tasks require read-only discipline

Several phases in this workflow required reviewing configuration and recording it in a documentation utility without modifying the tenant. This is a distinct operational mode from configuration tasks.

The risks of unintentional changes during documentation include:
- Modifying a timestamp that could affect audit evidence.
- Accidentally enabling or disabling a setting by selecting the wrong button.
- Triggering a save that records a no-change modification as an administrative action.
- Changing a policy state that affects user access in real time.

In assessment or audit contexts, unexpected tenant changes can invalidate evidence or create compliance issues. The correct discipline is: open read-only where possible, and cancel rather than save when inspecting editable panels.

This is also why the Configuration Data Editor was used as a separate tool. Filling in a local documentation form avoids touching the live tenant and creates a clean evidence record.