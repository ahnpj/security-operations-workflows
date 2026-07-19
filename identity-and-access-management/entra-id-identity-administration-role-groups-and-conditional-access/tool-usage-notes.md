# Tool Usage Notes — Microsoft Entra ID Identity Administration, Role-Assignable Groups, Security Configuration Documentation, and Conditional Access Analysis

This document covers how each portal and feature was used during this workflow, what function it served, why it was the appropriate tool for each task, and common navigation paths and points of confusion worth noting.

---

## Microsoft Entra Admin Center (`entra.microsoft.com`)

**Primary purpose:** Identity object management — creating and modifying users, guests, and groups; assigning directory roles; configuring authentication methods and password protection; managing named locations and Conditional Access policies.

**When to use it:** Any time you need to manage identity objects, directory roles, authentication configuration, or Conditional Access in the Entra tenant.

**When NOT to use it for licensing:** Microsoft 365 license assignment is managed in the Microsoft 365 admin center. The Entra portal will show whether a user holds a license, but the assignment action itself is in the M365 portal.

---

### Entra ID → Users → All Users → + New user → Create new user

**Used for:** Provisioning a new cloud-only internal user account.

**How it was used:**
- Configured the Basics tab: user principal name (`MayaC@lakeshorelabs.onmicrosoft.com`), display name (`Maya Chen`), password, account enabled set to **No**.
- Configured the Properties tab: first name, last name, job title (Financial Analyst), department (Treasury), usage location (Canada), user type (Member).
- Skipped Assignments tab (no group or role assignments at creation time).
- Selected **Review + create → Create**.

**Key navigation note:** The user creation wizard has four tabs — Basics, Properties, Assignments, and Review + create. Usage location is on the Properties tab, not Basics. It is required before license assignment.

**Account enabled = No:** This prevents authentication without removing the object from the directory. The user exists, can hold licenses and memberships, but cannot sign in.

---

### Entra ID → Users → All Users → [User] → Assigned roles → + Add assignments

**Used for:** Assigning the Billing Administrator directory role to an existing user.

**How it was used:**
- Opened `Omar Rahman`'s profile.
- Navigated to **Assigned roles → + Add assignments**.
- Selected Billing Administrator, set assignment type to Active, duration to Permanently assigned, and entered a business justification.
- Selected **Assign**.

**Active vs. Eligible:** Active means the role is usable immediately. Eligible means the user must activate it through Privileged Identity Management when needed.

**Justification field:** The lab environment required a justification for privileged assignments. In production, Privileged Identity Management can enforce this as a policy requirement.

**Navigation note:** Directory role assignment is on the user's profile page under **Assigned roles**, not under Groups or any other section.

---

### Entra ID → Users → + New user → Invite external user

**Used for:** Creating a business-to-business guest identity for an external collaborator.

**How it was used:**
- Selected **+ New user → Invite external user** (separate option from Create new user).
- Entered the external email address (`e.brooks@woodgrovebank.com`), display name (Evelyn Brooks), job title (Advisor), and company name (Woodgrove Bank).
- Left optional settings at defaults (personal message, invitation sending).
- Completed the invitation.

**Create new user vs. Invite external user:** Create new user provisions an internal member identity. Invite external user creates a guest object that authenticates through the external person's own identity provider. They are separate flows within the same + New user menu.

**What happens after invitation:** The guest receives an email with a redemption link. Until the invitation is redeemed, the guest object may show a pending state. Redemption completes the trust relationship.

---

### Entra ID → Groups → All Groups → New group

**Used for:** Creating a role-assignable security group (`InsightsOps`).

**How it was used:**
- Selected **New group**.
- Set Group type to Security, Group name to InsightsOps.
- Set **Microsoft Entra roles can be assigned to the group** to **Yes** — confirmed the warning that this cannot be changed later.
- Set Membership type to Assigned.
- Added `Marcus Lee` as a member during creation.
- Selected **Create**.

**Critical setting — role assignability:** Must be enabled at creation. Cannot be added after. If missed, the group must be recreated. The portal will display a warning about this before creation is confirmed.

**Why the warning appears:** Role-assignable groups receive additional protections because their membership controls who holds directory roles. The warning ensures administrators make this decision intentionally.

---

### Entra ID → Roles & Admins → [Role] → Add assignments

**Used for:** Assigning the Reports Reader directory role to the `InsightsOps` group.

