---
title: "Silent/Automated Installation Guide"
nav-title: "Silent Installation"
order: 3
---

This guide will show you how to perform a silent (non-interactive) installation of ProGet, BuildMaster, or Otter that won't display a user interface or prompt for input. This can be useful for automating your installation and upgrades.

Silent installations use the [Inedo Hub's Command Line Interface (CLI)](/docs/installation/windows/inedo-hub/cli-reference), which means that the Inedo Hub must be downloaded and installed first. However, you can perform a [silent/scripted installation of the Inedo Hub](#scripted-installation-of-inedo-hub) or even [create an offline installation bundle](/docs/installation/windows/inedo-hub/offline) that you can then distribute.

## How to Perform a Silent Installation

Before installing an Inedo product silently, you will need to have access to a SQL Server instance. Although the interactive (non-silent) process can install [SQL Server Express](https://www.microsoft.com/en-us/sql-server/sql-server-downloads) for you, it's not feasible for the Inedo Hub to do this silently.

Open Command Prompt and use the `cd` command to navigate to the Inedo Hub installation directory (default is `C:\Program Files\Inedo Hub`). For example:

```
 cd C:\Program Files\Inedo Hub
```

For a basic installation of an Inedo product, you can simply use the `install` command with the product name and a connection string that points to your SQL Server.

| Product | Command |
| -- | -- |
| ProGet | `hub.exe install ProGet --ConnectionString="Data Source=localhost; Integrated Security=True;"`
| BuildMaster | `hub.exe install BuildMaster --ConnectionString="Data Source=localhost; Integrated Security=True;"`
| Otter | `hub.exe install Otter --ConnectionString="Data Source=localhost; Integrated Security=True;"`

Note that you can’t use the `install` command without the `ConnectionString` argument, as the Inedo Hub would not know how and where to create and initialize the database schema.

Wait for the installer to finish. Once the installation is finished, you’ll get a message saying that your Inedo product is installed.

## How to Upgrade Silently

To silently upgrade an existing Inedo product using the Inedo Hub, follow the same process as a regular install. If a new version is available, it will be downloaded and installed.  Note that you only need to specify the `ConnectionString` if the product's stored connection string does not have permission to modify the database schema.

## Performing a Silent Uninstall 

If you need to uninstall an Inedo product for whatever reason, you can use the `uninstall` command. This requires no additional arguments.

| Product | Command |
| -- | -- |
| ProGet | `hub.exe uninstall ProGet`
| BuildMaster | `hub.exe uninstall BuildMaster`
| Otter | `hub.exe uninstall Otter`

Once the uninstallation is finished, you will get a confirmation message.

## Additional Silent Installation Options

It's possible to customize your Inedo product installation during the install stage by adding additional arguments to the command line when setting up. 

### Example: Specifying a License Key
For example, the following command will install ProGet with an existing license key so that you don't have to manually enter it later.

```
hub.exe install ProGet --ConnectionString="Data Source=localhost; Integrated Security=True;" LicenseKey=«your-license-key»
```

### Example: Installing to a Specific Version
By default, the Silent Install method will always download and install the newest version of the specified Inedo product. If you need to install a specific version, you can add the desired version number to the install string. 

For example, to install ProGet 5.2.3 using the upgrade process:

```
hub.exe install ProGet:5.2.3 --IsUpgrade=true
```

### Using Other Installation Arguments

Other common customization options include the ability to use IIS (`--UseIIS`), and specifying a custom path for Inedo extensions (`--ExtensionsPath=«custom-extensions-path»`)

A full list of features that can be customized during a silent instalation is available on our [Inedo Hub CLI guide](/docs/installation/windows/inedo-hub/cli-reference).

## Scripted Installation of Inedo Hub

The Inedo Hub may be installed manually by downloading the contents of the latest [DesktopHub package](https://proget.inedo.com/feeds/Products/InedoReleases/DesktopHub) as a zip file, and then extracting it to a target directory (such as `c:\InedoHub`).

Note that this process will not register the program as installed with Windows, nor will it add an icon to use the InedoHub in the start menu. 

### Example: PowerShell to Fully Script Installation
Following is an example script that will download the Inedo Hub, extract it to `c:\InedoHub`, and then run a silent installation of ProGet 5.2.3.

```powershell
# create working directories
mkdir C:\InedoHub
cd C:\InedoHub

# download and extract file to working directory
Invoke-WebRequest "https://proget.inedo.com/upack/Products/download/InedoReleases/DesktopHub?contentOnly=zip&latest" -OutFile C:\InedoHub\InedoHub.zip
Expand-Archive -Path InedoHub.zip -DestinationPath C:\InedoHub

# perform silent installation
hub.exe install ProGet:5.2.3 --ConnectionString="Data Source=localhost; Integrated Security=True;"
```