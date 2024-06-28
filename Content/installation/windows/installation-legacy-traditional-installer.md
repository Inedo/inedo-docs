---
title: "Legacy (Traditional) Installer"
order: 9
---

The Legacy (Traditional) Installer is a Windows-based desktop application that installs or upgrades a single version of an Inedo product on a server. It has been discontinued in favor of [the Inedo Hub](/docs/installation/windows/desktophub-overview).

::: (ERROR)
The Legacy (Traditional) Installer has been discontinued as of ProGet 6.0, BuildMaster 7.0, and Otter 3.0. If you require offline installation, see the [Offline Installation Options](/docs/installation/windows/desktophub-offline) available with the Inedo Hub.
:::

The legacy (traditional) installer Inedo products are available on the "All Version page" of each product ([BuildMaster](https://my.inedo.com/buildmaster/versions), [ProGet](https://my.inedo.com/proget/versions), and [Otter](https://my.inedo.com/otter/versions)), if you click on the “Traditional Installer” for the version you want.

If you have the Inedo Hub installed you will not be able to use the traditional installer without first uninstalling the Inedo Hub and any products installed using it.

Once you run the installer, you’ll be asked to review and accept our license agreement before you can use our software:
 * [ProGet License Agreement](https://inedo.com/proget/license-agreement)
 * [BuildMaster License Agreement](https://inedo.com/buildmaster/license-agreement)
 * [Otter License Agreement](https://inedo.com/otter/license-agreement)

Complete the rest of the installation by selecting each setting appropriate for your system.

## Silent Installation for Traditional (Legacy) Installer

::: (INFO) 
**These options only apply to the Traditional (Legacy) Installer.**

See the [silent installation guide] for the recommend silent process.
:::


| Parameter | Description |
| --- | --- |
| /S | 	Required for a silent install. If this is not specified, the UI will be displayed. |
| /Edition=«value»	 | 	Required for a silent install. The desired software edition: May be Express, Trial, or LicenseKey. |
| /EmailAddress=«value» | The email address used for requesting a license key. Required if Edition is Express or Trial. |
| /FullName=«value»	 | The user's full name used for requesting a license key. Required if Edition is Express or Trial. |
| /LicenseKey=«value»	 | License key to use. Only used if Edition is LicenseKey. |
| /TargetPath=«value» | Root path where the software will be installed. The default is `ProgramFiles`\«Product name».	|
| /PackagesPath=«value» | Root path where your Inedo software will store its feed packages. The default is `CommonApplicationData`\«Product name»\Packages. |
| /ASPNETTempPath=«value» | Path where ASP.NET will store temporary files for the Inedo product web application. The default is `CommonApplicationData`\«Product»\Temporary ASP.NET Files. |
| /WebAppPath=«value» | Path where the «Product» web application is installed. The default is `TargetPath`\WebApp. |
| /ServicePath=«value» | Path where the «Product» service is installed. The default is `TargetPath`\Service. |
| /Extensions=«value» | Path where the extension library is located. The default is `TargetPath`\Extensions. |
| /PackagesPath=«value» | Path where the packages root path is located. The default is `TargetPath`\Packages. |
| /ConnectionString=«value» | SQL Server connection string the software will use to connect to the database. The default is Data Source=localhost; Initial Catalog=«Product»; Integrated Security=True;. |
| /Port=«value» | Port number the «Product» web application will listen to. See "Default Ports for more details" |
| /UseIntegratedWebServer=«value» | When true, the integrated web server is used to host your inedo software; when false, IIS is used. The default is true. |
| /WebServerPrefixes=«value» | Prefixes used by the integrated web server. The default is http://*:`Port`/. |
| /InstallSqlExpress | Downloads and installs a SQL Express instance named «InedoProduct». The default is false. |
| /UserAccount=«value» | User account of the Inedo Product service and web application. The default is LocalSystem. |
| /Password=«value» | Password of the user account of the Inedo Product service and web application. Only used when UserAccount is not one of the standard service accounts. |
| /WebAppUserAccount=«value» | User account of the Inedo Product web application. The default is `UserAccount`. |
| /WebAppUserAccountPassword=«value» | Password of the user account of the Inedo Product web application. The default is `Password`. |
| /ServiceUserAccount=«value» | User account of the Inedo Product service. The default is `UserAccount`. |
| /ServiceUserAccountPassword=«value» | Password of the user account of the Inedo Product service. The default is `Password`. |
| /ConfigureIIS | 	Creates an IIS website and application pool to host the Inedo Product web application. The default is false. |
| /LogFile=«value» | If specified, writes out a detailed log file of the installation process to the specified path value. |

### Default Ports

| Inedo Product | Default Port |
| --- | --- |
| BuildMaster | 8622 |
| ProGet | 8624 |
| Otter | 8626 |

### Example (Installing ProGet)
Install ProGet using the default settings with license key XXXXX:
```
ProGetSetup.exe /S /LicenseKeys=XXXXX
```
<br>
:::(Warning) ()
We are no longer actively supporting the traditional installer, as such you may encounter errors during the installation process such as the ability to complete the installation using:
```
ProGetSetup.exe /S /Edition=Express /EmailAddress=«email» /FullName=«fullname
```
:::

## Silent Upgrade Options

| Parameter | Description |
| --- | --- |
| /S | 	Required for a silent upgrade. If this is not specified, the UI will be displayed. |
| /Upgrade | Required to perform an upgrade. If this is not specified, the upgrade is not performed. |
| /BackupDatabase | Backs up the Inedo Product database before the upgrade begins. The default is true. |
| /DatabaseBackupPath=«value» | The path to save the backup of the Inedo Product database. The default is `SysDrive`:\«Product»Backups. |
| /ConnectionString=«value» | SQL connection string used for upgrading your Inedo Product's database schema. The default is whatever connection string the software's service is currently using. |
| /LogFile=«value» | If specified, writes out a detailed log file of the installation process to the specified path value. |

### Example (Upgrade ProGet)
Upgrade ProGet using the default settings:

```
ProGetSetup.exe /S /Upgrade
```