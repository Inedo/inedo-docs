---
title: LDAP and Active Directory
keywords: buildmaster, ldap
sequence: 70
---

In addition to its own built-in user module, BuildMaster's Active Directory Domain integration allows permissions to be granted to users and groups defined in a third-party LDAP directory, or Active Directory domain forest. For an active directory domain, the BuildMaster web server (whether the Integrated Web Server or IIS) may additionally be configured to support Windows Integrated Authentication, removing the need to enter a username and password to log in.

Support for this type of authentication **requires** the InedoCore extension to be installed.

:::info
**Shared Documentation Note** - instead of copy/pasting the documentation (or creating a whole new set of documentation for LDAP and Active Directory), we've kept the detail pages in the *Various Documentation*, see the pages below:

- [Advanced Configuration](/support/documentation/various/ldap/advanced)
- [Integrated Authentication](/support/documentation/various/ldap/integrated-authentication)
- [Troubleshooting](/support/documentation/various/ldap/troubleshooting)
:::

## Switching to LDAP/AD {#switching data-title="Switching to LDAP/AD"}

The LDAP/AD integration can be enabled from the Administration page via the *Change User Directory* link under the *Security & Authentication* heading. Selecting the *Active Directory (New)* option will present a page that requires entering credentials for a BuildMaster administrator.

**TROUBLESHOOTING**: [Active Directory (New) is not in the list.](/support/documentation/various/ldap/troubleshooting#active-directory-new) {.info}

In new installations, you will need to configure privileges first by clicking the *assign privileges* link (or from the Administration page, clicking *Manage Users & Tasks*, selecting the Tasks tab, and ensuring the dropdown user directory is set to *Active Directory (New)*).

On the assign privileges page, select *Add Permission*, search for a username that will become a BuildMaster administrator, select the *Administer* task, leave *application or group*, and *environment* set to all, and click *Add*. Return to the Administration page, select *Change User Directory (LDAP)* again, and now when you select *Active Directory (New)*, enter the credentials of the account previously granted the Administer task. The following username formats are supported:

{.docs}
- username@domain *(recommended)*
- NETBIOSname\username
- username *(if the BuildMaster server is on a domain and not a forest)*

  Once the switch button is clicked, the web server will restart causing a brief outage in BuildMaster. Refreshing the page will eventually redirect to the login page. Here, enter the credentials of the BuildMaster administrator configured in the previous step, and you will be logged in as an administrator.

**TROUBLESHOOTING**: [Log in fails, can't remember the password, etc.](/support/documentation/various/ldap/troubleshooting#locked-out) {.info}
