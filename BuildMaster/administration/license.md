---
title: License Restrictions
keywords: buildmaster, license
sequence: 10
show-headings-in-nav: true
---

BuildMaster is licensed by number of users on an annual or perpetual basis.

BuildMaster is licensed by number of users on an annual or perpetual basis.

## Licensing Overview {#overview data-title="Licensing Overview"}

BuildMaster Free has no user limit and includes all the features of BuildMaster Enterprise with two important differences:

1.  Every authenticated user in the system is authorized to perform any function, effectively making every authenticated user a System Administrator
2.  "View-only" access for all unauthenticated users (see [Specific Guest Account Tasks](#guest-tasks) for complete list of granted task attributes)

BuildMaster Enterprise is restricted only by the number of licensed users.

Users in BuildMaster are granted a license the moment they log-in to the software, at which point they become named users. If a license grant increases the number of named users beyond the number of licensed users as determined by the license key, a licensing error will be generated and require administrator intervention.

Named users are listed on the "Licensing & Activation" page within the software, and can be delicensed at any time, allowing another user to claim the license, or to fix licensing errors that would occur if named users exceed the number of users permitted by the license key.

## License Keys {#keys data-title="License Keys"}

For more information on License Key Management and Activation, visit the docs [here](/support/documentation/various/licensing/management).

## Previous and Unsupported Versions {#pre-unsupported data-title="Previous and Unsupported Versions"}

Prior to v6.0, BuildMaster Free was restricted to 5 users, 10 applications, and 5 servers. Additionally, this version had the same privilege evaluation as the current BuildMaster Enterprise, i.e. it did not treat free users as administrators.

There have been a few licensing programs that have since been discontinued and are no longer supported:

{.docs}
-   BuildMaster Plus Edition (feature-based licensing model introduced in v3.5)
-   Limited/Approval-only users
-   Inedo VIP program (5 seat Enterprise Annual license)

### Multiple License Keys {#multiple data-title="Multiple License Keys"}

Due in part to the now-deprecated BuildMaster Plus edition, prior to v6.0 multiple license keys were allowed in BuildMaster, but it is recommended that **all keys besides the current one should be deleted** to prevent confusion and ease the upgrade process.

The licensing page makes it clear which license key is in effect, though the the precedence rules could affect the active key in some scenarios, for example, an Enterprise Annual key expires when a Free key exists in the system. The ordering rules are evaluated as follows:

1.  Non-expired keys over expired keys
2.  Non-BuildMaster-Plus keys over Plus keys
3.  Enterprise keys over other types
4.  Enterprise keys with greater user counts over fewer
5.  Expiration dates further into the future over nearer

## Specific Guest Account Tasks {#guest-tasks data-title="Specific Guest Account Tasks"}

The Free edition automatically grants the following specific task attributes to unauthenticated users:

{.docs}
-   Applications: View
-   Configuration Files: View Instance
-   Plans: View Contents
-   Builds: View Deployment Logs
-   Script Assets: View Contents
-   SQL Scripts: View Contents
