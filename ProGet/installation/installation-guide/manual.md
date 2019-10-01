---
title: ProGet Manual Installation Guide
sequence: 600
show-headings-in-nav: true
keywords: proget, installation, upgrade, manual
---

::: { .attention .technical }
This manual installation guide for ProGet is primarily for load-balanced and high availability installations on Windows. If you aren't using these features, you should probably use the [installer](https://inedo.com/proget/download), or refer to the [Linux/Docker installation instructions](linux-docker).
:::

On Windows, ProGet consists of 3 distinct deployable components:

{.docs}
 - **Web Node** - an ASP.NET 4.5 web application which serves as the primary interface to ProGet via a web UI and feed API implementations
 - **Service Node** - a Windows Service that performs indexing and other background tasks; hosts the web application if IIS is not used
 - **Database** - a SQL Server database which contains all of the persistent data including feeds, packages, privileges, and internal settings

### High Availability and Load-Balanced Installations

::: { .attention .best-practice }
High availability and load-balanced installations are only available in ProGet Enterprise.
:::

This manual installation guide also applies to High Availability and/or load-balanced (HA/LB) installations. In terms of "ProGet Enterprise licensing" everything is considering "high availability", but for the purposes of this documentation, HA/LB installations are distinguished *technically* from a normal installation by the following characteristics:

{.docs}
 - multiple web nodes behind a load balancer (LB)
 - multiple service nodes (HA)
 - one license for ProGet Enterprise (under the default agreement) enables up to 5 nodes to be installed, with a base recommendation being 2 service nodes and 3 web nodes

When performing installation for HA/LB, it is recommended to first provision a non-HA, "normal" instance, then add LB (i.e. additional web nodes), and finally HA (i.e an additional service node).

The database is "shared" among all the nodes in an installation in the sense that they all use the same connection string. Unless the database is configured to perform its own replication, connecting to more than one SQL Server database would require additional ProGet Enterprise licenses, and the instances would be connected via [feed replication](/docs/proget/advanced/feed-replication).

Refer to the [Load Balancing & Automatic Failover](/docs/proget/installation/load-balancing-and-automatic-failover) documentation for more information.

## Pre-requisites {#pre-requisites data-title="Pre-requisites"}

#### Pre-Installation Check List {#checklist}

