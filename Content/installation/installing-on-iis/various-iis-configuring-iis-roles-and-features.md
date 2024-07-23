---
title: "Configuring IIS Roles & Features for Inedo Products"
order: 1
---

All Inedo products support hosting with IIS, which is the recommended hosting option for the web applications in Enterprise installations.

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

Once the Server Manager completes the installation, ProGet, BuildMaster, or Otter may be hosted through IIS. Visit the manual install documentation for specific instructions and configuration, or refer to the [switching from the Integrated Web Server documentation](/docs/installation/installing-on-iis/various-iis-switching-to-iis) to switch from an installation using the Integrated Web Server.