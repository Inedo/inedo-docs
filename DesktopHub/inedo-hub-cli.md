---
title: Inedo Hub CLI
sequence: 30
keywords: proget, installation
---

A separate console program (hub.exe) is included with the standard GUI desktop hub. This tool supports the following commands:

## Install {#install data-title="Install"}

```
Usage: hub.exe install <product>[:version] [--arg=value]...
```

Installs/upgrades the specified product (ProGet, BuildMaster, Otter, or Hedgehog). If `[:version]` is not specified, the latest stable version is used. The `[--arg=value]` items are used to pass variables to Romp, and are the same variables defined for the package.

Installation arguments:


| Name                    | Description        |
| ----------------------- |--------------------|
| TargetDirectory         | The root install directory for ProGet.<br/><br/>*Default value:*<br/> $PathCombine($SpecialWindowsPath(ProgramFiles), ProGet) |
| ConnectionString        | Connection string used to create and initialize the database schema.<br/><br/>*no default value* |
| UseIntegratedWebServer  | When true, installs a service to host the integrated web server.<br/><br/>*default value:* true |
| UseIIS                  | When true, creates an app pool and site in IIS.<br/><br/>*default value:* false |
| DbName                  | Name of the SQL database to use or create. This overrides any Initial Catalog specified in the connection string.<br/><br/>*default value:* ProGet |
| WebServerPrefixes       | Specifies the URL which is used by the integrated web server. Ignored if UseIntegratedWebServer is not true.<br/><br/>*default value:* http://*:8624/ |
| ExtensionsPath          | Specifies the directory where user-installed product extensions will be stored.<br/><br/>*default value:* $PathCombine($SpecialWindowsPath(CommonApplicationData), ProGet, Extensions) |
| ExtensionsTempPath      | Specifies the directory where extensions are unpacked and loaded from.<br/><br/>*default value:* $PathCombine($SpecialWindowsPath(CommonApplicationData), ProGet, ExtensionsTemp) |
| LicenseKey              | If specified, this license key will be written to the database instance on installation.<br/><br/>*no default value* |
| UserAccount             | The name of the user account which will be used to run ProGet's services and/or IIS AppPool. It may be LocalSystem, NetworkService, or any domain/local account.<br/><br/>*default value:* NetworkService |
| IsUpgrade               | When true, services and IIS settings will not be created.<br/><br/>*default value:* false |
| RemoveLegacyComponents  | Removes legacy installations created before the Inedo Hub was available. (Versions of ProGet before 5.1.0).<br/><br/>*default value:* false |
| EncryptionKey           | 32-character hex string of encryption key for stored secrets. This value will be written to the product's configuration file on install only if there is not already an encryption key stored in the file.<br/><br/>*default value:* $CryptoRandomBytes(16) |

### Installation Examples

```
hub.exe install ProGet:5.1.0 --ConnectionString="Data Source=localhost; Integrated Security=True;"
```

## Uninstall {#uninstall data-title="Uninstall"}

```
Usage: hub.exe uninstall <product>
```
Uninstalls the specified product.

## List {#list data-title="List"}

```
Usage: hub.exe list
```
Lists the versions of each package installed, as well as the latest available versions of all products in the configured feed.

## Romp {#romp data-title="Romp"}

```
Usage: hub.exe romp <arguments for romp>
```
Executes the Hub Romp instance and forwards any following arguments.
This is useful primarily for troubleshooting failed installs using the romp jobs logs command.

## Help {#help data-title="Help"}

```
Usage: hub.exe help [command]
```
Displays help for this tool generally or any specific command.
