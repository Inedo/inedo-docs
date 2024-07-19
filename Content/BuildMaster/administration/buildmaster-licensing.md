---
title: "License Management & Restrictions"
order: 7
---

BuildMaster is licensed by number of users on an annual or perpetual basis. Three editions are available: Free, Basic, and Enterprise. This article describes the differences between each edition and tells you what you need to know about managing your license.

## Free vs. Basic vs. Enterprise

BuildMaster Free has no user limit and includes all the features of BuildMaster Enterprise with two important differences:

1.  Every authenticated user in the system is authorized to perform any function, effectively making every authenticated user a System Administrator
2.  View-only access for all unauthenticated users (see [Specific Guest Account Tasks](/docs/buildmaster/administration/buildmaster-licensing#guest-tasks) for a complete list of granted task attributes)

BuildMaster Basic allows up to 20 users, but does not allow high availability/load balancing or single sign-on authentication.

BuildMaster Enterprise is restricted only by the number of licensed users.

Users in BuildMaster are granted a license the moment they log into the software, making them named users. If a license grant increases the number of named users beyond the number of licensed users specified by the license key, a licensing error is generated that requires administrator intervention.

Named users are listed on the Licensing & Activation page within the software and can be delicensed at any time to allow another user to claim the license or to resolve licensing errors that would occur if named users exceeded the number of users allowed by the license key.

## Entering and Updating License Keys

You can enter a license key on the Administration > Licensing & Activation page. Once a new license key is set, it must be activated. Activation is usually automatic, but manual activation is also possible if automatic activation fails for some reason. A key must be activated when it is first set, and also after major BuildMaster upgrades.

For more information on License Key Management and Activation, visit the Inedo [licensing management docs](/docs/myinedo/activating-a-license-key).

## Users in BuildMaster Basic and Enterprise
BuildMaster is designed to be used by the entire development organization: Developers, Testers, Operations, Management, etc. While not all members need the same access, BuildMaster's granular security options can restrict which groups and individuals can access which features in which applications in which environments.

When a user first logs into BuildMaster, a *Named User* is created in the system. Named Users are used to determine if a license is compliant. Named Users are listed in a table on the Administration > Licensing & Activation page and can be deleted individually; this allows you to restore license compliance if you exceed the maximum number of users allowed. Note that any deleted Named User will be recreated when they log in again.

## Security for BuildMaster Free Edition
BuildMaster Free Edition supports limited access controls. 

1. **Authenticated Users are always admins**; any user who logs into BuildMaster Free Edition can perform any function, which makes any authenticated user a System Administrator

2. **Anonymous users always have read-only access**; unauthenticated users (i.e. not logged in) can view all applications and most of their contents, as they are granted the following task attributes: `Applications_View`, `ConfigurationFiles_ViewInstance`, `Scripts_ViewContents`, `Builds_ViewDeploymentLogs`, and ` SqlScripts_ViewContents`.

3. LDAP / Active Directory,  Integrated Windows Authentication, and Single Sign-on cannot be enabled
