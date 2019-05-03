---
title: Installation
subtitle: Installing Romp
sequence: 10
keywords: romp, installation, configuration, maintenance
---

Romp requires no external dependencies and has a very light footprint, making installation and maintenance quite easy.

Romp is just an executable with some libraries, and doesn't require any special installation steps. By default, Romp will install as a machine-level tool, but it can also be installed and used as a user-level tool.

### Simple Romp Installer {#installer data-title="Simple Romp Installer"}

Just [download the installer](/romp/download) and run it; the installer will do two things:

1. Extract romp execution files to `%ProgramFiles%\Inedo\Romp`
2. Add romp to the PATH environment variable, so that you can use it in any directory

If you specify user-level installation, the files will instead be extracted to `%UserProfile%\.romp` folder, and the [userLevel configuration value](/support/documentation/romp/installation/configuration) will be set to true in the configuration file.

Note that Romp does not have an uninstaller, so to uninstall, just remove from your path and delete files.

### Chocolatey Package {#chocolatey data-title="Chocolatey Package"}

You can also just run `choco install romp` to install the [romp chocolatey package](https://chocolatey.org/packages/romp). This effectively runs the simple romp installer.

### Manual Installation {#manual data-title="Manual Installation"}

You can [download the zip file](/romp/download) containing Romp, and install it in any manner you'd like.
