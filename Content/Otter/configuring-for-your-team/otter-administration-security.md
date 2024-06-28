---
title: "Security and Access Controls"
order: 1
---

Security and access control policies are defined by giving principals (users or groups) permission to perform certain tasks in a certain scope (either environment-specific or globally).

::: (INFO)
For example, you can say "The 'HDARS Developers' group may 'Configure Environment' to the 'Development environment.'"
:::

Principals are defined in a user directory, which is either internal (i.e. built-in to Otter) or external (such as Active Directory and LDAP). This allows you to create a single sign-on experience while letting other members of the organization manage user accounts and group memberships.

You can also restrict principals from performing tasks, such as "The 'Developers' group may not 'View Environment' for the 'Production' environment." These overlapping rules, as well as externally-defined user directories, can be used to model granular access control policies.

This is a feature of Otter Enterprise. 

## Tasks 

Otter includes four built-in tasks.

Task                  | Description
----------------------|----------
Administrators        | Allows unrestricted access to all functionality within Otter.
Configure Environment | Allows defining and managing of pre-defined servers and their configuration plans.
Orchestrate           | Allows access to all functions of Otter, including defining servers and environments, with the exception of administering Otter (users, passwords, privileges, etc.).
View Environment      | Allows view-only access to all non-sensitive areas.

## Adding Permissions and Restrictions

Tasks are assigned to principals by adding or deleting grants (permissions and restrictions) from the *Admin > Security > Tasks* page. Grants are comprised of the following:

- Principal - either a user or a group
- Scope - a specific environment or all environments
- Task - what the principal may (or may not) perform

## Task Resolution 

Because you can define both permissions and restrictions at multiple scopes, determining whether a user can perform a particular action can be quite complex. Generally speaking, Otter uses the following logic to resolve tasks:

1. More-specific grants override less-specific
2. Restrictions override Permissions

::: (INFO)
For example, consider the following set of rules:

1. The *Developers* group may *Configure Environments*
2. The *Developers* group may not *Configure Environments* for a *Production* environment

A more natural way to describe this in English might be:

*Developers are allowed to configure all environments except Production*

In this case, the restriction (rule 2) only applies to the Production environment, and in that case, it will override the first grant (rule 1).
:::

## Resolution Algorithm 

All the grants that would apply to the attribute demand (e.g. view project, deploy package, etc.) for the specified scope (i.e. specific environment or all environments) are gathered into a list, and then sorted by comparing each rule's scope using the following priority.

- User
- Environment
- Environment Ancestor
- Restriction

:::attention (INFO)
For example, a User-specific rule will be sorted above an Environment-specific rule. The first rule is then used; if it is a grant, then the user will be permitted to perform the requested action.
:::

## Users and Groups 

A *user directory* is a collection of users and groups that Otter can query. They are extensible (which means you can write your own), and Otter ships with two directories:

- Built-In - The default basic user account system used by new installs of Otter.
- LDAP/Single Domain - Users and groups from an LDAP directory (Active Directory) are used; this can come from multiple domains in an Active Directory forest.

Task permissions and restrictions are associated with a user directory, which means that "bob-smith" from the Built-in directory will not necessarily have the same permissions as "bob-smith" from the Active Directory

Directories are exclusive; meaning you can only use one at a time. For this reason, it’s important to make sure you will have sufficient administer permissions in Otter for the user directory you are switching to. If you do accidentally lock yourself out, don’t worry; you can easily run the Otter.Service.exe program, and select the reset to Built-In option.

### Built-In Directory

Otter's built-in user directory is used by default and initially contains a user with the username "Admin". You can add additional users and groups to this directory from the *Admin > Security > Users* page.

### LDAP/Active Directory

This is common to all of our products; check out the [shared documentation](/docs/installation/security-ldap-active-directory/various-ldap-ldap-active-directory).

## Virtual Privilege Assignments

As of Otter v2.2.3, privileges may be granted or restricted to the following "catch-all" principals in any user directory:

 - **Everyone** - all users with network access to the system, regardless if they are authenticated or not
 - **Authenticated** - all users who have supplied valid login credentials
 - **Anonymous** - all unauthenticated users with network access to the sytem (i.e. have not logged in)

## Hybrid User Directories

As of Otter v2.2.3, a hybrid user directory may be used to combine multiple user directories together, resolving principals from one or more existing user directories. This allows Otter administrators to configure the system such that, for example, user accounts can be defined in Otter with a fallback to Active Directory.

Visit the [Hybrid User Directories](/docs/installation/security-ldap-active-directory/various-ldap-combining-with-built-in) documentation for more information.