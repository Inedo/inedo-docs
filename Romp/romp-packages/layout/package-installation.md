---
title: Installation Script
subtitle: Installation Script
sequence: 20
keywords: romp, installation, configuration
---

A romp package must contain an `install.otter` file.

The `install.otter` file in the package root contains the OtterScript of the plan that will be executed when the [install command](/support/documentation/romp/command-line/command-overview/installation#install) is invoked.

## Basic Package File Installation {#basic data-title="Basic Package File Installation"}

If your package contains only content files, then only a trivially simple `install.otter` script is needed.

```
Romp::Deploy-Contents
```

This will invoke the Deploy-Content operation, which simply copies the contents of the package to `$TargetDirectory`. This is generally a commandline argument passed in to romp, but may also be specified with a default value in the `packageVariables.json` file.

## Built-In Variables {#built-in data-title="Built-In Variables"}

The following variables are available to the `install.otter` script:

{.docs}
- `$TargetDirectory`- specifies the file system path where the package content should be deployed. This directory is guaranteed to exist before `install.otter` begins to execute, and the `$WorkingDirectory` variable will also be set to this path initially.
- `$PackageGroup` - specifies the group name of the package being installed.
- `$PackageName` - specifies the name of the package being installed.
- `$PackageVersion` - specifies the version of the package being installed.
