---
title: Silent Installation
sequence: 300
show-headings-in-nav: true
---

This guide will show you how to perform a silent (non-interactive) installation of ProGet that doesn’t display a user interface or ask any questions. Instead, the installer uses information from arguments for this purpose. Silent installations use the Windows command line to run the ProGet installer.

Note that a Silent installation of ProGet requires the Inedo Hub to be installed, unless you first [create an offline installer for ProGet](https://docs.inedo.com/docs/desktophub/offline).

- [How to install ProGet silently](#install)
- [Upgrading ProGet silently](#upgrade)
- [ProGet Silent Uninstall](#uninstall)
- [Additional options for Silent Installation](#options)

## How to install ProGet silently {#install data-title="Installing"}

Before installing ProGet silently, you will need to have access to a SQL Server instance. Although the interactive (non-silent) process can install [SQL Server Express](https://www.microsoft.com/en-us/sql-server/sql-server-downloads) for you, it's not feasible for the Inedo Hub to do this silently.

Open Command Prompt and use the "cd" command to navigate to the Inedo Hub installation directory (default is `C:\Program Files\Inedo Hub`). For example:

```
 cd C:\Program Files\Inedo Hub
```

For a basic installation of ProGet, enter:

```
hub.exe install ProGet --ConnectionString="Data Source=localhost; Integrated Security=True;"
```

Note that you can’t use “install ProGet” without the `ConnectionString` argument, as the Inedo Hub would not know how and where to create and initialize the database schema.

Wait for the installer to finish. Once the installation is finished, you’ll get a message saying that ProGet is installed.

## Upgrading ProGet silently {#upgrade data-title="Upgrading"}

To silently upgrade an existing ProGet installation using the Inedo Hub; open Command Prompt and use the "cd" command to navigate to the Inedo Hub installation directory (default is `C:\Program Files\Inedo Hub`). For example:

```
cd C:\Program Files\Inedo Hub
```

Like during a new silent installation for ProGet, you will need to use the command “hub.exe install ProGet” but this time using the “--IsUpgrade” command to let the installer know to not overwrite existing configuration files.
```
hub.exe install ProgGet --IsUpgrade=true
```

If a new version is available, it will be downloaded and installed.  You do not need to specify the `ConnectionString` argument.

## ProGet Silent Uninstall {#uninstall data-title="Uninstalling"}

If you need to uninstall ProGet for whatever reason, you can use the "uninstall" command. For example:

```
hub.exe uninstall ProGet
```

Once the uninstallation is finished, you will get a confirmation message.

## Additional Silent Installation Options {#options data-title="Additional Options"}

It's possible to customize your ProGet installation during the install stage by adding additional arguments to the command line when setting up. 

For example, the following command will automatically add your license key during installation so you don't have to manually enter it later.

```
hub.exe install ProGet --ConnectionString="Data Source=localhost; Integrated Security=True;" LicenseKey=«your-license-key»
```

By default, the Silent Install method will always download install the newest version of ProGet. If you need to install a specific version, you can add the desired version number to the install string. For example:

```
hub.exe install ProGet:5.2.3
```

Other common customization options include the ability to use IIS (`--UseIIS`), and specifying a custom path for ProGet extensions (`--ExtensionsPath=«your-license-key»`)

A full list of features that can be customized during a silent instalation is available on our [Inedo Hub CLI guide](https://docs.inedo.com/docs/desktophub/inedo-hub-cli).

