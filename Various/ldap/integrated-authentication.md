---
title: Integrated Authentication
sequence: 20
keywords: ldap,active-directory
---

Integrated Authentication is an option that allows transparent user authentication, removing the web-based login prompt. When this option is enabled and working, whatever account you signed on to Windows with, will be used as your login.

**TROUBLESHOOTING**: [Receiving 401 (Not Authorized), always prompted for credentials.](/docs/various/ldap/troubleshooting#authentication-not-working) {.info}

## Web Server Configuration {#web-server-config}

The Integrated Web Server does not require additional configuration to support integrated authentication.

IIS installations require the following Authentication settings:

{.docs}
- Anonymous Authentication = disabled
- Windows Authentication = enabled

When this is configured, the web server and client will automatically negotiate and authenticate the user requesting the page.

## User Configuration {#user-config}

To enable integrated authentication, click the *Integrated Authentication* link on the Administration page.

When this is checked, your Inedo product will attempt to read the `LOGON_USER` server variable, treat that as the already-authenticated user, and not prompt for a name and password. If there is no such server variable, it will prompt as per normal.
