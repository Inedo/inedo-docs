---
title: "Integrated Authentication"
order: 2
---

Integrated Authentication is an option that allows transparent user authentication, removing the web-based login prompt. When this option is enabled and working, whatever account you signed on to Windows with, will be used as your login.

:::(Error) (💀  Microsoft is Discontinuing Windows Integrated Authentication)

Microsoft no longer recommends using Windows Integrated Authentication.



:::

If you need Need Help Troubleshooting Integrated Authentication, see [Integrated Authentication Not WorkingReceiving 401 (Not Authorized), always prompted for credentials.](/docs/installation/security-ldap-active-directory#authentication-not-working).
## Feed Support
Integrated authentication is supported in only a sub-set of ProGet's feed types.  This is not a limitation of ProGet itself, but a limitation of the client tools themselves.  The following feed types support integrated authentication:
- NuGet
- Chocolatey
- PowerShell
- Universal Packages
- Asset Directories

If you are using a feed type that is not supported by integrated authentication, additional configuration will be required on your web server.

## Web Server Configuration 
Integrated authentication can be configured for any ProGet instance hosted on Windows using either IIS or the Integrated Web Server.

::: (Warning) (Reverse Proxies Are Not Supported)
ProGet does not support the use of reverse-proxies, load balancers, etc... when enabling integrated authentication. Although this configuration may work, the reverse-proxy may cause adverse affects on authentication causing an unstable integration.
:::

### Using IIS
IIS installations require the following Authentication settings:
- Anonymous Authentication = disabled
- Windows Authentication = enabled

When this is configured, the web server and client will automatically negotiate and authenticate the user requesting the page.  This happens at the IIS level before the request is handled by ProGet.  This prevents ProGet from determining which URLs can bypass integrated authentication. 

#### Configuring IIS To Bypass Integrated Authentication for Unsupported Feed Types
To use feed types that do not support integrated authentication, you will need to set up a second site in IIS that has the following Authentication settings:
- Anonymous Authentication = enabled
- Windows Authentication = disabled

This site will need to use a different application pool, should point to the same directory as the primary site, and has a URL binding that is different from the main site.  Once the second site is added, configure your client tools to point to the second site for the feeds that do not use integrated authentication.

### Using the Integrated Web Server
The Integrated Web Server does not require additional configuration to support integrated authentication on supported feed types.  The integrated web server will uses two technologies to host ProGet; Kestrel and HTTP.sys.  The hosting technology is selected based on the format of the [Web Server Urls](/docs/installation/windows/inedohub-configure-products#configuration-tab) configured in Inedo Hub.  

#### Kestrel (Wildcard Hostnames Only)
Kestrel is the default configuration for the integrated web server.  It allows supported feed types to use integrated authentication and other feed types to use standard authentication.  Kestrel also allows the ability to bypass windows authentication on supported feeds by adding the feed ID to Administration -> Advanced Settings -> `Web.IntegratedAuthenticationBypass`.

Kestrel will be used when the [Web Server Urls](/docs/installation/windows/inedohub-configure-products#configuration-tab) are configured with a URL using a wild card character for the hostname (ex: `http://*:8624/`)

::: (Info) (Note: Kestrel does not support port sharing)
Kestrel does not allow the web server to share ports with multiple web servers.  HTTP.sys or IIS is required to share ports.
:::

#### HTTP.sys (Shared Ports / Non-wildcard Hostnames)

HTTP.sys allows for port sharing between different applications, but when it is configured to use integrated authentication all feed types will be forced to use integrated authentication.  This happens because HTTP.sys will handle integrated authentication at the Windows kernel level, preventing ProGet from determining which URLs can bypass integrated authentication. 

HTTP.sys will be used when the [Web Server Urls](/docs/installation/windows/inedohub-configure-products#configuration-tab) are configured with a URL that specifies a hostname (ex: `http://myprogetserver.com:8624/`).


## User Configuration

To enable integrated authentication, navigate to Administration > Manage Security, click *change* to the right of **Windows Integrated Authentication**, and enable or disable integrated authentication.  

When this is enabled, your Inedo product will attempt to read the `LOGON_USER` server variable, treat that as the already-authenticated user, and not prompt for a name and password. If there is no such server variable, it will prompt as per normal.