**How it was used:**
- Navigated to **Roles & admins → Reports Reader**.
- Selected **Add assignments**.
- Searched for and selected `InsightsOps`.
- Set assignment type to Active, permanently assigned, entered justification.
- Selected **Assign**.

**Why the group appeared as an eligible principal:** Only role-assignable groups appear in the principal search when assigning directory roles to groups. Ordinary security groups do not appear because they cannot hold directory roles.

**Navigation note:** Role assignment from the role's perspective (Roles & admins → Reports Reader → Add assignments) is different from role assignment from the user's perspective (User profile → Assigned roles → Add assignments). Both paths can achieve direct user role assignment, but group role assignment must be done from the role's perspective.

---

### Entra ID → Groups → All Groups → [Group] → Owners / Members

**Used for:** Adding `Marcus Lee` as owner and `Priya Nair` as member of `Security-Reviewers`.

**How it was used:**
- Opened `Security-Reviewers`.
- Navigated to **Owners → + Add owners** → added `Marcus Lee`.
- Navigated to **Members → + Add members** → added `Priya Nair`.

**Owner vs. Member navigation:** Owners and Members are separate sections within the group's management panel. Adding someone to Owners does not automatically add them to Members and vice versa.

**Why existing entries were not removed:** The task required additions only. Removing existing owners or members was outside scope and would change the environment unnecessarily.

---

### Entra ID → Authentication methods → Password protection

**Used for:** Reviewing smart lockout threshold and duration, custom banned passwords, and on-premises Active Directory password protection settings.

**Navigation path:**
```text
Microsoft Entra admin center
→ Authentication methods (left navigation, under Protection)
→ Password protection
```

**What this page shows:**
- Smart lockout threshold (failed attempts before lockout)
- Smart lockout duration (seconds before next attempt allowed)
- Custom banned password list toggle and entry
- On-premises Active Directory password protection enable toggle
- On-premises mode (Audit or Enforced)

**Read-only task note:** This page was opened to inspect values only. No changes were made. The fields were read to populate the Configuration Data Editor.

---

### Entra ID → Password reset → Authentication methods

**Used for:** Reviewing the number of methods required for self-service password reset.

**Navigation path:**
```text
Microsoft Entra admin center
→ Password reset (left navigation, under Protection)
→ Authentication methods
```

**What this page shows:** The number of authentication methods a user must provide to prove identity during self-service password reset. Security questions may also appear here if enabled.

**Why a second page was required:** This page shows the reset requirement count but does not show the full list of modern authentication method states. The authentication method policy page was needed for that.

---

### Entra ID → Authentication methods → Policies

**Used for:** Reviewing which modern authentication methods are enabled or disabled in the tenant.

**Navigation path:**
```text
Microsoft Entra admin center
→ Authentication methods (left navigation, under Protection)
→ Policies
```

**What this page shows:** Each authentication method (Passkey/FIDO2, Microsoft Authenticator, SMS, Temporary Access Pass, Voice call, Email OTP) and its current enabled/disabled state.

**Important distinction:** An enabled method is available for users to register. It does not mean users have registered it. A user can only use a method for password reset if both the policy enables it AND the user has registered it.

---

### Entra ID → Conditional Access → Named locations

**Used for:** Reviewing country-based named location objects.

**Navigation path:**
```text
Microsoft Entra admin center
→ Conditional Access (left navigation, under Protection)
→ Named locations
```

**What this page shows:** A list of all named locations — both country-based and IP-range-based. The list view shows the name and type of each location.

**How to distinguish country from IP-range locations:** The icon or type indicator next to each entry distinguishes them. Country-based locations show a globe icon or "Countries" type; IP-range locations show a network icon or "IP ranges" type.

**Review process used:**
- Opened each country-based named location.
- Verified selected countries.
- Confirmed Include unknown countries/regions = No.
- Confirmed Mark as trusted location = No.
- Selected **Cancel** (not Save) to exit without modification.

---

### Entra ID → Conditional Access → Policies → What If

**Used for:** Simulating a hypothetical sign-in to evaluate which policies would and would not apply.

**Navigation path:**
```text
Microsoft Entra admin center
→ Conditional Access (left navigation, under Protection)
→ Policies
→ What If (button in the toolbar)
```

**Inputs configured:**
- Identity type: Users
- User: `Noah Bennett`
- Target type: Cloud apps
- Target resource: Microsoft SharePoint Online
- Device platform: iOS
- Client app: Browser
- Sign-in risk: Medium
- IP address: `203.0.113.25`
- Country: Canada

**Unused inputs:** Authentication flow, insider risk, user risk, and device filters were left blank — only the required scenario signals were entered.

