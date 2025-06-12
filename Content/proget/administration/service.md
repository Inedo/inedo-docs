---
title: "ProGet Service Administration & Executable"
nav-title: "Service Executable"
order: 3
---

The ProGet Service runs the [Integrated Web Server](/docs/installation/windows/web) and performs a variety of background tasks such as health checks, replication, vulnerability scanning, and package cleanup. 

On Linux, this ProGet Service is the entrypoint for the container image and requires no specialized configuration.

## Service Configuration on Windows 

On Windows, the ProGet Service is a standard Windows Service application, and may be managed and configured using the [Windows Service Manager](https://msdn.microsoft.com/en-us/library/windows/desktop/ms685141(v=vs.85).aspx) or sc.exe as you see fit. 

By default, the ProGet Service is named `INEDOPROGETSVC`, runs under the `NetworkService` account, and is granted Read, Write, ListDirectory privileges to the following paths, as defined in Advanced Settings: 

 - `Extensions.BuiltInExtensionsPath`
 - `Extensions.CommonCachePath`  
 - `Extensions.ExtensionsPath`
 - `Storage.PackagesRootPath`
 - `Storage.LocalStoragePath`


::: (INFO) 
If you choose to configure this as a custom domain account, it will be important to grant these same privileges. 
:::

## Managing the Service & Logs

In addition to restarting the ProGet Service, you can see the live output which may be helpful when diagnosing problems or working with our support team to track down unexpected behavior or bugs.


## Task Runners

:::(Internal) (How to Generate)
Click "[DEBUG] Generate TaskRunner Docs" on Admin > Service Page in a DEBUG build
:::

Task runners run well-defined background jobs, on either a periodic and/or manual basis. Many will create sub-tasks that allow you to better visualize what the service is doing. Some task runners can be manually triggered. This should only be needed in debugging purposes, as the Web Application will trigger them in response to certain actions (such as adding a connector), and they are always executed on service start.

| Task Runner | Description 
| - | - 
| **Proxy Configuration Monitor** | Updates service proxy settings when proxy configuration is changed. Runs every 16.67 mins.
| **Drop Path Monitor** | Scans feed drop paths for packages to import. Runs every 60 secs.
| **Scheduled Job Dispatcher** | Creates executions for scheduled jobs based on each job's configured schedule. Runs every 16.67 mins.
| **Execution Dispatcher** | Monitors the database for pending executions that are ready to run. Runs every 15 secs.
| **Feed Replication** | Fetches the state of feed replication sources and determines if there have been any updates. If updates are found, a feed replication execution is started. Runs every 60 secs.
| **Docker Upload Cleanup** | Deletes orphaned partial uploads of Docker blobs. Runs every 16.67 mins.
| **Connector Cache Check** | Purges old cached connector metadata after it has become stale. Runs every 5 mins.
| **Multipart Upload Cleanup** | Terminates unfinished multipart uploads so that partial files will be deleted during feed cleanup. Runs every 16.67 mins.
| **Package/Container Scanner** | Collects information about package/container usage from an external system. Runs every 60 mins.
| **Edge Node Data Publisher** | Publishes package and container download information from an edge node to a hub. Runs every 60 mins.
| **Node Message Cleanup** | When running in a High Availability configuration, purges old control messages sent between nodes. Runs every 10 mins.

## Scheduled Jobs 

:::(Internal) (How to Generate)
Click "[DEBUG] Generate Jobs Docs" on Admin > Scheduled Tasks in a DEBUG build
:::

Scheduled jobs are often resource-intensive jobs that are run on a user-definable schedule.  They are are either associated with a particular feed, connector, or for the whole system. 

You cannot create scheduled jobs, but you can disable and change their schedule from the Administration > Scheduled Tasks.

| Name | Scope | Description 
| - | - | - 
| **BuildMavenFeedIndex** | Feed |  Builds an index file that can be used by clients and IDEs to search the artifacts in a Maven feed. Default schedule is ` daily at 2:00 AM`
| **FullMavenConnectorIndex** | Connector |  Downloads and indexes all metadata from a Maven repository. Default schedule is ` weekly (Sunday) at 2:00 AM`
| **DatabaseBackup** | System |  Saves a backup of the ProGet database on a regular schedule. Default schedule is ` daily at 4:00 AM`
| **PackageAnalyzer** | System |  Scans builds and projects for compliance against policy rules. Default schedule is ` daily at 2:00 AM`
| **ExecutionLogRetention** | System |  Performs various system cleanup including execution logs and old log messages. Default schedule is ` daily at 1:00 AM`
| **UpdateChecker** | System |  Checks inedo.com for updated versions of ProGet. Default schedule is ` weekly (Sunday) at 12:00 AM`
| **VulnerabilityDownloader** | System |  Downloads new vulnerability definitions from Inedo Security Labs. Default schedule is ` daily at 1:00 AM`
| **FeedCleanup** | Feed |  Runs retention rules configured for the feed. Default schedule is ` daily at 12:00 AM`
| **FeedIntegrity** | Feed |  Checks health of feeds and package compliance. Default schedule is ` daily at 1:45 AM`
| **DockerGarbageCollection** | System |  Deletes unreferenced Docker blobs. Default schedule is ` daily at 12:00 AM`

You can stagger these jobs as needed during off-peak hours to ensure minimal disruption, or even disable them and run them manually.

## ProGet Service Executable & CLI

The ProGet Service Executable (`proget.exe` on Windows, `proget` on Linux) contains a command line interface (CLI) to reset the admin account, update the database, manually install the service, and can be run interactively for debugging purposes.

| Command | Description |
| --- | --- | --- |
| `run` | Runs the ProGet service interactively
| `resetadminpassword` | Switches to the built-in user directory and changes the Admin account password to "Admin".
| `upgradedb` | Upgrades the database schema version to this version of ProGet.
| `install` | Installs the ProGet service as a Windows service.
| `uninstall` | Uninstalls the ProGet Windows service.

If you upgraded from ProGet 2024 or earlier, you may also have a `INEDOPROGETWEBSVC` Windows Service registered. It exists only to make it easy to rollback to ProGet 2024 and does nothing except wait for a `Stop` command to terminate.

### Supervisor Process

When the ProGet Service is started, either interactively or as a Windows Service, it will operate as a "supervisor" and additional processes to run the web server and background jobs.

:::(Info)
On Windows, you will see multiple `proget.exe` processes running and may see multiple `postgres.exe` processes. This is expected behavior.
:::

If the integrated PostgreSQL database is enabled, the supervisor process will also launch an PostgreSQL database server, which will result in multiple processes (`postgres.exe` / `postgres`) being created.

### ProGet 2024 and Earlier

ProGet 2024 and earlier versions used two separate Windows Services: one to run the integrated web server (`INEDOPROGETWEBSVC`) and the other to run background jobs (`INEDOPROGETSVC`). If the ProGet web application was hosted in IIS, then the `INEDOPROGETWEBSVC` service would not exist. Unless Optimized Service Mode is enabled, there was no "supervisor" process.

The Service Executable for these versions is named `ProGet.Service.exe` and contains two additional commands:

* `installweb` - Installs the ProGet integrated web server as a Windows service.
* `uninstallweb` - Uninstalls the ProGet integrated web server Windows service.

### Optimized Service Mode

In ProGet 2024.33, "Optimized Service Mode" can be enabled This will run both the web server and background jobs using the `INEDOPROGETSVC` service, similar to ProGet 2025.
