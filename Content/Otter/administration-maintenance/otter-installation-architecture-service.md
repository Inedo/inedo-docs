---
title: "Service Administration"
order: 2
---

The Otter Service is a key component of Otter's [high-availability architecture](/docs/installation/high-availability-load-balancing/high-availability-load-balancing), and is responsible for [executing scripts](/docs/otter/scripting-in-otter/otter-core-concepts-assets), communicating with [agents/servers](/docs/otter/connecting-to-your-servers-with-otter/otter-servers-in-otter), and performing other background tasks.

It's a standard Windows Service Application, and can be managed and configured using the [Windows Service Manager](https://msdn.microsoft.com/en-us/library/windows/desktop/ms685141(v=vs.85).aspx) or sc.exe as you see fit. 

## Service Configuration 

By default, the Otter Service is named `INEDOOTTERSVC`, runs under the `NetworkService` account, and is granted Read, Write, and ListDirectory privileges to the following paths, as defined in Advanced Settings: 

 -  `Extensions.CommonCachePath`  
 -  `Extensions.ExtensionsPath`
 -  `Extensions.BuiltInExtensionsPath`
 -  `System.ServiceTempPath`
 -  `System.WebTempPath`
 
::: (INFO) 
If you choose to configure this as a custom domain account, it will be important to grant these same privileges. 
:::

## Managing the Service from the Web Application

In addition to stopping, starting, and restarting (on Windows only) the Otter Service, you can view live logs from the service; this can be helpful in diagnosing problems or working with Inedo's support team to track down unexpected behavior or bugs.

 - For distributed installations (load balanced / high availability), you'll need to [configure the service messenger](/docs/installation/high-availability-load-balancing/installation-cluster-management) to work over a network connection instead of a local pipe.
 - If you're seeing "The Otter web application does not have sufficient privileges to query the service status", see [KB#1090: Granting Service Control Privileges for Inedo Product’s Web Applications](https://inedo.com/support/kb/1090/granting-service-control-privileges) for instructions on how to resolve the message.



## Task Runners


- **ServerCheckerTimedExecuter** - this runs every hour, performs a lightweight handshake on each remote agent, and upgrades the agent as needed
- **JobDispatcherTimedExecuter** - this runs every minute, checks for scheduled [jobs](/docs/otter/orchestration-server-automation/otter-jobs-templates), and executes those jobs as needed
- **RoutineConfigurationTimedExecuter** - this runs every hour (you can change this with `RoutineConfigurationExecutionThrottle` in All Settings), and executes configuration plans for all servers
- **UpdateCheckerTimedExecuter** - this runs every six hours (you disable this with `Updates.PerformUpdateChecks` in All Settings), and communicates with inedo.com for an updated version of the software

## Otter.Service.exe CLI

The `Otter.Service.exe` also contains a command line interface (CLI) to reset the admin account, manage the integrated web server, manually install the service, and can be run interactively for debugging purposes.

The CLI commands are:

| Command | Description |
| --- | --- | 
| run | Runs the Otter service and/or the Otter web server interactively. |
| install | Installs the Otter service as a Windows service. |
| installweb | Installs the Otter integrated web server as a Windows service. |
| uninstall | Uninstalls the Otter Windows service. |
| uninstallweb | Uninstalls the Otter integrated web server Windows service. |
| listreservations | Displays the URL reservations in the system. |
| reserveurls | Reserves one or more URLs with HTTP.SYS. |
| deletereservations | Deletes one or more URL reservations. |
| resetadminpassword | Switches to the built-in user directory and changes the Admin account password to "Admin". |