**How to read the results:**
- **Policies that will apply:** Conditions matched. If the policy is On, controls are actively enforced. If Report-only, the evaluation is logged but not enforced.
- **Policies that will not apply:** Conditions did not match. The result includes the category that caused non-application (e.g., Users and groups, Applications).

**What If does not do:**
- Does not perform a real sign-in.
- Does not test device compliance state in real time.
- Does not evaluate real-time risk signals from the identity protection engine.
- Does not change policy configuration.

---

### Entra ID → Conditional Access → Policies → [Policy] → Assignments → Users or agents

**Used for:** Reviewing which users each Conditional Access policy targets.

**How it was used:**
- Opened each policy individually.
- Selected **Assignments → Users or agents → Include**.
- Identified whether the scope was a specific user, a selected group, or All users.
- Selected **Cancel** (not Save) to exit without modification.

**Why the summary view was insufficient:** The policy list shows a user count but may not show the actual assigned principals. Opening the Assignments panel reveals whether a policy targets a named user, a group, or All users.

---

## Microsoft 365 Admin Center (`admin.microsoft.com`)

**Primary purpose:** Subscription and license management — assigning and removing Microsoft 365 product licenses for individual users and groups.

**When to use it:** Any time you need to assign or remove a Microsoft 365 license. The Entra admin center will show the license status on a user's profile but does not provide the assignment workflow.

---

### Billing → Licenses → [License] → Assign licenses

**Used for:** Assigning Microsoft 365 E3 to `Maya Chen`.

**Navigation path:**
```text
Microsoft 365 admin center (admin.microsoft.com)
→ Billing (left navigation)
→ Licenses
→ Select the license product
→ Licensed users tab
→ + Assign licenses
→ Search for and select Maya Chen
→ Assign
```

**Why the M365 admin center is used instead of Entra:** Identity objects are managed in Entra ID. Product subscription licenses are managed in the Microsoft 365 admin center. This split reflects the different systems of record for each concern.

**Default service plans:** Unless specific services need to be disabled, accepting the default service plan selection for a license is correct. Disabling unneeded services can be done later with intent.

---

## Configuration Data Editor (Lab-Provided Desktop Application)

**Primary purpose:** Recording current-state tenant configuration values for documentation purposes without modifying the tenant.

**When to use it:** When the task requires evidence documentation of existing settings rather than configuration changes.

**How it was used:**
- Password Protection section: recorded lockout threshold, lockout duration, and custom banned word.
- Password Reset section: recorded methods required and authentication method states.
- Named Locations section: recorded both country-based location names.
- Conditional Access Users section: recorded each policy's user scope.
- What If section: recorded each policy's applies/does-not-apply result and state.
- **Save** was selected and confirmed after each section.

**Critical behavior — always Save and verify the confirmation:** The editor required an explicit Save action to persist each documentation section. Navigating away without saving would lose the recorded values. A success confirmation message confirmed each save.

**Why this tool rather than tenant modification:** The task required gathering evidence, not changing configuration. Using a local editor avoids touching live tenant settings, eliminates risk of accidental modification, and produces a clean external record of the observed configuration.

---

## Common Navigation Notes and Points of Confusion

**"I can't find where to assign a license in the Entra admin center."**
License assignment is in the Microsoft 365 admin center under Billing → Licenses. The Entra portal shows license status on a user's profile page but does not offer the full assignment workflow.

**"I enabled role assignability on a group but can't find that option now."**
Role assignability is set at creation time and cannot be changed afterward. If the group was created without it, a new group must be created with the option enabled.

**"The named location I created doesn't seem to be blocking access."**
Named locations do not enforce access on their own. A Conditional Access policy must reference the named location for it to affect sign-ins.

**"A Report-only policy appeared under 'policies that will apply' in What If — is that correct?"**
Yes. "Will apply" in What If means the conditions matched. Report-only describes enforcement state, not whether the policy evaluates. Report-only policies evaluate and log results but do not enforce their controls.

**"I opened a policy to review its user assignment but I'm not sure if I changed anything."**
If you selected Cancel without changing any field, the policy was not modified. If you accidentally selected Save after opening an edit panel with no changes intended, review the policy's modification timestamp to confirm.

**"The Billing Administrator assignment required a justification — is that always the case?"**
The justification requirement is configured by Privileged Identity Management policy. In environments with PIM configured for role assignments, a justification field may be required. In environments without PIM or for non-PIM assignments, it may be optional.