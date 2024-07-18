---
title: "Installing Romp"
order: 4
---


Romp doesn't require any external dependencies and has a very small footprint, making it very easy to install and maintain.

Romp is just an executable file with some libraries and doesn't require any special installation steps. By default, Romp is installed as a machine-level tool, but it can also be installed and used as a user-level tool.

### Simple Romp Installer

Just [download the installer](https://inedo.com/romp/download) and run it; the installer will do two things:

1.  Extract Romp execution files to `%ProgramFiles%\Inedo\Romp`
2.  Add Romp to the PATH environment variable, so that you can use it in any directory

If you specify user-level installation, the files will instead be extracted to `%UserProfile%\.romp` folder, and the [userLevel configuration value](/docs/executionengine/romp-overview/installing-configuring-and-maintaining/romp-installation-configuration) will be set to true in the configuration file.

Note that Romp does not have an uninstaller, so to uninstall, simply remove it from your path and delete files.

### Chocolatey Package

You can also just run `choco install romp` to install the [Romp Chocolatey package](https://chocolatey.org/packages/romp). This effectively runs the simple Romp installer.

### Manual Installation

You can [download the zip file](https://inedo.com/romp/download) containing Romp, and install it in any manner you'd like.