ProGet supports all modern version of Windows that Microsoft supports (see [Windows Lifecycle Fact Sheet](https://support.microsoft.com/en-us/help/13853/windows-lifecycle-fact-sheet)). The recommended requirements are:

{.docs}
- **Minimum** - 2 Core CPU, 2 GB RAM, 1 GB for disk storage
- **Recommended Average/Typical** - 2 Core CPU, 4 GB RAM, 10 GB disk storage
- **Recommended Large** -  4 Core CPU, 8 GB RAM, 16 GB disk storage

Before installing, consider the following:

{.docs}
- **.NET Framework 4.5.2+** - ProGet requires .NET 4.5.2 or later
- **IIS 7.5+** - While the ProGet installer includes an Integrated Web Server to help get started, it is not supported for manual installations
- **SQL Server 2012 SP4+** - ProGet requires any supported version of Microsoft SQL Server (2012 SP4 or later at the time of writing), and will work with SQL Server Express
- **Disk Space (1GB+)** - ProGet itself requires minimal disk space, but keep in consideration for package storage or temporary package operations
- **Firewall (Inbound)** - ProGet is a web application and can be installed on the port of your choosing, and service nodes may require access if the [Service Messenger](service-messenger) is configured to use TCP as its transport mechanism
- **Firewall (Outbound)** - ProGet may require outbound access for scheduled tasks or feed connectors
- **ProGet User Account** - The ProGet application pool and Windows Service will likely require a custom service account, for which special permissions will need to be granted as documented (this will be referred to as `ProGetServiceUser@domain`, but of course can be whatever you want)
- ***Your* User Account** - your (i.e. the administrator's) user account needs to have administrator access for most of the following steps (and run the commands as an administrator), and database permission to create logins and grant database roles

#### Get the Manual Installation Package

The manual installation package is a .zip file that contains all the files and scripts necessary to create or upgrade the deployable components listed above.

To download the package, select "Manual Install" link for the desired version from the following URL: https://my.inedo.com/proget/versions. If you are unsure which version to select, use the latest version.

The manual installation package contains the following:

{.docs}
 - **Extensions directory** - a directory containing the default extensions shipped with the selected version of ProGet
 - **ProGet-Service.zip** - contains the files and binaries required to run the ProGet Windows Service
 - **ProGet-WebApp.zip** - contains the files and binaries required to run the web application
 - **ProGet-SqlScripts.zip** - contains the SQL scripts required to generate the SQL Server database, or update an existing one to the schema required for the downloaded version

#### Installation Directories {#directories}

By default, the ProGet installer allows configuration of its root installation directory, and will configure temporary directories and package storage paths for you. During a manual installation, these directories must be provisioned prior to installation.

Before configuring the necessary paths, you should decide where the following base directories will reside:

 | Directory | Description | Installer Default |
|--|--|--|
| Root installation directory | the root disk path for the web/service node binaries, and extensions | `C:\Program Files\ProGet` |
| Program data directory | the root disk path for package storage, temporary directories, and the configuration file | `C:\ProgramData\ProGet` |

Though not strictly required until later, you may also want to ensure the existence of the following directories if you would like to use the installer defaults:

{.docs}
 - `C:\ProgramData\ProGet\Extensions`
 - `C:\ProgramData\ProGet\ExtensionsTemp\Service`
 - `C:\ProgramData\ProGet\ExtensionsTemp\Web`
 - `C:\ProgramData\ProGet\Packages`
 - `C:\ProgramData\Inedo\SharedConfig`
 - `C:\Program Files\ProGet\Extensions`

If you plan to use other paths (i.e. UNC shares) for certain storage paths like "packages root path", you may proceed without creating the directories before they are [configured in the software](#db-settings).

## Database Installation (SQL Server) {#database data-title="Database (SQL Server)"}

ProGet is compatible with existing supported SQL Server installations, local or remote, and also with SQL Azure. Examples here will use `osql` as the tool to execute sample queries (since it is installed with SQL Server), but you can also use `sqlcmd` or SQL Server Management Studio directly.

#### SQL Server Recommendations:

{.docs}
 - Use the latest version of SQL Server available (but it must be at least 2012 SP4)
 - ProGet does support SQL Server Express, but we recommend using an existing SQL Server Standard Edition (or higher) if you are licensed for it
 - If you've installed a new SQL Server instance, note whether a named or default instance is used, as it will be required for the connection string information (e.g. `Server=dbserver01\SQLEXPRESS; ...` vs. `Server=dbserver01; ...`). SQL Server Express defaults to `SQLEXPRESS` as the instance name

### 1. Create a database named `ProGet` on the desired SQL Server instance

Technically speaking, the database doesn't need to be named ProGet, but for simplicity of both upgrades and configuration, we recommend `ProGet` as the database name.

```
osql -E -S <db-server> -Q "CREATE DATABASE [ProGet] COLLATE SQL_Latin1_General_CP1_CI_AS"
```

### 2. Create a database server login

Since most installations will use Integrated Authentication, a database server login for the ProGet user identity is required. If it doesn't exist, one can be created using this script:

```
osql -E -S <db-server> -Q "CREATE LOGIN [domain\ProGetServiceUser] FROM WINDOWS"
```

Note that built-in accounts may be used, such as `NT AUTHORITY\NETWORK SERVICE` or `NT AUTHORITY\LOCAL SYSTEM`, though an actual domain account is recommended.

For more information on creating database logins, see: https://docs.microsoft.com/en-us/sql/t-sql/statements/create-login-transact-sql

### 3. Run `dbupdater.exe` to update the database

Unzip `ProGet-SqlScripts.zip` into a temporary directory run the following command *in that directory*, replacing the connection string as needed:

```
.\dbupdater.exe update . <connection-string>
```

For example:

```
.\dbupdater.exe update . "Server=dbserver01\SQLEXPRESS; Database=ProGet; Integrated Security=true;"
```

This command will both update the schema of the target database, and recreate all objects (views, functions, stored procedures).

::: {.attention .best-practice}
Be sure to note the connection string that was used for this command, as it be a required value in the [configuration file](#configuration-file) that instructs ProGet which database to connect to.
::: 

### 4. Create a database user and grant access to `ProGet` database

In order for the database server login to access the database with the permissions required by ProGet, a database user must be created:

```
osql -E -S <db-server> -d ProGet -Q "CREATE USER [domain\ProGetServiceUser] FOR LOGIN [domain\ProGetServiceUser]"
```

Then the user must be granted access to the ProGet user role:

```
osql -E -S <db-server> -d ProGet -Q "ALTER ROLE [ProGetUser_Role] ADD MEMBER [domain\ProGetServiceUser]"
```

## Web Node Installation (IIS) {#web data-title="Web Node (IIS)"}

### Pre-requisites

Before installing ProGet in IIS, the following roles/features must be enabled in Windows:

#### Server Roles

{.docs}
 - Web Server (IIS) > Web Server > Security > Windows Authentication (*if planning to configure Integrated Windows Authentication in the future*)
 - Web Server (IIS) > Web Server > Application Development > .NET Extensibility 4.6 (or greater)
 - Web Server (IIS) > Web Server > Application Development > ASP.NET 4.6 (or greater)
 - Web Server (IIS) > Management Tools > IIS Management Console

#### Features

{.docs}
 - .NET Framework 4.6 Features > .NET Framework 4.6
 - .NET Framework 4.6 Features > ASP.NET 4.6

### 1. Extract website files

Extract the contents of `ProGet-WebApp.zip` from the installation package to a subdirectory of the *root installation directory* identified earlier. For reference, the installer defaults this to: `C:\Program Files\ProGet\Web`

### 2. Create an application pool in IIS

While you could use an existing application pool if desired, we recommend creating a new one. The minimum requirements for the application pool are: 

{.docs}
 - **.NET CLR Version** - 4.0
 - **Managed Pipeline Mode** - Integrated is recommended, but Classic will also work

Example PowerShell command to create an app pool:

```
&"C:\Windows\System32\inetsrv\appcmd.exe" add apppool /name:ProGet /managedRuntimeVersion:v4.0 /managedPipelineMode:Integrated /processModel.identityType:SpecificUser /processModel.userName:ProGetServiceUser@domain /processModel.password:<account-password>
```

You may adjust other application pool settings as desired.

### 3. Create a site in IIS

The minimum requirements for the IIS web site are:

{.docs}
 - **Physical Path** - the path on disk where the ProGet web application files were extracted to in step 1
 - **Application Pool** - the application pool to use, created in step 2
 - **Binding** - the combination of IP address(es) and port(s) used to access ProGet over the network

Note that any of these values can be edited once they are created, for example, to add SSL certificates, update the port that ProGet is listening on, etc.

Example PowerShell command to create a site:

```
&"C:\Windows\System32\inetsrv\appcmd.exe" add site /name:ProGet /physicalPath:"C:\Program Files\ProGet\Web" /bindings:"http/*:80:" /applicationDefaults.applicationPool:ProGet
``` 

You may adjust other IIS site settings as desired.

## Service Node (Windows Service) {#service data-title="Service Node (Windows Service)"}

### 1. Extract service files 

Extract the contents of `ProGet-Service.zip` from the installation package to a subdirectory of the *root installation directory* identified earlier. For reference, the installer defaults this to: `C:\Program Files\ProGet\Service`

### 2. Install the service

The extracted service files will contain the service executable `ProGet.Service.exe`; run this program from an elevated command prompt or PowerShell window to install the Windows Service (`INEDOPROGETSVC`):

```
PS C:\Program Files\ProGet\Service> .\ProGet.Service.exe install --user="ProGetServiceUser@domain" --password="<account-password>"
```

::: {.attention .technical}
Note: at this time, it is not permitted to change the service name from `INEDOPROGETSVC`.
:::

### 3. Start the service

The service can be started from the Windows Service Controller, or using the following PowerShell command:

```
Start-Service INEDOPROGETSVC
```
## Finalize Configuration {#configuration data-title="Finalize Configuration"}

### 1. Ensure the ProGet Configuration file exists with valid values

Once the web, service, and database are configured, the final step is to create or update the [ProGet Configuration File](/docs/proget/installation/config-files) to supply the database connection string noted earlier, and ensure `WebServer Enabled="false"`.

Once values in this file are changed, any IIS application pool or ProGet Windows Service that reference it must be restarted to see the new values.

### 2. Ensure the proper Advanced Configuration settings are set {#db-settings}

Once you log-in to ProGet for the first time, you can visit the *Administration* > *Advanced Settings* page to finalize the configuration stored in the database.

The following settings within the product that must be configured, their corresponding directories, and their installer defaults are listed below:

| Setting | Description | Installer Default | 
|---|---|---| 
| `Extensions.ExtensionsPath` | the disk path where download extensions are stored and loaded from by ProGet | `C:\ProgramData\ProGet\Extensions` |
| `Extensions.ServiceTempPath` | a temp path where extensions loaded from the extensions path are extracted to and loaded by the ProGet service | `C:\ProgramData\ProGet\ExtensionsTemp\Service` |
| `Extensions.UpdateFeedUrl` | the extensions feed URL | `https://proget.inedo.com/upack/Extensions` |
| `Extensions.WebTempPath` | a temp path where extensions loaded from the extensions path are extracted to and loaded by the ProGet web application |  `C:\ProgramData\ProGet\ExtensionsTemp\Web` |
| `Extensions.BuiltInExtensionsPath` | path containing extensions included with the installer | `C:\Program Files\ProGet\Extensions` |
| `Storage.PackagesRootPath` | base path on disk for ProGet's default package store | `C:\ProgramData\ProGet\Packages` ([more info](#packages-root-path)) |

::: {.attention .technical}
The directories referenced in the configuration values **must exist** prior to beginning use of ProGet as a package manager, otherwise several extensions-related errors will be logged in the Diagnostic Center, and/or packages may be stored on disk in an undefined manner. If the directories did not exist but ProGet was already started, both the IIS application pool and Windows Service must be restarted.
::: 

Each of these directories can assigned to a path on a local volume, mapped drive, or UNC share provided that:

{.docs}
 - the user account specified as the "log on user" for the Windows Service has read/write/delete access to them
 - the user account specified as the "application pool identity" in IIS has read/write/delete access to them

##### Packages Root Path {#packages-root-path}

The `Storage.PackagesRootPath` setting defines the base storage location on disk for packages managed by ProGet's default package store. Its value may be assigned on the All Settings page or, for load-balanced installations, may be set in the `web_AppSettings.config` file with a key name of `ProGetConfig.Storage.PackagesRootPath`. To use the .config file value, the value specified in All Settings must be saved with an empty value first, then configured in the .config file.

There are several other packages path settings (e.g. `Storage.NpmPackagesLibrary`, `Storage.MavenArtifactLibrary`) that by default are relative to the `Storage.PackagesRootPath`. If one of these settings is individually set, it will override the default behavior of being relative to the root path. To restore that behavior, save the desired field with an empty value.

## Troubleshooting {#troubleshooting data-title="Troubleshooting"}

There are a lot of moving pieces involved in a manual installation (particularly ones that the installer handles for you). Here are the most common manual installation errors as we have noted from our various support channels.

### Cannot connect to the database

Perhaps the most common problem is that the SQL Server database is inaccessible. Check the following:

{.docs}
 - the user account connecting (i.e. the application pool identity or Windows Service logon user) have a database login, an associated database user, and that user is granted the `ProGetUser_Role`
 - the user account is a domain account when accessing a database over the network (i.e. don't use `NETWORK SERVICE` and connect to a remote database)
 - the connection string exists in the ProGet configuration file and is valid (i.e. has the correct server name, database name, and includes the instance name e.g. `SQLEXPRESS`)

### Windows Service will not start

Typically when the ProGet Windows Service does not start, it is database connection-related. Any errors logged by the ProGet Service can be found in the Windows Event Log under "Windows Logs" > "Application".

Alternatively, you can run the service interactively (i.e. in a command window) for live logging. Simply ensure the `INEDOPROGETSVC` service is stopped, then run the following command as an administrator:

```
PS C:\Program Files\ProGet\Service> .\ProGet.Service.exe run --mode=serviceonly
```

### Can't control the service from within ProGet

Visit the [Service Messenger](service-messenger) documentation for enabling inter-process communication between the ProGet web application and ProGet Windows Service.

### Extensions failing to load

Extensions failing to load is commonly caused by:

{.docs}
 - missing/invalid extensions path setting (check "Administration" > "Advanced Settings")
 - the service not being restarted after the extension path is changed

Load errors for extensions are logged in the [Diagnostic Center](/docs/proget/installation/diagnostic-center).

### Additional Help

Inedo offers several support mechanisms if you have any other questions regarding a manual installation:

{.docs}
 - Support Home - https://inedo.com/support
 - Community Forums - https://forums.inedo.com
 - Slack Workspace - https://inedo.com/support/slack
 - Support Tickets (*paid users only*) - https://my.inedo.com/tickets/new