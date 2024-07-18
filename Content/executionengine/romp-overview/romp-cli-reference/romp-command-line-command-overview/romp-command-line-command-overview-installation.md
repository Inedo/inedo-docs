---
title: "Installation, Inspection, and Verification"
order: 7
---

Romp is invoked as follows:

```
romp «command»
```

The **«command»** is one of the following:

*   [`install`](#install) - Install the specified romp package, unless it's already installed.
*   [`validate`](#validate) - Validates a package file on disk to ensure the layout and OtterScript are valid.
*   [`inspect`](#inspect) - Validates the package and displays a report of the contents, required variables, and credentials.
*   [`uninstall`](#uninstall) - Uninstall the specified romp package.

### Install

```
romp install «package-file-or-name» [--version=«version-number»]
    [--source=«feedurl»] 
    [--authentication=«api-key-or-username-password»]

    [--unregistered] [--force]
    [--comment=«comment-text»]
    [--V«variable-name»=«variable-value»]
```

This command has two run modes: file, where a file on disk is used for installation, and package source, where a universal feed is queried for a package.

#### Arguments and Flags


|  |  |  |
| --- | --- | --- |
| `packageR` | positional | This is either a file name or a package name <p> If this argument ends with `.upack`, then the command runs in file mode; otherwise it will run in package source mode |
| `version` | value | In package source mode, this references the specific version of a package to install. When not specified, the latest version is used.  <p> Specifying this argument in file mode will result in an error. |
| `source` | value | See [Common Package Source Arguments](/docs/executionengine/romp-overview/romp-cli-reference/romp-command-line-common-configuration). <p<>Specifying this argument in file mode will result in an error. |
| `authentication` | value | See [Common Package Source Arguments](/docs/executionengine/romp-overview/romp-cli-reference/romp-command-line-common-configuration). |
| `unregistered` | flag | The package installation will not be registered; this does not impact storage of the installation logs. |
| `force` | flag | If the package is already installed, it will be reinstalled. |
| `comment` | value | Comment for the local package registry.  <p> Specifying this argument with the unregistered flag will result in an error. |


An "`R`" denotes a required property. When an object is not specified for a variable, a string indicates a required text variable, while a null represents a required text variable without a value.

#### Specifying Variables

You can specify variables as additional arguments by prepending the argument name with a "V".

#### Required Variables and Credentials

If the package specifies required [variables](/docs/executionengine/romp-overview/romp-romp-packages#variables) or [credentials](/docs/executionengine/romp-overview/romp-cli-reference/romp-command-line-command-overview/romp-command-line-command-overview-credentials), and you are running Romp interactively, you will be prompted for these values before the plan is executed.

Otherwise, Romp will exit with an error.

### Validate

```
romp validate «package-file»
```

The following are verified: file format (zip), file layout, metadata validity, OtterScript validity.

#### Arguments and Flags

This has only a single, required argument: the path to the package file on disk.

### Inspect

```
romp inspect «package-file-or-name» [--version=«version-number»]
    [--source=«feedurl»] 
    [--authentication=«api-key-or-username-password»]
```

This command has two run modes: file, where a file on disk is used for installation, and package source, where a universal feed is queried for a package.

#### Arguments and Flags


|  |  |  |
| --- | --- | --- |
| `packageR` | positional | This is either a file name or a package name <p>If this argument ends with `.upack`, then the command runs in file mode; otherwise it will run in package source mode |
| `version` | value | In package source mode, this references the specific version of a package to install. When not specified, the latest version is used.  <P> Specifying this argument in file mode will result in an error. |
| `source` | value | See [Common Package Source Arguments](/docs/executionengine/romp-overview/romp-cli-reference/romp-command-line-common-configuration). <p> Specifying this argument in file mode will result in an error.  |
| `authentication` | value | See [Common Package Source Arguments](/docs/executionengine/romp-overview/romp-cli-reference/romp-command-line-common-configuration). |

An "`R`" denotes a required property. When an object is not specified for a variable, a string indicates a required text variable, while a null represents a required text variable without a value.

### Uninstall
```
romp uninstall «package-name»
    [--V«variable-name»=«variable-value»]
```

This command uninstalls a previously installed Romp package. The package must have an uninstall.otter script file for this to be supported.

#### Arguments and Flags

|  |  |  |
| --- | --- | --- |
| `package-nameR` | positional | This a package name of a package that is already installed. |


An "`R`" denotes a required property. When an object is not specified for a variable, a string indicates a required text variable, while a null represents a required text variable without a value.

#### Specifying Variables

You can specify variables as additional arguments by prepending the argument name with a "V".

#### Required Variables

If the package specifies required [variables](/docs/executionengine/romp-overview/romp-romp-packages#variables), and you are running Romp interactively, you will be prompted for these values before the plan is executed.

Otherwise, Romp will exit with an error.