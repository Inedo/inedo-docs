---
title: Security for Free Edition
subtitle: Security for BuildMaster Free Edition
keywords: buildmaster, licensing, security
sequence: 500
---

As of BuildMaster v6.0, BuildMaster Free edition only supports two types of authorization controls:

{.docs}
 - Every authenticated user in the system is authorized to perform any function, effectively making every authenticated user a System Administrator
 - All unauthenticated users have "view-only" access to the system (see Specific Guest Account Tasks for complete list of granted task attributes)
 
In addition, the Free Edition does not support LDAP / Active Directory or Integrated Windows Authentication.

## Specific Guest Account Tasks {#guest-tasks data-title="Specific Guest Account Tasks"}

The Free edition automatically grants the following specific task attributes to unauthenticated users:

{.docs}
-   Applications: View
-   Configuration Files: View Instance
-   Plans: View Contents
-   Builds: View Deployment Logs
-   Script Assets: View Contents
-   SQL Scripts: View Contents

## Anonymous Authentication

In some cases, you may wish to replicate the "all unauthenticated users have view-only access" behavior of the free edition. For example, we do this for our own <a href="https://buildmaster.inedo.com/" target="_blank">public instance of BuildMaster</a>. 

To do this, you can change the `Web.AnonymousAuthenticationEnabled` setting under all settings. Note that the Free Edition will always ignore this value and is always enabled.
