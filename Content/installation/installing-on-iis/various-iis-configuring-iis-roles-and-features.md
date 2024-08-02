---
title: "Configuring IIS Roles & Features for Inedo Products"
order: 1
---

All Inedo products support hosting with IIS. However, Following Microsoft's guidance to [no longer use IIS for modern .NET applications](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/servers/kestrel), we no longer recommend using IIS to host modern versions (2022+) of our products.  

Instead, you should use the Integrated Web Server, which is our (and Microsoft's) recommended web server. Although IIS is still supported, there are no benefits aside from a more familiar user interface.


## Required Settings

The following minimum settings are required for any Inedo product to be hosted through IIS. To configure them in any supported version of Windows Server, visit the Server Manager and choose the "Manage" button and "Add Roles and Features" to enable the options below.

### Server Roles

The server must be configured as a "Web Server (IIS)" to host IIS websites with the following settings enabled:

 - Web Server (IIS) > Web Server > Application Development > .NET Extensibility 4.6 (or greater)
 - Web Server (IIS) > Web Server > Application Development > ASP.NET 4.6 (or greater)
 - Web Server (IIS) > Management Tools > IIS Management Console
 - Web Server (IIS) > Web Server > Security > Windows Authentication (*optional, required if planning to configure Integrated Windows Authentication in the future*)

### Features

Once the Server Roles above are selected, ensure the following features are also enabled:

 - .NET Framework 4.6 Features > .NET Framework 4.6
 - .NET Framework 4.6 Features > ASP.NET 4.6

Once the Server Manager completes the installation, ProGet, BuildMaster, or Otter may be hosted through IIS. s