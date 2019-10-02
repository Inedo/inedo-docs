---
title: ProGet Manual Installation Guide
sequence: 600
show-headings-in-nav: true
keywords: proget, installation, upgrade, manual
---

### Table of Contents

{.docs}
 - [Upgrading an Existing Installation](#upgrade)
 - [Pre-Installation Checklist](#checklist)
 - [Get the Manual Installation Package](#get)
 - [Database Installation (SQL Server)](#database)
 - [Web Node Installation (IIS)](#web)
 - [Service Node Installation (Windows Service)](#service)
 - [Extensions](#extensions)
 - [High Availability & Load Balancing](#high-availability)
 - [Troubleshooting](#troubleshooting)

::: { .attention .technical }
This manual installation guide for ProGet is primarily for load-balanced and high availability installations on Windows. If you aren't using these features, you should probably use the [installer](https://inedo.com/proget/download), or refer to the [Linux/Docker installation instructions](linux-docker).
:::

### ProGet Software Components

On Windows, ProGet consists of 3 distinct deployable application components required to run the software:

{.docs}
 - **Web Node** - an ASP.NET 4.5 web application which serves as the primary interface to ProGet via a web UI and feed API implementations
 - **Service Node** - a Windows Service that performs indexing and other background tasks
 - **Database** - a SQL Server database which contains all of the persistent data including feeds, packages, privileges, and internal settings

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

#### Get the Manual Installation Package {#get}

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
 - For single-server installations (i.e. non-HA/LB), you may have the database server running on the same server that ProGet is installed on (i.e. the connection string would be `Server=localhost; ...` or `Server=localhost\SQLEXPRESS; ...`)
 - For manual upgrades from a previous version installed via the ProGet installer, the instance name might be `INEDO`, and older versions (v3 and earlier) would have used the instance name `PROGET`

### Pre-requisites

ProGet requires an existing SQL Server database server, so if you don't already have a database server, you'll need to create one. We recommend installing and configuring the latest version of SQL Server Express for this purpose: https://www.microsoft.com/en-us/sql-server/sql-server-editions-express

This should mostly be a guided process, but consider:

{.docs}
 - Integrated Windows Authentication is the recommended authentication mechanism (i.e. using a domain account as opposed to supplying a username/password in the connection string)
 - Make sure to add your account (or a group that will represent ProGet database administrators) when prompted to "Specify SQL Server Administrators" during "Database Engine Configuration" step so that no additional steps are required to create/manage the ProGet database
 - Be aware of the SQL Server instance name as it is part of the `Server` value in the connection string

### 1. Create a database named `ProGet` on the desired SQL Server instance

Technically speaking, the database doesn't need to be named ProGet, but for simplicity of both upgrades and configuration, we recommend `ProGet` as the database name. The database collation however must be `SQL_Latin1_General_CP1_CI_AS` to ensure case-insensitivity in default text comparisons.

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

### 3. Run `dbupdater.exe` to update the database {#db-updater}

> ##### What is `dbupdater.exe`?
> `dbupdater.exe` is a simple command-line tool developed internally by Inedo in order to update a database schema based on scripts within the supplied directory (or subdirectories). Scripts with `AH:ScriptId` headers specify a globally unique GUID to a specific script that is added to an implementation-defined metadata table (typically named similar to `DbSchemaChanges`) when the script is run so it only gets run once no matter how many times the `update` command is run. SQL scripts without this header are executed every time the command is run. All scripts are run in the order they are defined on disk, i.e. lexicographically.

#### Database Update Process

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
On new installations, be sure to note the connection string that was used for this command, as it be a required value in the [configuration file](#first-run-file) that instructs ProGet which database to connect to.
::: 

### 4. Create a database user and grant access to `ProGet` database role

In order for the database server login to access the database with the permissions required by ProGet, a database user must be created:

```
osql -E -S <db-server> -d ProGet -Q "CREATE USER [domain\ProGetServiceUser] FOR LOGIN [domain\ProGetServiceUser]"
```

Then the user must be granted access to the ProGet user role (which was added during the "run `dbupdater.exe`" step):

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

### 3. Create a site in IIS {#site}

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
## First Run {#first-run data-title="First Run"}

Once the web and service nodes are started, ProGet should be accessible via the binding specified in IIS web node [step 3](#site). The first time you access ProGet via the web application, some additional settings must be configured.

### 1. Ensure the ProGet configuration file exists with valid values

Once the web, service, and database are configured, the final step is to create or update the [ProGet Configuration File](/docs/proget/installation/config-files) to supply the database connection string noted earlier in [step 3](#db-updater) of the database configuration, and ensure `WebServer Enabled="false"`.

Once values in this file are changed, any IIS application pool or ProGet Windows Service that reference it must be restarted to see the new values.

### 2. Ensure the proper Advanced Configuration settings are set {#db-settings}

Once you log-in to ProGet for the first time, you can visit the *Administration* > *Advanced Settings* page to finalize the configuration stored in the database.

The following settings within the product that must be configured, their corresponding directories, and their installer defaults are listed below:

| Setting | Description | Installer Default | 
|---|---|---| 
| `Extensions.BuiltInExtensionsPath` | path containing extensions included with the installation package | `C:\Program Files\ProGet\Extensions` |
| `Extensions.ExtensionsPath` | the disk path where downloaded extensions are stored and loaded from by ProGet | `C:\ProgramData\ProGet\Extensions` |
| `Extensions.ServiceTempPath` | a temp path where extensions loaded from the extensions path are extracted to and loaded by the ProGet service | `C:\ProgramData\ProGet\ExtensionsTemp\Service` |
| `Extensions.WebTempPath` | a temp path where extensions loaded from the extensions path are extracted to and loaded by the ProGet web application |  `C:\ProgramData\ProGet\ExtensionsTemp\Web` |
| `Extensions.UpdateFeedUrl` | the extensions feed URL, required to browse extensions in the software | `https://proget.inedo.com/upack/Extensions` |
| `Storage.PackagesRootPath` | base path on disk for ProGet's default package store | `C:\ProgramData\ProGet\Packages` ([more info](#packages-root-path)) |

::: {.attention .technical}
The directories referenced in the configuration values **must exist** prior to beginning use of ProGet as a package manager - ProGet does not create them for you. If they do not exist, several extensions-related errors will be logged in the Diagnostic Center, and/or packages may be stored on disk in an undefined manner. If the directories did not exist but ProGet was already started, both the IIS application pool and Windows Service must be restarted.
::: 

Each of these directories can assigned to a path on a local volume or UNC share provided that:

{.docs}
 - the user account specified as the "log on user" for the Windows Service has read/write/delete access to them
 - the user account specified as the "application pool identity" in IIS has read/write/delete access to them

We do not recommend using a mapped drive (e.g. `X:\Packages`) to reference directories for the reasons described here: https://docs.microsoft.com/en-us/windows/win32/services/services-and-redirected-drives. Instead, use the full UNC path that the drive is mapped to, for example: `\\file-sv01\$x\packages`

##### Packages Root Path {#packages-root-path}

The `Storage.PackagesRootPath` setting defines the base storage location on disk for packages managed by ProGet's default package store. Its value is stored in the database and assignable on the *Advanced Settings* page.

There are several other packages path settings (e.g. `Storage.NpmPackagesLibrary`, `Storage.MavenArtifactLibrary`) that by default are relative to the `Storage.PackagesRootPath`. If one of these settings is individually set, it will override the default behavior of being relative to the root path. To restore that behavior, save the desired field with an empty value.

::: {.attention .technical}
**Note:** Older load-balanced installations may have stored the `ProGetConfig.Storage.PackagesRootPath` storage location in a web application configuration file. This source of configuration will be removed in a future version of ProGet, replaced solely by the database setting. If you see the `ProGetConfig.Storage.PackagesRootPath` setting in an existing `Web_appSettings.config`, its value should be migrated to the `Storage.PackagesRootPath` setting in *Administration* > *Advanced Settings*. The database setting already takes precedence if it exists, so if there is an existing value for this setting on the *Advanced Settings* page, it can be removed from the configuration file.
:::

### 3. Set license key and activate

All editions of ProGet require a valid license to use. Once the ProGet web application is accessible, you must visit the *Administration* > *Licensing & Activation* page to apply a license key. If ProGet is able to access the internet, the key will be automatically activated.

If you have already obtained a license key but cannot automatically activate, visit the [License Key Activation documentation](/docs/various/licensing/activation) for more information on manual activation. 

If you do not have a license key yet (or do not know what yours is), you may view keys associated with your organization or request one from [MyInedo](https://my.inedo.com).

## Extensions {#extensions}

While extensions are available in the [Inedo Den](https://inedo.com/den) and our own [public ProGet instance](https://proget.inedo.com/feeds/Extensions), the ProGet installation ships with "frozen" or "locked" versions of the extensions at the time the installer was built.

Once the `Extensions.BuiltInExtensionsPath` setting is configured to a valid directory, extract the contents of `Extensions/` directory within the manual installation package into that directory, then restart the application pool and ProGet Windows Service. Any extensions in this path are combined with the extensions managed within the ProGet software (i.e. downloaded from the Administration > Extensions page and stored in the `Extensions.ExtensionsPath` directory), and the "latest" SDK-compatible version (as per semantic versioning rules) is loaded.

#### Manual Extension Installation

For installations that are restricted from accessing the internet, or if you with to install a version other than the latest, extensions can also be installed manually:

 1. Download the desired version of the extension from the [public ProGet feed](https://proget.inedo.com/feeds/Extensions)
Copy the extension file (e.g. Windows.upack) to the extensions path.
 2. Visit the Admin > Advanced Settings page within your ProGet instance to determine where extensions are stored by examining the `Extensions.ExtensionsPath` setting.
 3. Ensure the file is named `<extension-name>.upack` in the extensions directory, and that no other files with the same name exist in that directory, even with a different extension. For example, make sure to delete an existing `<extension-name>.inedox` file if you are installing `<extension-name>.upack`. The downloaded file may contain a version part, which should be removed (e.g. InedoCore-1.5.0.upack should be just InedoCore.upack)
 4. Restart the product's Windows service: Admin > Service > Stop then Start (e.g. INEDOBMSVC, INEDOPROGETSVC)
 5. Restart the product's Web application, either the IIS application pool: Admin > Service > Restart Web App
 6. Verify that the new extension has been loaded (Admin > Extensions)

::: {.attention .technical}
Note: the [InedoCore](https://proget.inedo.com/feeds/Extensions/inedox/InedoCore) extension must be installed to use Active Directory integration.
:::

## Upgrading an Existing ProGet Installation {#upgrade data-title="Upgrading ProGet"}

Upgrading a ProGet installation from an existing installation can be performed by following this installation guide, except it should be much simpler and quicker than the initial installation since the infrastructure is already in place.

This guide may also be used to upgrade an instance that was installed by the installer, but once that has occurred, this manual process must be used going forward.

### 1. Backup the existing instance

To backup the instance, refer to the [Backup and Restoring ProGet documentation](/docs/proget/installation/backing-up).

### 2. Stop services

Before upgrading ProGet, the existing instance must be taken offline. Ensure that the application pool and Windows Service are both stopped.

Here is an example PowerShell script that performs these operations:

```
&"C:\Windows\System32\inetsrv\appcmd.exe" stop apppool ProGet
Stop-Service INEDOPROGETSVC
```

### 3. Copy installation package contents

For the most part, an upgrade is an "XCOPY deployment" of files from the [manual installation package](#get). Copy all of the contents of each of the following into the specified target directory while carefully analyzing the differences in configuration files as noted:

| Contents | Target | Installer Default | Notes |
|---|---|---|
| ProGet-Service.zip | Service installation directory | `C:\Program Files\ProGet\Service` | May overwrite existing `App_appSettings.config` if empty |
| ProGet-Web.zip | Web application home directory | `C:\Program Files\ProGet\Web` | May overwrite existing `Web_appSettings.config` if empty |
| Extensions | `Extensions.BuiltInExtensionPath` directory | `C:\Program Files\ProGet\Extensions` | Overwrite contents with install package contents |

While v5.1.0 and later versions of ProGet should not store data in these configuration files, existing installations might contain relevant data such as connection strings that should ultimately be migrated to the [ProGet Configuration File](/docs/proget/installation/config-files) and removed from the `*_appSettings.config` file.

### 4. Update database schema

Because the tool used to update the database schema is aware of previous scripts that have run, this process is the same as the initial installation process. See [Running `dbupdater.exe`](#db-updater) above to perform this step using the existing connection string specified in the [ProGet Configuration File](/docs/proget/installation/config-files). An additional benefit of being aware of previous scripts that have run is that the `dbupdater.exe` command can be applied to a database copy or backup to test the SQL upgrade before running it against a production database.

### 5. Start services

Once all files are copied and the database is updated, the services can be started to bring ProGet back online.

Here is an example PowerShell script that performs these operations:

```
&"C:\Windows\System32\inetsrv\appcmd.exe" start apppool ProGet
Start-Service INEDOPROGETSVC
```

## High Availability and Load-Balanced Installations {#high-availability}

::: { .attention .best-practice }
High availability and load-balanced installations are only available in ProGet Enterprise.
:::

This manual installation guide also applies to High Availability and/or load-balanced (HA/LB) installations. In terms of ProGet Enterprise licensing, the feature as a whole is considered "high availability", but for the purposes of this documentation, HA/LB installations are distinguished *technically* from a normal/basic installation by the following characteristics:

{.docs}
 - multiple web nodes behind a load balancer (LB)
 - multiple service nodes (HA)
 - one license for ProGet Enterprise (under the default agreement) enables up to 5 nodes to be installed, with a base recommendation being 2 service nodes and 3 web nodes

When performing installation for HA/LB, it is recommended to first provision a non-HA, "normal" instance, then add LB (i.e. additional web nodes), and finally HA (i.e an additional service node).

The database is "shared" among all the nodes in an installation in the sense that they all use the same connection string. Unless the database is configured to perform its own replication, connecting to more than one SQL Server database would require additional ProGet Enterprise licenses, and the instances would be connected via [feed replication](/docs/proget/advanced/feed-replication).

Refer to the [Load Balancing & Automatic Failover](/docs/proget/installation/load-balancing-and-automatic-failover) documentation for more information.

## Troubleshooting {#troubleshooting data-title="Troubleshooting"}

There are a lot of moving pieces involved in a manual installation (particularly ones that the installer would have handled for you). Here are the most common manual installation errors as we have noted from our various support channels.

### Cannot connect to the database

Perhaps the most common problem is that the SQL Server database is inaccessible. Check the following:

{.docs}
 - the user account connecting (i.e. the application pool identity or Windows Service logon user) have a database login, an associated database user, and that user is granted the `ProGetUser_Role`
 - the user account is a domain account when accessing a database over the network (i.e. don't use `NETWORK SERVICE` and connect to a remote database)
 - the connection string exists in the ProGet configuration file and is valid (i.e. has the correct server name, database name, and includes the instance name e.g. `SQLEXPRESS`)

### Windows Service will not start

Typically when the ProGet Windows Service does not start, it is database connection-related. Any errors logged by the ProGet Service can be found in the Windows Event Log under *Windows Logs* > *Application*.

Alternatively, you can run the service interactively (i.e. in a command window) to view live log output. Simply ensure the `INEDOPROGETSVC` service is stopped, then run the following command as an administrator:

```
PS C:\Program Files\ProGet\Service> .\ProGet.Service.exe run --mode=serviceonly
```

If there is a difference in behavior when run interactively vs. running the service, there may be a problem with the user account that the ProGet service is running as.

### Can't control the service from within ProGet

Visit the [Service Messenger](service-messenger) documentation for enabling inter-process communication between the ProGet web application and ProGet Windows Service.

### Extensions failing to load

Extensions failing to load is commonly caused by:

{.docs}
 - missing/invalid extensions path setting (check *Administration* > *Advanced Settings*)
 - the service not being restarted after the extension path is changed

Load errors for extensions are logged in the [Diagnostic Center](/docs/proget/installation/diagnostic-center).

### Additional help & support

Inedo offers several support mechanisms if you have any other questions regarding a manual installation:

{.docs}
 - Support Home - https://inedo.com/support
 - Community Forums - https://forums.inedo.com
 - Slack Workspace - https://inedo.com/support/slack
 - Support Tickets (*paid users only*) - https://my.inedo.com/tickets/new