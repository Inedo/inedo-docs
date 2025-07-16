---
title: "Inedo Hub CLI Reference"
nav-title: "CLI Reference"
order: 4
---


A separate console program (hub.exe) is included with the standard GUI desktop hub. This tool supports the following commands:

## Install 

```
Usage: hub.exe install <product>[:version] [--arg=value]...
```

Installs/upgrades the specified product (ProGet, BuildMaster, or Otter). If `[:version]` is not specified, the latest stable version is used. The `[--arg=value]` syntax is used to pass any of the following options:


| Name                    | Description        |
| ----------------------- |--------------------|
| TargetDirectory         | The root install directory for the Inedo product.<br/><br/>*Default value:*<br/> $PathCombine($SpecialWindowsPath(ProgramFiles), `ProductName`) |
| ConnectionString        | Connection string used to create and initialize the database schema.<br/><br/>*no default value* |
| UseIntegratedWebServer  | When true, installs a service to host the integrated web server.<br/><br/>*default value:* true |
| UseIIS                  | When true, creates an app pool and site in IIS.<br/><br/>*default value:* false |
| DbName                  | Name of the SQL database to use or create. This overrides any Initial Catalog specified in the connection string.<br/><br/>*default value:* `ProductName` |
| WebServerPrefixes       | Specifies the URL which is used by the integrated web server. Ignored if UseIntegratedWebServer is not true.<br/><br/>*default value:* http://*:8624/ |
| ExtensionsPath          | Specifies the directory where user-installed product extensions will be stored.<br/><br/>*default value:* $PathCombine($SpecialWindowsPath(CommonApplicationData), `ProductName`, Extensions) |
| ExtensionsTempPath      | Specifies the directory where extensions are unpacked and loaded from.<br/><br/>*default value:* $PathCombine($SpecialWindowsPath(CommonApplicationData), `ProductName`, ExtensionsTemp) |
| LicenseKey              | If specified, this license key will be written to the database instance on installation.<br/><br/>*no default value* |
| UserAccount             | The name of the user account which will be used to run your Inedo product's services and/or IIS AppPool. It may be LocalSystem, NetworkService, or any domain/local account.<br/><br/>*default value:* NetworkService |
| UserPassword      | The password of the user account specified with the `UserAccount` argument. Ignored if using LocalSystem or NetworkService. <br/><br/>*no default value* |
| IsUpgrade               | When true, services and IIS settings will not be created.<br/><br/>*default value:* false |
| UsePostgres             | When true, the ConnectionString option is a PostgreSQL connection string; otherwise it is a SQL Server connection string.<br/><br/>*default value:* true for ProGet 2025+ |

### Installation Examples

```
hub.exe install ProGet:5.1.0 --ConnectionString="Data Source=localhost; Integrated Security=True;"
```

## Uninstall 

```
Usage: hub.exe uninstall <product>
```
Uninstalls the specified product.

## List 

```
Usage: hub.exe list
```
Lists the versions of each package installed, as well as the latest available versions of all products in the configured feed.

## Romp 

```
Usage: hub.exe romp <arguments for romp>
```
Executes the Hub Romp instance and forwards any following arguments.
This is useful primarily for troubleshooting failed installs using the romp jobs logs command.

## Help 

```
Usage: hub.exe help [command]
```
Displays help for this tool generally or any specific command.
