---
title: Hybrid User Directories
keywords: security, users, active-directory, hybrid-directory, ldap
sequence: 100
---

## Use Cases {#use-cases}

A hybrid user directory allows multiple user directories to be combined together, resolving principals from one or more existing user directories. This can be useful for any of the following use-cases:

{.docs}
 - Resolving users from active directory, while allowing additional users or groups to be added without required user accounts or security groups to be added to Active Directory
 - Provide dual authentication method for public-facing servers that are also on an internal Active Directory domain
 - Find users in one of two domains not connected via trust such as an on-prem domain and Azure Cloud AD

### Supported User Directories {#supported-directories}

BuildMaster supports the following types of user directories, any of which can be referenced in a hybrid directory: 

{.docs}
 - **Built-in** - define users and groups in BuildMaster
 - **LDAP / Active Directory** - define permissions against existing users or groups in an existing Windows AD forest or generic LDAP directory
 - **Custom** - because user groups are extensible, a custom user directory built against the [Inedo SDK](/docs/inedosdk/overview) is eligible to be referenced in a hybrid user directory

## Configuring a Hybrid User Directory {#configuring}

To edit or add a hybrid user directory you will need to go to administration section under Security & Authentication then click on Change User Directory (LDAP) and click the **Advanced** button. To edit an existing hybrid user directory, select its name in the user directory table. To add a new hybrid directory click the **Add Hybrid Directory** button, and then choose any combination of existing non-hybrid user directories to be included in the hybrid configuration.

Once the directory has been created, [privileges must be assigned to it](/docs/buildmaster/administration/security#adding-permissions-and-restrictions) because privileges for the contained user directories are not considered when resolving permissions.

## Switching to a Hybrid User Directory {#switching}

In the administration section under Security & Authentication, click on Change User Directory (LDAP). This will prompt you to select or change your user directory option. Choose **Hybrid-Directory**, and enter a username/password combination of a user that has been granted the `Admin_ConfigureBuildMaster` task attribute.

## Task Resolution {#task-resolution}

Principals and privileges are evaluated in directories in the order they are listed in the **Directories** field. This means that if a user is found in the first user directory listed, any subsequent user directories are not considered.

:::{.attention .best-practice}
Because hybrid directories are evaluated in order when searching for principals, it is recommended that the built-in directory is always supplied first. This will prevent the performance hits of searching, for example, Active Directory if a user is specified within BuildMaster.
:::

## Hybrid Directory Limitations {#limitations}

#### Cannot Define Principals

Because a hybrid directory is not permitted to contain any principals of its own, contained users or groups cannot be edited from the *Users & Tasks* page for the hybrid directory. The users and groups must be configured for the configured directories assigned in it.

#### Cannot Reference Another Hybrid Directory

Hybrid directories are not permitted to contain other hybrid directories as part of its user directories configuration.