---
title: Security and Access Controls
sequence: 200
keywords: proget, packages, chocolatey
---

Security and access control policies are defined by giving principals (users or groups) permission to perform certain tasks in a certain scope (either feed-specific or globally). For example, you can say "The 'HDARS Developers' group may 'Publish Packages' to the 'Dev' feed."

This feature is available in paid and trial ProGet editions.{.info}

Principals are defined in a user directory, which is either internal (i.e. built-in to ProGet) or external (such as Active Directory and LDAP). This allows you to create a single sign-on experience while letting other members of the organization manage user accounts and group membership.

You can also restrict principals from performing tasks, such as "The 'Developers' group may not 'Publish Packages' to the 'Restricted' feed." These overlapping rules, as well as externally-defined user directory, can be used to model granular access control policies.

## Tasks {#tasks data-title="Tasks"}

ProGet ships with five tasks:

|Task|Description|
|--- |--- |
|Administer|Full control over the ProGet instance|
|Manage Feed|Allows access to manage feed settings, delete packages, and overwrite packages.|
|Promote Packages|Allows access to promote packages to a specified feed of the same feed type. Users granted this task should also be granted at least the View & Download Packages task for the source feed.|
|Publish Packages|Allows access to publish, upload, push, and pull packages from a feed.|
|View & Download Packages|Allows access to view and download packages from a feed.|


### Adding Permissions and Restrictions {#permissions-restrictions data-title="Adding Permissions and Restrictions"}

Tasks are assigned to principals by adding or deleting grants (permissions or restrictions) from the Admin > Security > Tasks page. Grants are comprised of the following:

{.docs}
*   Principal – either a user or group
*   Scope – a specific feed or all feeds
*   Task – what the principal may (or may not) perform

### Task Resolutions {#task-resolutions data-title="Task Resolutions"}

Because you can define both permissions and restrictions at multiple scoping levels, determining whether a user can perform a particular action can be quite complex. Generally speaking, ProGet uses the following guidelines to resolve tasks:

1.  More-specific grants override less-specific
2.  Restrictions override Permissions

For example, consider the following set of rules:

1.  The 'Developers' group may 'Promote Packages'
2.  The 'Developers' group may not Promote Packages to a 'Production' feed

A more natural way to describe this in English might be:

_Developers are allowed to promote packages from any feed except Production._

In this case, the restriction (rule 2) only applies to the Production feed, and in that case, it will override the first grant (rule 1).

### Specific Algorithm  {#specific-algorithm data-title="Specific Algorithm"}

All of the grants that would apply to the attribute demand (e.g. view project, deploy package, etc.) for the specified scope (i.e. specific feed or all feeds) are gathered into a list, and then sorted by comparing each rule's scope using the following priority.

{.docs}
*   User
*   Feed
*   Restriction

For example, a User-specific rule will be sorted above a Feed-specific rule. The first rule is then used; if it is a grant, then the user will be permitted to perform the requested action.

## User Directories  {#user-directories data-title="User Directories"}

A user directory is a collection of users and groups that ProGet can query. They are extensible (which means you can write your own), and ProGet ships with two directories:

{.docs}
*   **Built-In** - The default basic user account system used by new installs of ProGet
*   **LDAP/Single Domain** - Users and groups from an LDAP directory (Active Directory) are used; this can come from multiple domains in an Active Directory forest

Task permissions and restrictions are associated with a user directory, which means that "bob-smith" from the Built-in directory will not necessarily have the same permissions as "bob-smith" from the Active Directory.

Directories are also exclusive; meaning you can only use one at a time. For this reason, it's important to make sure you will have sufficient administer permissions in ProGet for the user directory you are switching to. If you do accidentally lock yourself out, don't worry; you can easily run the ProGet.Service.exe program, and select the reset to Built-In option.

## Built In Directory {#built-in data-title="Built In Directory"}

ProGet's built-in user directory is used by default and initially contains a single user with the username "Admin" and the password "Admin". You can add additional users and groups to this directory from the Admin > Security > Users page.

### Active Directory LDAP {#ad-ldap data-title="Active Directory LDAP"}

This is common to all of our products; check out the [shared documentation](/docs/various/ldap/ldap-active-directory).

## Virtual Privilege Assignments

As of ProGet v5.2.5, privileges may be granted or restricted to the following "catch-all" principals in any user directory:

 - **Everyone** - all users with network access to the system, regardless if they are authenticated or not
 - **Authenticated** - all users who have supplied valid login credentials
 - **Anonymous** - all unauthenticated users with network access to the sytem (i.e. have not logged in)

## Hybrid User Directories

As of ProGet v5.2.6, a hybrid user directory may be used to combine multiple user directories together, resolving principals from one or more existing user directories. This allows ProGet administrators to configure the system such that, for example, user accounts can be defined in ProGet with a fallback to Active Directory.

Visit the [Hybrid User Directories](/docs/various/ldap/combining-with-built-in) documentation for more information.
