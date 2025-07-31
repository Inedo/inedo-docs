---
title: "Manual Installation"
order: 9
---

:::(Error) (💀 Manual Installations are Not Supported 💀)
This documentation serves only as a reference to how our products our configured on Windows and may help you troubleshoot problems that you couldn't fix by reinstalling with InedoHub.

Please do not attempt to install our products manually.
:::

This documentation is based the installation script run by the Inedo Hub when installing ProGet 2025 and BuildMaster 2025. For earlier versions (i.e. ProGet 2024, BuildMaster 2024, and Otter 2024), see the [old version of this article (github.com)](https://github.com/Inedo/inedo-docs/blob/c93d4de19b15f9dc8bad878e81d014ff8a71be32/Content/installation/manual-installation/%23.md).


## 1. Extract Installation Package

The installation package can be downloaded from our [Products Release Feed](https://proget.inedo.com/feeds/Products). 

It will contain two sub-packages that you need to extract the installation directory (e.g. `%ProgramFiles%`):

| Path within Package| Destination Directory
| -- | --
| `/package/Service` | `%ProgramFiles%\Service`
| `/package/Extensions` | `%ProgramFiles%\Extensions`

Any other files/directories you find in the installation package are not needed and should not be extracted to the installation directory.


## 2. Create Database

If you will be using the integrated PostgreSQL database with ProGet 2025, this step is not required. Instead, if there is no connection string specified, ProGet will automatically create a database and add the connection string to the configuration file.

### SQL Server

If you don't already have SQL Server installed, you can download and install the latest version of SQL Server Express: [https://www.microsoft.com/en-us/sql-server/sql-server-editions-express](https://www.microsoft.com/en-us/sql-server/sql-server-editions-express). 

When installing SQL Server, we recommend naming your new instance `INEDO`.

#### Creating a SQL Server Database

We recommend naming your database `ProGet`, `BuildMaster`, or `Otter`, depending on the Inedo Product you're installing. 

:::(Warning)
The database collation must be `SQL_Latin1_General_CP1_CI_AS` to ensure case-insensitivity in default text comparisons.
:::

You will also need to create a database user based on a server login (see [CREATE LOGIN](https://docs.microsoft.com/en-us/sql/t-sql/statements/create-login-transact-sql)), and grant that user the appropriate permissions.

```
CREATE USER [NT AUTHORITY\NETWORK SERVICE] FOR LOGIN [NT AUTHORITY\NETWORK SERVICE]
ALTER USER [NT AUTHORITY\NETWORK SERVICE] WITH DEFAULT_SCHEMA=[dbo]
ALTER ROLE [db_owner] ADD MEMBER [NT AUTHORITY\NETWORK SERVICE]
```

Finally, make sure to set the [database recovery model](https://learn.microsoft.com/en-us/sql/relational-databases/backup-restore/recovery-models-sql-server) to Simple; this will significantly improve performance. The default (Full) model does not offer any benefit to Inedo product usage.

## 3. Create Configuration File

You will need to create an [Installation Configuration File](https://docs.inedo.com/docs/installation/configuration-files) in the `%ProgramData%\Inedo\SharedConfig\`.

For example, ProGet's configuration file would be `C:\ProgramData\Inedo\SharedConfig\ProGet.config` and look something like this:

```
<?xml version="1.0" encoding="utf-8"?>
<InedoAppConfig>
  <ConnectionString>server=localhost;database=proget;integrated security=sspi;</ConnectionString>
  <EncryptionKey>1fa97faa42744155a879c056a70c11a7</EncryptionKey>
  <WebServer Urls="http://*:8624" />
</InedoAppConfig>
```

Note that you can simply use`New-Guid` to create an Encryption Key.

## 4. Register Windows Service

The `%ProgramFiles\«inedo-product»\Service` directory will contain the main service executable (`proget.exe` or `BuildMaster.Service.exe`). You can run this program from an elevated command prompt or PowerShell window to install the Windows Service using the `install` command.

For example, to install ProGet, you would run:
```
%ProgramFiles\ProGet\Service\proget.exe install
```

This will create a service named `INEDOPROGETSVC` that runs under `NT AUTHORITY\NETWORK SERVICE`.

The service can then be run via the Windows Service Controller or with the `Start-Service` PowerShell CmdLet.


## 5. First Run Configuration
Once the service is started, your Inedo product should be accessible through the binding you specified in the configuration file.  The first time you access your Inedo product through the site, some additional settings will need to be configured.

Once you log-in to your Inedo product for the first time, you can visit the *Administration* > *Advanced Settings* page to finalize the configuration stored in the database.

### Create & Configure Data Directories
The following settings within the product that must be configured, their corresponding directories, and their installer defaults are listed below:

| Setting | Description | Installer Default | 
|---|---|---| 
| `Extensions.BuiltInExtensionsPath` | path containing extensions included with the installation package | `C:\Program Files\«inedo-product»\Extensions` |
| `Extensions.ExtensionsPath` | the disk path where downloaded extensions are stored and loaded from by ProGet | `C:\ProgramData\«inedo-product»\Extensions` |
| `Extensions.CommonCachePath` | temp path where extensions loaded from `ExtensionsPath` are extracted to and loaded by the «inedo-product» service | `C:\ProgramData\«inedo-product»\ExtensionsCache` |
| `Storage.PackagesRootPath` |**ProGet Only** base path on disk for ProGet's default package store | `C:\ProgramData\ProGet\Packages` ([more info](#packages-root-path)) |
| `Artifacts.BasePath` |**BuildMaster Only** base path on disk for BuildMaster's default artifact store | `C:\ProgramData\BuildMaster\Artifacts` |


::: (ERROR)
The directories referenced in the configuration values **must** exist before you start using your Inedo software, because the Inedo product does not create them for you.

If these directories do not exist, the Diagnostic Center will log various errors related to extensions and/or the packages may be stored on disk in an undefined manner. If the directories did not exist, but your Inedo product has already been started, the Windows service must be restarted.
::: 

Each of these directories can be assigned to a path on a local volume or UNC share provided that the user account specified as the "log on user" for the Windows Service has read/write/delete access to them.

We do not recommend using a mapped drive (e.g. `X:\Packages`) to reference directories for the reasons [described here](https://docs.microsoft.com/en-us/windows/win32/services/services-and-redirected-drives). Instead, use the full UNC path that the drive is mapped to, for example: `\\file-sv01\$x\packages`

::: (INFO)
If you do attempt to use a mapped drive (or you are installing on Azure using Azure Files as the backing store), you may need to configure the application pool to set the `Load User Profile` property to `True`.
:::

#### ProGet only: Packages Root Path 
The `Storage.PackagesRootPath` setting defines the base storage location on disk for packages managed by ProGet's default package store. Its value is stored in the database and assignable on the *Advanced Settings* page.

There are several other packages path settings (e.g., `Storage.NpmPackagesLibrary`, `Storage.MavenArtifactLibrary`) that are relative to the `Storage.PackagesRootPath` by default. If one of these settings is individually set, it will override the default behavior of being relative to the root path. To restore that behavior, save the desired field with an empty value.

## Troubleshooting 

We don't maintain any troubleshooting documentation anymore; you can find the old documentation here:

* [manual-installation-troubleshooting-guide.md (github.com)](https://github.com/Inedo/inedo-docs/blob/c82fd2881e2f1d0c36e77bc8b8b48e2a2c7b75a9/Content/installation/manual-installation/manual-installation-troubleshooting-guide.md)
* [installation-manually-upgrade.md (github.com)](https://github.com/Inedo/inedo-docs/blob/c82fd2881e2f1d0c36e77bc8b8b48e2a2c7b75a9/Content/installation/manual-installation/installation-manually-upgrade.md)

It's likely outdated.