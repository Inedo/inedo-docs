---
title: "Windows Integrated Authentication"
order: 4
---

Integrated Authentication is an option that allows transparent user authentication, removing the web-based login prompt. When this option is enabled and working, whatever account you signed on to Windows with, will be used as your login.

:::(Error) (💀  Microsoft is Discontinuing Windows Integrated Authentication)

Although Microsoft hasn't formally deprecated Windows Integrated Authentication (WIA) yet, it's been removed from their reference architectures and is no longer mentioned on any technology roadmaps. There's also been Windows/platform bugs that have impacted WIA that show a clear lack of regression testing in this area.

Fundamentally, WIA is not technologically compatible with modern environments that incorporate Linux, mobile, load-balancers, and many other technologies on Azure and other cloud services. 

We will do our best to continue supporting WIA in our products, but it's clearly a dead technology with no future. You should move away from WIA.
:::

If you Need Help Troubleshooting Integrated Authentication, see [Integrated Authentication Not WorkingReceiving 401 (Not Authorized), always prompted for credentials.](#troubleshooting).

## Supported Feed Types

Windows Integrated authentication is supported in only a few ProGet feed types: NuGet, Chocolatey, PowerShell, Universal Packages, and Asset Directories.

:::(Warning) (⚠ Most Feed Types (npm, Docker, PyPi, etc) do not support Windows Integrated Authentication)

This is not a limitation of ProGet, but of the client tools (`npm`, `docker`, `pip`, etc.) that access the API. No matter how you try to configure these tools, they will continually report a `401` (Not Authorized).

These tools will likely never implement the complicated, Windows-specific authentication protocols required for Windows Integrated Authentication
:::

If you are using a feed type that is not supported by integrated authentication, additional configuration will be required on your web server.

## Enabling WIA in ProGet

To enable Windows Integrated Authentication in ProGet, navigate to Administration > Manage Security, click *change* to the right of **Windows Integrated Authentication**, and enable or disable integrated authentication.

:::(Warning)
Depending on your Web Server configuration, additional server configuration may be required.
:::

When enabled, your Inedo product will attempt to read the username authenticated by Windows and bypass the normal login prompt. If no such username was negotiated, ProGet will act as normal.


## Integrated Web Server with WIA

When using the Integrated Web Server, ProGet will selectively use Windows Integrated for supported feed types. However, you can also disable specific feeds (such as an Asset Directory) if you don't wish to use WIA.

However, depending on the URLs you've configured for ProGet, the Integrated Web Server will behave differently.

:::(Error)
⛔ Don't Use Shared Ports (Non-wildcard Hostnames) with Windows Integrated Authentication
:::


By default, ProGet is bound to a port using a wildcard hostname syntax (e.g. `https://*:8625/;http://*:8624`). However, the Integrated Web Server can be configured to share ports with other Windows applications by specifying a hostname in the [Web Server Urls](/docs/installation/windows/inedohub-configure-products#configuration-tab)  such as `https://proget.corp.local:443/`.

When configuring ProGet to share ports, every request coming to the configured URL will be authenticated by Windows. This means that non-supported feeds (i.e. most of them) cannot be used.

If you *absolutely must* use port sharing, and you *absolutely must* use Windows Integrated Authentication, then consider hosting ProGet in IIS using multiple sites. It's not a great option either, but it's the most likely to work.


## How to Configure WIA in IIS

::: (Warning)
Microsoft no longer recommends using IIS for modern .NET applications; see [HOWTO: Switch to Integrated Web Server from IIS](/docs/installation/installing-on-iis/howto-switch-to-integrated-web-server-from-iis) to learn more.
:::

If you wish to use Windows Integrated Authentication and you are hosting ProGet in IIS, then you'll first need to configure the ProGet site in the IIS manager with the following Authentication settings:
- Anonymous Authentication = disabled
- Windows Authentication = enabled

However, this will cause every request to be authenticated by Windows before it is passed to ProGet. This means that non-supported feeds (i.e. most of them) will be completely unusable. Some users may also be unable to access ProGet, depending on their workstation configuration.

### IIS Workaround: Creating a Second Site in IIS

To bypass Windows Integrated Authentication and use feed types that do not support integrated authentication, you will need to set up a second site in IIS.

This second site will need to use a different application pool, should point to the same directory as the primary site, and have a URL binding that is different from the main site.  

In addition, the site will need the following Authentication settings:
- Anonymous Authentication = enabled
- Windows Authentication = disabled

Once the second site is added, you can configure your client tools to point to the second site for the feeds that do not use integrated authentication.



<h2 id="troubleshooting">Troubleshooting Integrated Authentication</h2>

::: (Warning) (⚠ Reverse Proxies Are Not Supported with Windows Integrated Authentication)
The Windows Integrated Authentication protocol is not designed for reverse-proxies, load balancers, etc., and is not supported by Microsoft/Windows. Although this configuration may work, the reverse-proxy may cause adverse affects on authentication causing an unstable integration.
:::


The underlying mechanisms for Integrated Windows Authentication are handled at the HTTP.SYS layer, which our products do not, nor cannot, access. Our code simply looks for a server variable named `LOGON_USER`, which is added by the web server (IIS or IIS components we use in the self-hosted version). When you enable Integrated Windows Authentication in our products, users will be automatically logged in if the `LOGON_USER` variable is present. 

Understanding this mechanism will help troubleshoot when it goes wrong, but we've also put together [some tips](/docs/installation/security-ldap-active-directory/various-ldap-troubleshooting#iwa-tips){target="_blank"} from our users about what they've found works.

### Behind The Scenes: Integrated Windows Authentication
Integrated Windows Authentication is a very complicated mechanism that requires a properly configured client, server, and domain. To summarize the most common IWA set-up ([Kerberos Authentication](https://technet.microsoft.com/en-us/library/cc772815(v=ws.10).aspx)):

- The web server sends a 401 (unauthorized) response with a specially encrypted HTTP response header
- The web browser decrypts the header, and sends another request with a special token indicating the user name as an HTTP request header
- The web server reads this header and contacts the domain controller to verify the client token
- The web server adds the `LOGON_USER` variable, strips out the special client token, and sends the modified request to the application software (ProGet, BuildMaster, or Otter) for processing

A lot of things can go wrong in this chain, and describing how to troubleshoot and set-up your network is well beyond the scope of this document and our support. But fortunately, since it’s not at all related to our products, there are a myriad of resources for helping debug this; just search *Integrated Windows Authentication not working*.

### TIP: Add BackConnectionHostNames for same-server or self connections

Integrated Windows Authentication often doesn't work when you're doing a same-server connection. For example: browsing to BuildMaster from a browser on your BuildMaster server, using a connector in ProGet to connect to itself, etc.

Setting the `BackConnectionHostNames` registry value may help!

1. In Registry Editor, locate and then click the following registry key: `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Lsa\MSV1_0`
2. Right-click `MSV1_0`, point to New, and then click Multi-String Value.
3. Type `BackConnectionHostNames`, and then press ENTER.
4. Right-click `BackConnectionHostNames`, and then click Modify.
5. In the Value data box, type the host names for the sites that are on the local computer (e.g. `proget.kramerica.local`), one per line, and then click OK.
6. Restart machine

### TIP: Setup a Second site in IIS

Many clients will never support Integrated Windows Authentication, which means they will never be able to connect to your instance. To around this, you can create a second site in IIS that doesn't have Integrated Windows Authentication configured; this will require a different hostname and/or port.

### TIP: Configure a Service Principal Name

You may need to configure a [Service Principal Name](https://docs.microsoft.com/en-us/windows/win32/ad/service-principal-names); Windows is supposed to do this automatically for you when you set up a DNS name, but you may need to use the [`setspn` tool](https://docs.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/cc731241(v%3Dws.11)).
