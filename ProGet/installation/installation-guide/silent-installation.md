---
title: Silent Installation
sequence: 300
keywords: proget, installation, silent-installation
---
<!---### Table of Contents --->

This guide will show you how to perform a silent (non-interactive) installation of ProGet that doesn’t display a user interface or ask any questions. Instead, the installer uses information from arguments for this purpose. Silent installations use the Windows command line to run the ProGet installer.
Note that a Silent installation of ProGet requires the Inedo Hub to be installed. You can use the Inedo Hub to [create an offline installer for ProGet and other Inedo products](https://docs.inedo.com/docs/desktophub/offline).

- [Basic Installation](#basic-installation)
- [ProGet Silent Upgrade](#proget-silent-upgrade)
- [ProGet Silent Uninstall](#proget-silent-uninstall)
- [Customizing Your ProGet Silent Installation](#customizing-your-proget-silent-installation)

## [Basic Installation](#basic-installation)

Open Command Prompt and use the "cd" command to navigate to the Inedo Hub install directory. Default is C:\Program Files\Inedo Hub.

E.g.
```
 cd C:\Program Files\Inedo Hub
```

For a basic installation of ProGet, enter:
```
hub.exe install ProGet --ConnectionString="Data Source=localhost; Integrated Security=True;"
```

You can’t use “install ProGet” without defining ConnectionStrings, as ProGet would not know how and where to create and initialize the database schema.

Wait for the installer to finish. Once the installation is finished, you’ll get a message saying that ProGet is installed.

## [ProGet Silent Upgrade](#proget-silent-upgrade)

To silently upgrade an existing ProGet installation using the Inedo Hub; open Command Prompt and use the “cd” command to navigate to the Inedo Hub install directory. Default is C:\Program Files\Inedo Hub.

E.g.
```
cd C:\Program Files\Inedo Hub
```

Like during a new silent installation for ProGet, you will need to use the command “hub.exe install ProGet” but this time using the “--IsUpgrade” command to let the installer know to not overwrite existing configuration files.
```
hub.exe install ProgGet --IsUpgrade=true
```

If a new version is available, it will be downloaded and installed

## [ProGet Silent Uninstall](#proget-silent-uninstall)

If you need to uninstall ProGet for whatever reason, open Command Prompt and use the “cd” command to navigate to the Inedo Hub install directory. Default is C:\Program Files\Inedo Hub.

E.g.
```
 cd C:\Program Files\Inedo Hub
```

The command for uninstalling ProGet is:
```
hub.exe uninstall ProGet
```

Once the uninstallation is finished, you will get a confirmation message.

## [Customizing Your ProGet Silent Installation](#customizing-your-proget-silent-installation)

It's possible to customize your ProGet installation during the install stage by adding additional arguments to the command line when setting up. 

For example, you could enter 
```
hub.exe install ProGet --ConnectionString="Data Source=localhost; Integrated Security=True;" LicenseKey=`<value>`
```
to insert your license key directly during installation to save you having to manually enter it later.

By default, the Silent Install method will always download install the newest version of ProGet. If you need to install a specific version, you can add the desired version number to the install string.

E.g.
```
hub.exe install ProGet:5.1.0
```
Other common customization options include the ability to use IIS (--UseIIS), and specifying a custom path for ProGet extensions (--ExtensionsPath=`<value>`)

A full list of features that can be customized during a silent instalation is available on our [Inedo Hub CLI guide](https://docs.inedo.com/docs/desktophub/inedo-hub-cli).

