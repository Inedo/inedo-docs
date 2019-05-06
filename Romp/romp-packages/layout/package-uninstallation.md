---
title: Uninstallation Script
subtitle: Uninstallation Script
sequence: 21
keywords: romp, installation, configuration
---

A Romp package may contain an `uninstall.otter` file. If this file is not present, then the package does not support uninstallation.

The `uninstall.otter` file in the package root contains the OtterScript of the plan that will be executed when the [uninstall command](/support/documentation/romp/command-line/command-overview/installation#uninstall) is invoked.

## Basic Package File Uninstallation {#basic-uninstallation data-title="Basic Package File Uninstallation"}

If your package contains only content files, then only a trivially simple `uninstall.otter` script is needed.

```
Romp::Delete-Contents
```

This will invoke the Delete-Contents operation, which deletes all content files which were installed using the install command.

## Built-In Variables {#built-in data-title="Built-In Variables"}

The following variables are available to the `uninstall.otter` script:

{.docs}
- `$TargetDirectory` - specifies the file system path where the package content was deployed, and the `$WorkingDirectory` variable will also be set to this path initially.
- `$PackageGroup` - specifies the group name of the package being uninstalled.
- `$PackageName` - specifies the name of the package being uninstalled.
- `$PackageVersion` - specifies the version of the package being uninstalled.
