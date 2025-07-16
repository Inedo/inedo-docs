---
title: "Service Administration"
order: 5
---


The BuildMaster Service runs the [Integrated Web Server](/docs/installation/windows/web) and performs executions (builds, deployments, etc.) and other background tasks including agent updates and retention policies.

On Linux, this BuildMaster Service is the entrypoint for the container image and requires no specialized configuration.

## Service Configuration on Windows 

On Windows, the BuildMaster Service is a standard Windows Service application, and may be managed and configured using the [Windows Service Manager](https://msdn.microsoft.com/en-us/library/windows/desktop/ms685141(v=vs.85).aspx) or sc.exe as you see fit. 

By default, the BuildMaster Service is named `INEDOBMSVC`, runs under the `NetworkService` account, and is granted Read, Write, ListDirectory privileges to the following paths, as defined in Advanced Settings: 

 - `Extensions.BuiltInExtensionsPath`
 - `Extensions.CommonCachePath`  
 - `Extensions.ExtensionsPath`
 -  `System.ServiceTempPath`
 -  `System.WebTempPath`


::: (INFO) 
If you choose to configure this as a custom domain account, it will be important to grant these same privileges. 
:::

## Managing the Service & Logs

In addition to restarting the BuildMaster Service, you can see the live output which may be helpful when diagnosing problems or working with our support team to track down unexpected behavior or bugs.


## Task Runners

:::(Internal) (How to Generate)
Click "[DEBUG] Generate TaskRunner Docs" on Admin > Service Page in a DEBUG build
:::

Task runners run well-defined background jobs, on either a periodic and/or manual basis. Many will create sub-tasks that allow you to better visualize what the service is doing. Some task runners can be manually triggered. This should only be needed in debugging purposes, as the Web Application will trigger them in response to certain actions (such as adding a connector), and they are always executed on service start.

| Task Runner | Description 
| - | - 
| **Event Listener Dispatcher** | Monitors BuildMaster events and notifies any configured event listeners when they have occurred. Runs every 60 secs.
| **Server Checker** | Scans servers to verify agent connectivity and automatically pushes agent updates as needed. Runs every 60.00 mins.
| **Execution Dispatcher** | Monitors the database for pending executions that are ready to run. Runs every 5 secs.
| **Update Checker** | Checks for updated versions of BuildMaster and installed extensions. Runs every 720.00 mins.
| **Infrastructure Sync** | Synchronizes infrastructure with another instance of BuildMaster or Otter. Runs every 60 secs.
| **Pipeline Stage Validator** | Checks pipeline approval and requirement states. Runs every 3.00 mins.
| **Resource Monitor** |  Monitors Git repositories, CI servers, and other resources for new activity like commits and builds, then triggers an action like creating a build or deployment. Runs every 60 secs.
| **Scheduled Promotion Dispatcher** | Promotes builds that have been scheduled. Runs every 2.00 mins.
| **System Execution Cleanup** | Purges old system executions (retention policy, build trigger, and general/manual) based on configured settings. Runs every 1440.00 mins.
| **Retention Policy Dispatcher** | Runs retention policies. Runs every 10.00 mins.
| **Raft Analyzer** | Performs routine maintenance on rafts and updates cached metadata about raft items. Runs every 240.00 mins.
| **Container Scanner** | Scans Docker hosts for running containers and gathers information about them. Runs every 60.00 mins.
| **Node Message Cleanup** | When running in a High Availability configuration, purges old control messages sent between nodes. Runs every 10.00 mins.



## BuildMaster Service Executable & CLI

The BuildMaster Service Executable (`BuildMaster.Service.exe` on Windows, `BuildMaster.Service` on Linux) contains a command line interface (CLI) to reset the admin account, update the database, manually install the service, and can be run interactively for debugging purposes.

| Command | Description |
| --- | --- | --- |
| `run` | Runs the BuildMaster service interactively
| `resetadminpassword` | Switches to the built-in user directory and changes the Admin account password to "Admin".
| `upgradedb` | Upgrades the database schema version to this version of BuildMaster .
| `install` | Installs the BuildMaster service as a Windows service.
| `uninstall` | Uninstalls the BuildMaster Windows service.

If you upgraded from BuildMaster 2024 or earlier, you may also have a `INEDOBMWEBSVC` Windows Service registered. It exists only to make it easy to rollback to BuildMaster 2024 and does nothing except wait for a `Stop` command to terminate.

### Supervisor Process

When the BuildMaster Service is started, either interactively or as a Windows Service, it will operate as a "supervisor" and additional processes to run the web server and background jobs.

:::(Info)
On Windows, you will see multiple `BuildMaster.Service.exe` processes running.
<!-- and may see multiple `postgres.exe` processes -->
This is expected behavior.
:::

<!-- 
If the integrated PostgreSQL database is enabled, the supervisor process will also launch an PostgreSQL database server, which will result in multiple processes (`postgres.exe` / `postgres`) being created.
-->
### BuildMaster 2024 and Earlier

BuildMaster 2024 and earlier versions used two separate Windows Services: one to run the integrated web server (`INEDOBMWEBSVC`) and the other to run background jobs (`INEDOBMSVC`). If the BuildMaster web application was hosted in IIS, then the `INEDOBMWEBSVC` service would not exist. Unless Optimized Service Mode is enabled, there was no "supervisor" process.

The Service Executable for these versions is named `BuildMaster.Service.exe` and contains two additional commands:

* `installweb` - Installs the BuildMaster integrated web server as a Windows service.
* `uninstallweb` - Uninstalls the BuildMaster integrated web server Windows service.