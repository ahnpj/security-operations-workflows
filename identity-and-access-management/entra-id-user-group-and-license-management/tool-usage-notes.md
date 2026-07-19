# Tool Usage Notes — Microsoft Entra ID User and Group Management, License Assignment, and User Lifecycle Operations

This document covers how each portal and feature was used during this workflow, what function it served, why it was the appropriate tool for each task, and common points of confusion or navigation that are worth noting for future reference.

---

## Microsoft Entra Admin Center (`entra.microsoft.com`)

**Primary purpose:** Identity object management — creating, viewing, modifying, and deleting users, groups, applications, service principals, and directory roles within a Microsoft Entra ID tenant.

**When to use it:** Any time you need to create or manage identity objects. User accounts, security groups, Microsoft 365 groups, guest invitations, app registrations, and directory role assignments all live here.

**When NOT to use it for licensing:** License assignment to users and groups is handled in the Microsoft 365 admin center, not here — even though the licensed users and groups are Entra objects. This is a common source of confusion. The Entra admin center will show you whether a user has a license assigned (on their profile's Licenses tab), but you cannot assign licenses from within the Entra portal.

---

### Identity → Users → All Users

**Used for:** Viewing all active user accounts in the tenant, creating new user accounts, and accessing individual user profiles.

**How it was used:**
- Navigated to **Identity → Users → All Users** to access the user list.
- Selected **+ New user → Create new user** to open the account creation form.
- Completed the required fields: User principal name, Display name, Password, Usage location.
- After creation, returned to **All Users** to confirm the new account appeared in the list.
- Repeated this process for both `Chris Green` and `Dominique Koch`.

**Navigation note:** The **Identity** section is in the left navigation panel. If the left panel is collapsed, look for the person icon or expand the navigation. "All Users" is nested under **Users** within the Identity section.

**Key fields during user creation:**

| Field | What it does | Why it matters |
|---|---|---|
| User principal name | Sets the account's login identity (e.g., ChrisG@yourtenant.onmicrosoft.com) | This is the unique identifier used for authentication |
| Display name | The human-readable name shown in directories and emails | Visible to other users across M365 services |
| Password | Sets the initial password | Can be auto-generated or manually specified |
| Usage location | Declares the user's country/region | Required before any license can be assigned; determines which service plans are available |

**What "cloud identity" means here:** Accounts created through this form are cloud-only identities — they exist only in the Entra directory and are not synchronized from an on-premises Active Directory. This is distinct from directory-synced identities (which originate in on-premises AD) and guest users (which are external identities invited into the tenant).

---

### Identity → Users → Deleted Users

**Used for:** Viewing user accounts that have been deleted within the last 30 days, restoring accounts within that window, and understanding the lifecycle of a deleted identity.

**How it was used:**
- After deleting `Chris Green` from the All Users view, navigated to **Identity → Users → Deleted Users** in the left navigation.
- Located `Chris Green` in the deleted users list.
- Selected the account and chose **Restore user** from the top menu.
- Confirmed the restoration dialog.
- Navigated back to **All Users** to verify `Chris Green` appeared as an active account.

**The 30-day window:** Deleted accounts remain in this list for 30 days. After that window expires, permanent deletion occurs automatically and the account cannot be recovered through any means — not through the portal, PowerShell, or Microsoft support. This list is the only recovery path for a deleted account, and only within that window.

**Roles required:** Restoring or permanently deleting accounts from this list requires one of the following roles: Global Administrator, User Administrator, Partner Tier-1 Support, or Partner Tier-2 Support.

**Permanently deleting from this list:** The same interface that allows restoration also allows permanent deletion of accounts before the 30-day window expires — for example, in compliance scenarios where an account needs to be removed immediately and completely. Use this with caution; permanent deletion from this list is irreversible.

---

### Identity → Groups → All Groups

**Used for:** Viewing all groups in the tenant and creating new Security groups and Microsoft 365 groups.

**How it was used:**
- Navigated to **Identity → Groups → All Groups**.
- Selected **New group** to open the group creation form.
- Created the `Marketing` Security group with Assigned membership.
- Returned to **All Groups** to confirm `Marketing` was visible.
- Repeated the process to create the `Northwest Sales` Microsoft 365 group with an owner and member.
- Noted that the Microsoft 365 group took slightly longer to appear in the list than the Security group, due to backend provisioning of shared resources.

**Key fields during group creation:**

| Field | What it does | Notes |
|---|---|---|
| Group type | Determines whether it's a Security group or Microsoft 365 group | Security groups are for access control and license assignment; Microsoft 365 groups provision shared collaboration resources |
| Group name | The display name of the group | Should reflect the group's purpose clearly |
| Membership type | Assigned (manual) or Dynamic (rule-based) | Dynamic requires Entra ID P1 license |
| Owners | Accounts who can manage the group's membership and settings | At least one owner is recommended; owners do not need admin roles |
| Members | Initial members added at creation time | Additional members can be added after creation |

**Why Microsoft 365 groups take longer to appear:** When a Microsoft 365 group is created, Entra provisions not just the group object but also a shared mailbox, SharePoint site, calendar, and OneNote notebook. This provisioning takes additional time compared to creating a Security group (which only creates the group object itself). Refreshing the All Groups list a few times is normal.

---

### Viewing a user's Licenses tab

**Used for:** Verifying that a license assignment made in the Microsoft 365 admin center has propagated to the user's Entra identity object.

**How it was used:**
- After assigning a license to `Dominique Koch` in the M365 admin center, returned to the Entra admin center.
- Navigated to **Identity → Users → All Users**, selected `Dominique Koch`.
- Selected the **Licenses** section from the user's profile menu.
- Confirmed the assigned license appeared in the Licenses list.

**Why this verification matters:** License assignment in the M365 admin center is the source of truth for billing and subscription management, but verifying the result in the Entra admin center confirms the assignment propagated to the identity layer correctly. In production environments, transient errors or missing prerequisites (like an unset usage location) can cause assignment failures that appear successful in the M365 portal but don't propagate. Cross-portal verification catches these edge cases.

---

## Microsoft 365 Admin Center (`admin.microsoft.com`)

**Primary purpose:** Subscription and license management — assigning, reviewing, and revoking Microsoft 365 licenses for users and groups, and managing billing and subscription settings.

**When to use it:** Any time you need to assign or remove licenses. Neither the Entra admin center nor any other Microsoft portal exposes the full license assignment workflow for groups and individual users in the same way the M365 admin center does.

**Relationship to the Entra admin center:** These are two separate but connected portals. The Entra admin center owns identity objects; the M365 admin center owns license management. A license assigned in the M365 admin center appears on the user's Entra profile (visible in the Licenses tab), but the assignment itself lives in the M365 licensing system.

---

### Billing → Licenses

**Used for:** Viewing available licenses in the tenant and accessing license assignment for both groups and individual users.

**How it was used:**

For group-based assignment:
- Navigated to **Billing → Licenses** in the left navigation.
- Selected an available license from the list.
- Selected the **Groups** tab near the top of the license details page.
- Selected **+ Assign license**.
- Searched for and selected the `Marketing` group.
- Selected **Assign** to confirm.

For individual assignment:
- Returned to **Billing → Licenses**, selected an available license.
- Selected **Licensed users** from the menu near the top.
- Selected **+ Assign licenses**.
- Searched for and selected `Dominique Koch`.
- Selected **Assign**.

**The Groups tab vs. Licensed users tab:**

| Tab | What it manages | When to use it |
|---|---|---|
| **Groups** | Group-based license assignments | When assigning a license to all current and future members of a group |
| **Licensed users** | Individual user license assignments | When assigning a license directly to a specific user |

Both tabs exist within the same license detail view. Navigating between them simply switches the scope of what you're managing without leaving the page.

**What group-based licensing does behind the scenes:** When a license is assigned to a group, Entra ID evaluates the group's current membership and applies the license to every member who has a usage location set and doesn't already hold that license through another assignment. As members are added or removed from the group in the future, Entra automatically grants or reclaims the license accordingly — no manual intervention required.

**What happens if a user is in a licensed group but has no usage location:** The license assignment will fail for that specific user (even though it succeeds for others in the group who have usage locations set). The M365 admin center will typically surface this as an error on the group's license detail view, showing which users failed and why. Resolving it requires setting the usage location on the affected account and allowing the licensing system to retry.

---

## Common Navigation Notes and Points of Confusion

**"I can't find the option to assign a license in the Entra admin center."**
License assignment to users and groups is not available in the Entra admin center — it lives in the Microsoft 365 admin center under Billing → Licenses. The Entra admin center will show you whether a license is assigned (on a user's Licenses profile tab), but assignment and revocation always go through the M365 admin center.

**"I created a Microsoft 365 group but it's not showing up in the All Groups list."**
This is expected behavior. Microsoft 365 groups trigger backend provisioning of shared resources (mailbox, SharePoint, calendar) that takes additional time. Refresh the All Groups list a few times — the group will appear once provisioning completes. This is not an error.

**"I deleted a user and they disappeared from All Users. Where did they go?"**
Deleted accounts move to the **Deleted Users** list under Identity → Users → Deleted Users. They remain there for 30 days and can be restored within that window. After 30 days, permanent deletion occurs automatically.

**"I want to restore a deleted user but the Restore user option is greyed out or missing."**
Restoring deleted users requires the User Administrator, Global Administrator, Partner Tier-1 Support, or Partner Tier-2 Support role. If your account doesn't have one of these roles, you won't be able to restore accounts.

**"A license I assigned in the M365 admin center isn't showing up on the user's Entra profile."**
Check whether the user has a usage location set. If usage location is missing, the license assignment may have failed silently. Set the usage location on the account in the Entra admin center and then return to the M365 admin center to retry the assignment.
