---
title: Otter Manual Installation Guide
sequence: 600
show-headings-in-nav: true
keywords: otter, installation, upgrade, manual
---

### Table of Contents

{.docs}
 - [Upgrading an Existing Installation](#upgrade)
 - [Pre-Installation Checklist](#checklist)
 - [Get the Manual Installation Package](#get)
 - [Database Installation (SQL Server)](#database)
 - [Website Installation (IIS)](#web)
 - [Service Installation (Windows Service)](#service)
 - [First Run](#first-run )
 - [Extensions](#extensions)
 - [Troubleshooting](#troubleshooting)

### Otter Software Components

On Windows, Otter consists of 3 distinct deployable application components required to run the software:

{.docs}
 - **Website** - an ASP.NET 4.5 web application which serves as the primary interface to Otter via a web UI and feed API implementations
 - **Service** - running aside the web application, the Otter Service is a Windows Service that performs tasks requested through the Web Application
 - **Database** - a SQL Server database which stores the state and history
 - **Inedo Agent** - a lightweight Windows service that allows a server to be orchestrated using a connected instance of Otter and BuildMaster. See our [Inedo Agent Installation Guide](/docs/inedoagent/installation/installation-guide).

## Prerequisites {#prerequisites data-title="Prerequisites"}


#### Pre-Installation Check List {#checklist}

These are the same as the [Otter Installation Guide](/docs/otter/installation/installation-guide).

#### Get the Manual Installation Package {#get}

The manual installation package is a .zip file that contains all the files and scripts necessary to create or upgrade the deployable components listed above.

To download the package, select "Manual Install" link for the desired version from the following URL: https://my.inedo.com/otter/versions. If you are unsure which version to select, use the latest version.

The manual installation package contains the following:

{.docs}
 - **Extensions directory** - contains the default extensions shipped with the selected version of Otter
 - **Service directory** - contains the files and binaries required to run the Otter Windows Service
 - **SqlScripts directory** - contains the SQL scripts required to generate the SQL Server database, or update an existing one to the schema required for the downloaded version
 - **Web directory** - contains the files and binaries required to run the Website application

 Note that, in earlier versions of Otter, the directory names may be different or may be .zip files instead of directories.
 
#### Installation Directories {#directories}

By default, the Otter installer allows configuration of its root installation directory, and will configure temporary directories for you. During a manual installation, these directories must be provisioned prior to installation.

Before configuring the necessary paths, you should decide where the following base directories will reside:

| Directory | Description | Installer Default |
|--|--|--|
| Root installation directory | the root disk path for the Website and Service binaries, and extensions | `C:\Program Files\Otter` |
| Program data directory | the root disk path for package storage, temporary directories, and the configuration file | `C:\ProgramData\Otter` |

Though not strictly required until later, you may also want to ensure the existence of the following directories if you would like to use the installer defaults:

{.docs}
 - `C:\ProgramData\Inedo\SharedConfig`
 - `C:\Program Files\Otter\Service`
 - `C:\Program Files\Otter\Web`
 - `C:\Program Files\Otter\Extensions`
 - `C:\ProgramData\Otter\Extensions`
 - `C:\ProgramData\Otter\ExtensionsTemp\Service`
 - `C:\ProgramData\Otter\ExtensionsTemp\Web`

## Database Installation (SQL Server) {#database data-title="Database (SQL Server)"}

Otter is compatible with existing supported SQL Server installations, local or remote, and also with SQL Azure. Examples here will use `osql` as the tool to execute sample queries (since it is installed with SQL Server), but you can also use `sqlcmd` or SQL Server Management Studio directly.

#### SQL Server Recommendations:

{.docs}
 - Use the latest version of SQL Server available (but it must be at least 2012 SP4)
 - Otter does support SQL Server Express, but we recommend using an existing SQL Server Standard Edition (or higher) if you are licensed for it
 - If you've installed a new SQL Server instance, note whether a named or default instance is used, as it will be required for the connection string information (e.g. `Server=dbserver01\SQLEXPRESS; ...` vs. `Server=dbserver01; ...`). SQL Server Express defaults to `SQLEXPRESS` as the instance name
 - For single-server installations (i.e. non-HA/LB), you may have the database server running on the same server that Otter is installed on (i.e. the connection string would be `Server=localhost; ...` or `Server=localhost\SQLEXPRESS; ...`)

### Prerequisites

Otter requires an existing SQL Server database server, so if you don't already have a database server, you'll need to create one. We recommend installing and configuring the latest version of SQL Server Express for this purpose: https://www.microsoft.com/en-us/sql-server/sql-server-editions-express

This should mostly be a guided process, but consider:

{.docs}
 - Integrated Windows Authentication is the recommended authentication mechanism (i.e. using a domain account as opposed to supplying a username/password in the connection string)
 - Make sure to add your account (or a group that will represent Otter database administrators) when prompted to "Specify SQL Server Administrators" during "Database Engine Configuration" step so that no additional steps are required to create/manage the Otter database
 - Be aware of the SQL Server instance name as it is part of the `Server` value in the connection string

### 1. Create a database named `Otter` on the desired SQL Server instance

Technically speaking, the database doesn't need to be named Otter, but for simplicity of both upgrades and configuration, we recommend `Otter` as the database name. The database collation however must be `SQL_Latin1_General_CP1_CI_AS` to ensure case-insensitivity in default text comparisons.

```
osql -E -S <db-server> -Q "CREATE DATABASE [Otter] COLLATE SQL_Latin1_General_CP1_CI_AS"
```

### 2. Create a database server login

Since most installations will use Integrated Authentication, a database server login for the Otter user identity is required. If it doesn't exist, one can be created using this script:

```
osql -E -S <db-server> -Q "CREATE LOGIN [domain\OtterServiceUser] FROM WINDOWS"
```

Note that built-in accounts may be used, such as `NT AUTHORITY\NETWORK SERVICE` or `NT AUTHORITY\LOCAL SYSTEM`, though an actual domain account is recommended.

For more information on creating database logins, see: https://docs.microsoft.com/en-us/sql/t-sql/statements/create-login-transact-sql

### 3. Run `inedosql` to update the database {#inedosql}

> ##### What is `inedosql`?
> [inedosql](https://github.com/Inedo/inedosql) is a open-source command-line tool developed by Inedo in order to execute SQL (.sql) scripts against a SQL Server database. Scripts with `AH:ScriptId` headers specify a globally unique GUID to a specific script that is added to an implementation-defined metadata table (typically named similar to `__InedoDb_DbSchemaChanges`) when the script is run so it only gets run once no matter how many times the `update` command is run. SQL scripts without this header are executed every time the command is run. All scripts are run in the order they are defined on disk, i.e. lexicographically.  See the [inedosql GitHub page](https://github.com/Inedo/inedosql) for more information.

#### Database Update Process

1. Copy the `SqlScripts` directory into a temporary directory.

2. Run command line and change current directory to the `SqlScripts` folder which contains `inedosql.exe` file.

3. Run the following command in that directory, replacing the connection string as needed:

```
.\inedosql.exe update . --connection-string=<connection-string>
```

For example:

```
cd C:\Otter3.0.2_Manual\
.\inedosql.exe update . --connection-string="Server=dbserver01\SQLEXPRESS; Database=Otter; Integrated Security=true;"
```

This command will both update the schema of the target database, and recreate all objects (views, functions, stored procedures).

::: {.attention .best-practice}
On new installations, be sure to note the connection string that was used for this command, as it be a required value in the [configuration file](#first-run-file) that instructs Otter which database to connect to.
::: 

##### Otter 2.2 and earlier

In Otter 2.2 and earlier, `dbupdater.exe` is used to update the database.  Use these instructions for updating the database:

1. Copy the `SqlScripts` directory into a temporary directory.

2. Run command line and change current directory to the `SqlScripts` folder which contains dbupdater.exe file.

3. Unpack required scripts from Scripts.gz with the following command: 

```
.\dbupdater.exe unpack .\Scripts.gz .
```

4. After the required folders/files are extracted, stay in the same directory and run the following command in that directory, replacing the connection string as needed:

```
.\dbupdater.exe update . <connection-string>
```

For example:

```
cd C:\OtterSetup2.2.23_Manual\
.\dbupdater.exe unpack .\Scripts.gz .
.\dbupdater.exe update . "Server=dbserver01\SQLEXPRESS; Database=Otter; Integrated Security=true;"
```

### 4. Create a database user and grant access to `Otter` database role

In order for the database server login to access the database with the permissions required by Otter, a database user must be created:

```
osql -E -S <db-server> -d Otter -Q "CREATE USER [domain\OtterServiceUser] FOR LOGIN [domain\OtterServiceUser]"
```

Then the user must be granted access to the Otter user role (which was added during the "run `dbupdater.exe`" step):

```
osql -E -S <db-server> -d Otter -Q "ALTER ROLE [OtterUser_Role] ADD MEMBER [domain\OtterServiceUser]"
```

## Website Installation (IIS) {#web data-title="Website (IIS)"}

### Prerequisites

Before installing Otter in IIS, the following roles/features must be enabled in Windows:

#### Server Roles

{.docs}
 - Web Server (IIS) > Web Server > Security > Windows Authentication (*if planning to configure Integrated Windows Authentication in the future*)
 - Web Server (IIS) > Web Server > Application Development > .NET Extensibility 4.6 (or greater)
 - Web Server (IIS) > Web Server > Application Development > ASP.NET 4.6 (or greater)
 - Web Server (IIS) > Web Server > Common HTTP Features > Static Content
 - Web Server (IIS) > Management Tools > IIS Management Console

#### Features

{.docs}
 - .NET Framework 4.6 Features > .NET Framework 4.6
 - .NET Framework 4.6 Features > ASP.NET 4.6

### 1. Copy Website files

Copy the contents of `Web` directory from the installation package to a subdirectory of the *root installation directory* identified earlier. For reference, the installer defaults this to: `C:\Program Files\Otter\Web`

### 2. Create an application pool in IIS

While you could use an existing application pool if desired, we recommend creating a new one. The minimum requirements for the application pool are: 

{.docs}
 - **.NET CLR Version** - 4.0
 - **Managed Pipeline Mode** - Integrated is recommended, but Classic will also work

Example PowerShell command to create an app pool:

```
&"C:\Windows\System32\inetsrv\appcmd.exe" add apppool /name:Otter /managedRuntimeVersion:v4.0 /managedPipelineMode:Integrated /processModel.identityType:SpecificUser /processModel.userName:OtterServiceUser@domain /processModel.password:<account-password>
```

You may adjust other application pool settings as desired.

### 3. Create a site in IIS {#site}

The minimum requirements for the IIS web site are:

{.docs}
 - **Physical Path** - the path on disk where the Otter Website files were copied to in step 1
 - **Application Pool** - the application pool to use, created in step 2
 - **Binding** - the combination of IP address(es) and port(s) used to access Otter over the network

Note that any of these values can be edited once they are created, for example, to add SSL certificates, update the port that Otter is listening on, etc.

Example PowerShell command to create a site:

```
&"C:\Windows\System32\inetsrv\appcmd.exe" add site /name:Otter /physicalPath:"C:\Program Files\Otter\Web" /bindings:"http/*:80:" /applicationDefaults.applicationPool:Otter
``` 

You may adjust other IIS site settings as desired.

## Service (Windows Service) {#service data-title="Service (Windows Service)"}

### 1. Copy service files 

Copy the contents of `Service` directory from the installation package to a subdirectory of the *root installation directory* identified earlier. For reference, the installer defaults this to: `C:\Program Files\Otter\Service`

### 2. Install the service

The copied service files will contain the service executable `Otter.Service.exe`; run this program from an elevated command prompt or PowerShell window to install the Windows Service (`INEDOOTTERSVC`):

```
PS C:\Program Files\Otter\Service> .\Otter.Service.exe install --user="OtterServiceUser@domain" --password="<account-password>"
```

::: {.attention .technical}
Note: at this time, it is not permitted to change the service name from `INEDOOTTERSVC`.
:::

### 3. Start the service

The service can be started from the Windows Service Controller, or using the following PowerShell command:

```
Start-Service INEDOOTTERSVC
```
## First Run {#first-run data-title="First Run"}

Once the Website and Service are started, Otter should be accessible via the binding specified in IIS site [step 3](#site). The first time you access Otter via the Website, some additional settings must be configured.

### 1. Ensure the Otter configuration file exists with valid values

Once the Website, Service, and Database are configured, the final step is to create or update the [Otter Configuration File](/docs/otter/installation/config-files) to supply the database connection string noted earlier in [step 3](#db-updater) of the database configuration, specify the `EncryptionKey`, and ensure `WebServer Enabled="false"`.

The `EncryptionKey` is a 32-character hex string is used for AES encryption. You can generate this however you'd like, including by copying the pseudorandom string below, which is regenerated every time you refresh this page:

<pre id="random-aes-key"></pre>
<script>
var aesKey;
if (window.crypto) {
    if (crypto.getRandomValues){
        aesKey = new Uint8Array(16);
        crypto.getRandomValues(aesKey);
        aesKey = aesKey.reduce(function(str, byte) {
            return str + ('0' + byte.toString(16)).substr(-2);
        }, '').toUpperCase();
    } else {
        aesKey = Array(32).map(function() {
            return Math.floor(Math.random() * 16).toString(16);
        }).join('').toUpperCase();
    }
} else {
    aesKey = Array(32).map(function() {
        return Math.floor(Math.random() * 16).toString(16);
    }).join('').toUpperCase();
}
document.getElementById('random-aes-key').textContent = aesKey;
</script>

Once values in this file are changed, any IIS application pool or Otter Windows Service that reference it must be restarted to see the new values.

### 2. Ensure the proper Advanced Configuration settings are set {#db-settings}

Once you log-in to Otter for the first time, you can visit the *Administration* > *Advanced Settings* page to finalize the configuration stored in the database.

The following settings within the product that must be configured, their corresponding directories, and their installer defaults are listed below:

| Setting | Description | Installer Default | 
|---|---|---| 
| `Extensions.BuiltInExtensionsPath` | path containing extensions included with the installation package | `C:\Program Files\Otter\Extensions` |
| `Extensions.ExtensionsPath` | the disk path where downloaded extensions are stored and loaded from by Otter | `C:\ProgramData\Otter\Extensions` |
| `Extensions.UseNewExtensionLoader` | when set, uses `CommonCachePath` instead of `ServiceTempPath` and `WebTempPath` for Extensions | true (checked) |
| `Extensions.CommonCachePath` | a temp path where extensions loaded from `ExtensionsPath` are extracted to and loaded by the Otter service | `C:\ProgramData\Otter\ExtensionsCache` |
| `Extensions.UpdateFeedUrl` | the extensions feed URL, required to browse extensions in the software | `https://proget.inedo.com/upack/Extensions` |

::: {.attention .technical}
The directories referenced in the configuration values **must exist** prior to beginning to use Otter - Otter does not create them for you. If they do not exist, several extensions-related errors will be logged in the Diagnostic Center, and/or packages may be stored on disk in an undefined manner. If the directories did not exist but Otter was already started, both the IIS application pool and Windows Service must be restarted.
::: 

Each of these directories can assigned to a path on a local volume or UNC share provided that:

{.docs}
 - the user account specified as the "log on user" for the Windows Service has read/write/delete access to them
 - the user account specified as the "application pool identity" in IIS has read/write/delete access to them

We do not recommend using a mapped drive (e.g. `X:\Packages`) to reference directories for the reasons described here: https://docs.microsoft.com/en-us/windows/win32/services/services-and-redirected-drives. Instead, use the full UNC path that the drive is mapped to, for example: `\\file-sv01\$x\packages`

::: {.attention .best-practice}
If you do attempt to use a mapped drive (or you are installing on Azure using Azure Files as the backing store), you may need to configure the Otter application pool to set the `Load User Profile` property to `True`.
:::

### 3. Set license key and activate

All editions of Otter require a valid license to use. Once the Otter Website is accessible, you must visit the *Administration* > *Licensing & Activation* page to apply a license key. If Otter is able to access the internet, the key will be automatically activated.

If you have already obtained a license key but cannot automatically activate, visit the [License Key Activation documentation](/docs/various/licensing/activation) for more information on manual activation. 

If you do not have a license key yet (or do not know what yours is), you may view keys associated with your organization or request one from [MyInedo](https://my.inedo.com).

## Extensions {#extensions}

While extensions are available in our own [public ProGet instance](https://proget.inedo.com/feeds/Extensions), the Otter installation ships with "frozen" or "locked" versions of the extensions at the time the installer was built.

Once the `Extensions.BuiltInExtensionsPath` setting is configured to a valid directory, copy the contents of `Extensions/` directory from the installation package into that directory, then restart the application pool and Otter Windows Service. Any extensions in this path are combined with the extensions managed within the Otter software (i.e. downloaded from the Administration > Extensions page and stored in the `Extensions.ExtensionsPath` directory), and the "latest" SDK-compatible version (as per semantic versioning rules) is loaded.

#### Manual Extension Installation

For installations that are restricted from accessing the internet, or if you with to install a version other than the latest, extensions can also be installed manually:

 1. Download the desired version of the extension from the [public ProGet feed](https://proget.inedo.com/feeds/Extensions)
 2. Copy the extension file (e.g. Windows.upack) to the extensions path.
 3. Visit the Admin > Advanced Settings page within your Otter instance to determine where extensions are stored by examining the `Extensions.ExtensionsPath` setting.
 4. Ensure the file is named `<extension-name>.upack` in the extensions directory, and that no other files with the same name exist in that directory, even with a different extension. For example, make sure to delete an existing `<extension-name>.inedox` file if you are installing `<extension-name>.upack`. The downloaded file may contain a version part, which should be removed (e.g. InedoCore-1.10.1.upack should be just InedoCore.upack)
 5. Restart the product's Windows service: Admin > Service > Stop then Start (e.g. INEDOOTTERSVC)
 6. Restart the product's Website application, either the IIS application pool: Admin > Service > Restart Web App
 7. Verify that the new extension has been loaded (Admin > Extensions)

::: {.attention .technical}
Note: the [InedoCore](https://proget.inedo.com/feeds/Extensions/inedox/InedoCore) extension must be installed to use Active Directory integration.
:::

## Upgrading an Existing Otter Installation {#upgrade data-title="Upgrading Otter"}

Upgrading a Otter installation from an existing installation can be performed by following this installation guide, except it should be much simpler and quicker than the initial installation since the infrastructure is already in place.

This guide may also be used to upgrade an instance that was installed by the installer, but once that has occurred, this manual process must be used going forward.

### 1. Backup the existing instance

To backup the instance, refer to the [Backup Up Otter documentation](/docs/otter/installation/backing-up).

### 2. Stop services

Before upgrading Otter, the existing instance must be taken offline. Ensure that the application pool and Windows Service are both stopped.

Here is an example PowerShell script that performs these operations:

```
&"C:\Windows\System32\inetsrv\appcmd.exe" stop apppool Otter
Stop-Service INEDOOTTERSVC
```

### 3. Copy installation package contents

For the most part, an upgrade is an "XCOPY deployment" of files from the [manual installation package](#get). Copy all of the contents of each of the following into the specified target directory while carefully analyzing the differences in configuration files as noted:

| Contents | Target | Installer Default | Notes |
|---|---|---|
| Service | Service installation directory | `C:\Program Files\Otter\Service` | May overwrite existing `App_appSettings.config` if empty |
| Web | Website application home directory | `C:\Program Files\Otter\Web` | May overwrite existing `Web_appSettings.config` if empty |
| Extensions | `Extensions.BuiltInExtensionPath` directory | `C:\Program Files\Otter\Extensions` | Overwrite contents with install package contents |

### 4. Update database schema

Because the tool used to update the database schema is aware of previous scripts that have run, this process is the same as the initial installation process. See [Running `inedosql.exe`](#inedosql) above to perform this step using the existing connection string specified in the [Otter Configuration File](/docs/otter/installation/config-files). An additional benefit of being aware of previous scripts that have run is that the `inedosql.exe` command can be applied to a database copy or backup to test the SQL upgrade before running it against a production database.

### 5. Start services

Once all files are copied and the database is updated, the services can be started to bring Otter back online.

Here is an example PowerShell script that performs these operations:

```
&"C:\Windows\System32\inetsrv\appcmd.exe" start apppool Otter
Start-Service INEDOOTTERSVC
```


## Troubleshooting {#troubleshooting data-title="Troubleshooting"}

There are a lot of moving pieces involved in a manual installation (particularly ones that the installer would have handled for you). Here are the most common manual installation errors as we have noted from our various support channels.

### Cannot connect to the database

Perhaps the most common problem is that the SQL Server database is inaccessible. Check the following:

{.docs}
 - the user account connecting (i.e. the application pool identity or Windows Service logon user) have a database login, an associated database user, and that user is granted the `OtterUser_Role`
 - the user account is a domain account when accessing a database over the network (i.e. don't use `NETWORK SERVICE` and connect to a remote database)
 - the connection string exists in the Otter configuration file and is valid (i.e. has the correct server name, database name, and includes the instance name e.g. `SQLEXPRESS`)

### Windows Service will not start

Typically when the ProGet Windows Service does not start, it is database connection-related. Any errors logged by the Otter Service can be found in the Windows Event Log under *Windows Logs* > *Application*.

Alternatively, you can run the service interactively (i.e. in a command window) to view live log output. Simply ensure the `INEDOOTTERSVC` service is stopped, then run the following command as an administrator:

```
PS C:\Program Files\Otter\Service> .\Otter.Service.exe run --mode=serviceonly
```

If there is a difference in behavior when run interactively vs. running the service, there may be a problem with the user account that the Otter service is running as.

### Can't control the service from within Otter

Visit the [Service Messenger](service-messenger) documentation for enabling inter-process communication between the Otter Website and Otter Service.

### Extensions failing to load

Extensions failing to load is commonly caused by:

{.docs}
 - missing/invalid extensions path setting (check *Administration* > *Advanced Settings*)
 - the service not being restarted after the extension path is changed

Load errors for extensions are logged in the [Diagnostic Center](/docs/otter/installation/diagnostic-center).

### Additional help & support

Inedo offers several support mechanisms if you have any other questions regarding a manual installation:

{.docs}
 - Support Home - https://inedo.com/support
 - Community Forums - https://forums.inedo.com
 - Slack Workspace - https://inedo.com/support/slack
 - Support Tickets (*paid users only*) - https://my.inedo.com/tickets/new
