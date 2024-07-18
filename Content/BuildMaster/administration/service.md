---
title: "Service Administration"
order: 5
---

The BuildMaster Service is a key component of BuildMaster's [high-availability architecture](/docs/installation/high-availability-load-balancing/high-availability-load-balancing), and is responsible for [executing scripts](/docs/buildmaster/otterscript-execution-engine/buildmaster-scripts), communicating with [agents/servers](/docs/buildmaster/administration-agents-and-infrastructure/buildmaster-servers), and performing other background tasks.

It's a standard Windows Service Application, and can be managed and configured using the [Windows Service Manager](https://msdn.microsoft.com/en-us/library/windows/desktop/ms685141(v=vs.85).aspx) or sc.exe as you see fit. 

## Service Configuration 

By default, the BuildMaster Service is named `INEDOBMSVC`, runs under the `NetworkService` account, and is granted Read, Write, and ListDirectory privileges to the following paths, as defined in Advanced Settings: 

 -  `Extensions.CommonCachePath`  
 -  `Extensions.ExtensionsPath`
 -  `Extensions.BuiltInExtensionsPath`
 -  `System.ServiceTempPath`
 -  `System.WebTempPath`
 
::: (INFO) 
If you choose to configure this as a custom domain account, it will be important to grant these same privileges. 
:::

## Managing the Service from the Web Application

In addition to stopping, starting, and restarting (on Windows only) the BuildMaster Service, you can view live logs from the service; this can be helpful in diagnosing problems or working with Inedo's support team to track down unexpected behavior or bugs.

 - For distributed installations (load balanced / high availability), you'll need to [configure the service messenger](/docs/installation/high-availability-load-balancing/installation-cluster-management) to work over a network connection instead of a local pipe.

 - If you're seeing "The BuildMaster web application does not have sufficient privileges to query the service status", see [KB#1090: Granting Service Control Privileges for Inedo Product’s Web Applications](https://inedo.com/support/kb/1090/granting-service-control-privileges) for instructions on how to resolve the message.

## Task Runners

Task runners perform well-defined background tasks, either on a periodic and/or manual basis. Many create subtasks that allow you to better see what the service is doing. Some task runners can be triggered manually. This should only be necessary for debugging purposes, as the Web Application will trigger them in response to certain actions (e.g. adding a connector) and they are always executed when the service is started.

| Name | Description |
|---|---|
| Event Listener Dispatcher | monitors BuildMaster events and notifies all configured event listeners when they have occurred |
| Server Checker | periodically checks the status of connected servers, updates process hosts as needed, and marks outdated Inedo agents|
| Execution Dispatcher | monitors the database for executions that are ready to run and creates a subtask to run them; this happens at regular intervals as specified in the ExecutionDispatcher.Frequency configuration setting (5 seconds by default) |
| Update Checker | checks the System.IntegrationUrl (defaults to https://inedo.com/bm) for software and extension updates every 12 hours; this may be disabled by setting the Extensions.NextCheck value to empty |
| Infrastructure Sync | if enabled, queries the configured source for the infrastructure configuration and dispatches an execution when there are changes; this runs periodically as specified by the Infrastructuresync.Frequency configuration setting (60 seconds by default)
| Pipeline Stage Validator | evaluates the automated gates of a given deployment set and marks the time and status of the check; this is triggered via the web |
| Resource Monitor | syncs Git, CI servers, and runs actions when changes are detected
| Scheduled Promotion Dispatcher | promotes builds that are “scheduled as soon as requirements are met” |
| System Execution Cleanup | purges old system executions (retention policy, build trigger, and general/manual) based on configured settings |
| Retention Policy Dispatcher | evaluates retention policies every 10 minutes |
| Raft Analyzer | performs routine maintenance on rafts and updates cached metadata about raft items |


## BuildMaster.Service.exe CLI

The `BuildMaster.Service.exe` also contains a command line interface (CLI) to reset the admin account, manage the integrated web server, manually install the service, and can be run interactively for debugging purposes.

The CLI commands are:

| Command | Description |
| --- | --- | 
| run | Runs the BuildMaster service and/or the BuildMaster web server interactively. |
| install | Installs the BuildMaster service as a Windows service. |
| installweb | Installs the BuildMaster integrated web server as a Windows service. |
| uninstall | Uninstalls the BuildMaster Windows service. |
| uninstallweb | Uninstalls the BuildMaster integrated web server Windows service. |
| listreservations | Displays the URL reservations in the system. |
| reserveurls | Reserves one or more URLs with HTTP.SYS. |
| deletereservations | Deletes one or more URL reservations. |
| resetadminpassword | Switches to the built-in user directory and changes the Admin account password to "Admin". |
