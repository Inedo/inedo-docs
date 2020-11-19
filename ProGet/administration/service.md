---
title: Service Administration
subtitle: Service Administration
sequence: 500
keywords: proget, administration, service
---

The ProGet Service is key component of ProGet's [architecture](high-availability), and performs a variety of background tasks such as health checks, replication, vulnerability scanning, and package cleanup. It's a standard Windows Service Application, and may be managed and configured using the [Windows Service Manager](https://msdn.microsoft.com/en-us/library/windows/desktop/ms685141(v=vs.85).aspx) or sc.exe as you see fit. 

## Service Configuration {#configuration data-title="Service Configuration"}

By default, the ProGet Service is named `INEDOPROGETSVC`, runs under the `NetworkService` account, and is granted Read, Write, ListDirectory privileges to the following paths, as defined in Advanced Settings: 

{.docs}
 -  `Extensions.CommonCachePath`  
 -  `Extensions.ExtensionsPath`
 -  `Extensions.BuiltInExtensionsPath`
 -  `Extensions.ServiceTempPath`
 -  `Extensions.WebTempPath`
 -  `Storage.PackagesRootPath`

{.attention .best-practice} If you choose to configure this as a custom domain account, it will be important to grant these same privileges. 

## Managing the Service from the Web Application

In addition to stopping, starting, and restarting (on Windows only) the ProGet Service, you can see the service's live logs; this may be helpful when diagnosing problems or working with Inedo's support team to track down unexpected behavior or bugs. On the .NET 5 based Docker image, there is also an option to reload the extensions without the need to restart the service.

 - For distributed installations (load balanced / high availability), you'll need to [configure the service messenger](https://docs.inedo.com/docs/proget/installation/installation-guide/service-messenger) to work over a network connection instead of a local pipe.

 - If you're seeing "The ProGet web application does not have sufficient privileges to query the service status", see [KB#1090: Granting Service Control Privileges for Inedo Product’s Web Applications](https://inedo.com/support/kb/1090/granting-service-control-privileges) for how to resolve the message.

## Task Runners {#task-runners data-title="Task Runners"}

Task runners run well-defined background jobs, on either a periodic and/or manual basis. Many will create sub-tasks that allow you to better visualize what the service is doing. Some task runners can be manually triggered. This should only be needed in debugging purposes, as the Web Application will trigger them in response to certain actions (such as adding a connector), and they are always executed on service start.

| Name | Description |
|---|---|
|**Proxy Configuration Monitor**|updates service proxy settings when proxy configuration is changed; this runs every 16 minutes|
|**Drop Path Monitor**|checks configured drop paths for new packages, processes them, and adds them to the appropriate feed; this runs periodically, as defined by the `Service.DropPathMonitorExecuterThrottle` (60 seconds by default)|
|**Scheduled Job Dispatcher**|monitors the database for scheduled jobs that need to be run, such as retention policy execution, and creates an execution to run them; this runs every 16 minutes|
|**Execution Dispatcher**|monitors the database for executions that are ready to run, and creates a sub-task to run them; this runs periodically, as defined by the `Service.ExecutionDispatcherThrottle` advanced configuration setting (15 seconds by default)|
|**Connector Health Check**|verifies connectivity with connectors and attempts to determine how many packages each one exposes; this runs every hour|
|**Feed Replication**|performs client-based replication for all configured feeds; this runs periodically, as defined by the `Service.FeedReplicationExecuterThrottle` (60 seconds by default)|
|**Docker Upload Cleanup**|deletes orphaned partial uploads of Docker blobs; this runs every 16 minutes|
|**Connector Cache Check**|purges old cached connector metadata and refreshes cached metadata for queries that are frequently used; this runs periodically, as defined by the `Service.MetadataCacheCheckExecuterThrottle` (5 minutes by default)|
|**Multipart Upload Cleanup**|terminates unfinished multipart asset uploads so that partial files will be deleted during feed cleanup; this runs every 16 minutes|
|**Package/Container Scanner**|collects information about package/container usage from an external system, as defined by the `Service.PackageContainerScannerThrottle` (60 minutes by default)|
|**Failover Detection**|when running in a High Availability configuration, detects failover conditions and ensures task runners are running on another server. Configurable on the Cluster Management page (ProGet 5.3.16+)|
|**Service Heartbeat**|periodically pings the ProGet database to verify connectivity every 5 minutes|


## Scheduled Jobs {#scheduled-jobs data-title="Scheduled Jobs"}

Scheduled jobs are often resource-intensive jobs that are run on a user-definable schedule.  They are are either associated with a particular feed, or for the whole system. 

You cannot create scheduled jobs, but you can disable and change their schedule from the Administration > Scheduled Tasks page using the [Quartz Cron Syntax](https://www.quartz-scheduler.net/documentation/quartz-3.x/tutorial/crontrigger.html).

| Name | Scope | Description |
|---|---|---|
|**ExecutionLogRetention** |system|purges execution logs; default schedule is `0 0 0 * * ?` (nightly at 12:00AM)|
|**FeedCleanup**|feed|runs user-defined retention rules on the associated feed; default schedule is `0 0 2 * * ?` (nightly at 02:00AM)|
|**UpdateChecker** |system|checks for ProGet updates; default schedule is `0 0 0 ? * SUN` (Sundays at 12:00AM)|
|**VulnerabilityDownloader**|system|downloads and scans feeds for [vulnerabilities](/docs/proget/compliance/vulnerabilities); default schedule is `0 0 2 * * ?` (nightly at 02:00AM)|
|**BuildMavenFeedIndex**|feed|generates a `nexus-maven-repository-index.gz` against the associated feed; default schedule is `0 0 2 * * ?` (nightly at 02:00AM)|
|**FullMavenConnectorIndex** |feed|downloads and processes `nexus-maven-repository-index.gz` from remote connectors for Maven feeds; default schedule is `0 0 2 ? * SUN` (Sundays at 02:00AM)|
|**DockerGarbageCollection**|system|cleans up orphaned bobs and multipart uploads; default schedule is `0 0 0 * * ?` (nightly at 12:00AM)|

You can stagger these jobs as needed during off-peak hours to ensure minimal disruption, or even disable them and run them manually.

## ProGet.Service.exe CLI

The `ProGet.Service.exe` also contains a command line interface (CLI) to reset the admin account, manage the integrated web server, manually install the service, and can be run interactively for debugging purposes.

The CLI commands are:

| Command | Description |
| --- | --- | --- |
| run | Runs the ProGet service and/or the ProGet web server interactively. |
| install | Installs the ProGet service as a Windows service. |
| installweb | Installs the ProGet integrated web server as a Windows service. |
| uninstall | Uninstalls the ProGet Windows service. |
| uninstallweb | Uninstalls the Otter integrated web server Windows service. |
| listreservations | Displays the URL reservations in the system. |
| reserveurls | Reserves one or more URLs with HTTP.SYS. |
| deletereservations | Deletes one or more URL reservations. |
| resetadminpassword | Switches to the built-in user directory and changes the Admin account password to "Admin". |
