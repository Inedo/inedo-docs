---
title: "Service Administration"
order: 2
---



The Otter Service runs the [Integrated Web Server](/docs/installation/windows/web) and performs executions (script runs, jobs, etc.) and other background tasks including agent updates.

On Linux, this Otter Service is the entrypoint for the container image and requires no specialized configuration.

## Service Configuration on Windows 

On Windows, the Otter Service is a standard Windows Service application, and may be managed and configured using the [Windows Service Manager](https://msdn.microsoft.com/en-us/library/windows/desktop/ms685141(v=vs.85).aspx) or sc.exe as you see fit. 

By default, the Otter Service is named `INEDOOTTERSVC`, runs under the `NetworkService` account, and is granted Read, Write, ListDirectory privileges to the following paths, as defined in Advanced Settings: 

 - `Extensions.BuiltInExtensionsPath`
 - `Extensions.CommonCachePath`  
 - `Extensions.ExtensionsPath`
 -  `System.ServiceTempPath`
 -  `System.WebTempPath`


::: (INFO) 
If you choose to configure this as a custom domain account, it will be important to grant these same privileges. 
:::

## Managing the Service & Logs

In addition to restarting the Otter Service, you can see the live output which may be helpful when diagnosing problems or working with our support team to track down unexpected behavior or bugs.


## Task Runners

:::(Internal) (How to Generate)
Click "[DEBUG] Generate TaskRunner Docs" on Admin > Service Page in a DEBUG build
:::

Task runners run well-defined background jobs, on either a periodic and/or manual basis. Many will create sub-tasks that allow you to better visualize what the service is doing. Some task runners can be manually triggered. This should only be needed in debugging purposes, as the Web Application will trigger them in response to certain actions (such as adding a connector), and they are always executed on service start.

| Task Runner | Description 
| - | - 
| **Proxy Configuration** | Verifies that HTTP proxy settings are correct. Runs every 60.00 mins.
| **Job Dispatcher** | Initiates executions as required for any recurring jobs that are ready to run. Runs every 60 secs.
| **Routine Configuration** | Initiates executions to gather configuration from servers. Runs every 60.00 mins.
| **Server Checker** | Scans servers to verify agent connectivity and automatically pushes agent updates as needed. Runs every 60.00 mins.
| **Execution Dispatcher** | Monitors the database for pending executions that are ready to run. Runs every 30 secs.
| **Update Checker** | Checks for updated versions of Otter and installed extensions. Runs every 360.00 mins.
| **Infrastrucure Sync** | Synchronizes infrastructure with another instance of BuildMaster or Otter. Runs every 60 secs.
| **Log Retention** | Purges old log messages based on the configuration settings. Runs every 1440.00 mins.
| **Raft Analyzer** | Performs routine maintenance on rafts and updates cached metadata about raft items. Runs every 240.00 mins.

## Otter Service Executable & CLI

The Otter Service Executable (`Otter.Service.exe` on Windows, `Otter.Service` on Linux) contains a command line interface (CLI) to reset the admin account, update the database, manually install the service, and can be run interactively for debugging purposes.

| Command | Description |
| --- | --- | --- |
| `run` | Runs the Otter service interactively
| `resetadminpassword` | Switches to the built-in user directory and changes the Admin account password to "Admin".
| `upgradedb` | Upgrades the database schema version to this version of Otter .
| `install` | Installs the Otter service as a Windows service.
| `uninstall` | Uninstalls the Otter Windows service.

If you upgraded from Otter 2024 or earlier, you may also have a `INEDOOTTERWEBSVC` Windows Service registered. It exists only to make it easy to rollback to Otter 2024 and does nothing except wait for a `Stop` command to terminate.

### Supervisor Process

When the Otter Service is started, either interactively or as a Windows Service, it will operate as a "supervisor" and additional processes to run the web server and background jobs.

:::(Info)
On Windows, you will see multiple `Otter.Service.exe` processes running.
<!-- and may see multiple `postgres.exe` processes -->
This is expected behavior.
:::

<!-- 
If the integrated PostgreSQL database is enabled, the supervisor process will also launch an PostgreSQL database server, which will result in multiple processes (`postgres.exe` / `postgres`) being created.
-->
### Otter 2024 and Earlier

Otter 2024 and earlier versions used two separate Windows Services: one to run the integrated web server (`INEDOOTTERWEBSVC`) and the other to run background jobs (`INEDOOTTERSVC`). If the Otter web application was hosted in IIS, then the `INEDOOTTERWEBSVC` service would not exist. Unless Optimized Service Mode is enabled, there was no "supervisor" process.

The Service Executable for these versions is named `Otter.Service.exe` and contains two additional commands:

* `installweb` - Installs the Otter integrated web server as a Windows service.
* `uninstallweb` - Uninstalls the Otter integrated web server Windows service.



