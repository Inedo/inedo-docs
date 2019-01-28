---
title: Using LDAP/AD Integration
sequence: 10
keywords: ldap,active-directory
---

In addition to its own built-in user module, Inedo's Active Directory Domain integration allows permissions to be granted to users and groups defined in a third-party LDAP directory, or Active Directory domain forest. For an active directory domain, the web server (whether the Integrated Web Server or IIS) may additionally be configured to support Windows Integrated Authentication, removing the need to enter a username and password to log in. Support for this type of authentication **requires** the InedoCore extension to be installed.

## Switching to LDAP/AD {#switching}

The LDAP/AD integration can be enabled from the Administration page via the *Change User Directory* link under the *Security & Authentication* heading. Selecting the *Active Directory (New)* option will present a page that requires entering credentials for an administrator.

**TROUBLESHOOTING**: [Active Directory (New) is not in the list.](/support/documentation/various/ldap/troubleshooting#active-directory-new) {.info}

In new installations, you will need to configure privileges first by clicking the *assign privileges* link (or from the Administration page, clicking *Manage Users & Tasks*, selecting the *Tasks* tab, and ensuring the dropdown user directory is set to *Active Directory (New)*).

On the assign privileges page, select *Add Permission*, and in the *Principles* field, search for a username that will become an administrator. Select the *Administer* task, leave the other fields set to their default, and click *Save Privilege*. Return to the Administration page, select *Change User Directory (LDAP)* again, and now when you select *Active Directory (New)*, enter the credentials of the account previously granted the Administer task. The following username formats are supported:

{.docs}
- username@domain *(recommended)*
- NETBIOSname\\username
- username *(if the server is on a domain and not a forest)*

Once the switch button is clicked, the web server will restart, causing a brief outage in your Inedo product. Refreshing the page will eventually redirect to the login page. Here, enter the credentials of the administrator configured in the previous step, and you will be logged in as an administrator.

**TROUBLESHOOTING**: [Log in fails, can't remember the password, etc.](/support/documentation/various/ldap/troubleshooting#locked-out) {.info}
