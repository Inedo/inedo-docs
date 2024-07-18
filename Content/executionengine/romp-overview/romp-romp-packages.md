---
title: "Package Layout"
order: 4
---


## Package Metadata

At a minimum, a Romp package must contain a `upack.json` file.

See the [Universal Package Metadata Specification](/docs/proget/upack/upack-overview#metadata) for a list of `upack.json` properties.

A Romp package may also include an additional metadata file, `rompPackage.json`, that defines package behavior.

### Romp Package Metadata Specification

Just like `upack.json`, a `rompPackage.json` file is a JSON object with the following optional properties:

|   |  |
| --- | --- |
| `minimumClient` | a _string_ representing a three-part version number that describes the lowest version of the Romp client that may install the package; if not specified, any client version may be used |
| `requiredExtensions` | an _array of strings_ containing the names of extensions that must be installed in the Romp’s extension; if not specified, the default extensions are InedoCore, Windows, and Linux  <p> _Note: when you define this, make sure you also specify the default values if you want them to be required as well_ |
| `machineOnly` | a _boolean_ indicating whether the package may only be installed as a machine package; this defaults to false |


_However_, unlike `upack.json`, the `rompPackage.json` file may not contain any additional properties.

## Installing Romp
Romp doesn't require any external dependencies and has a very small footprint, making it very easy to install and maintain.

Romp is just an executable file with some libraries and doesn't require any special installation steps. By default, Romp is installed as a machine-level tool, but it can also be installed and used as a user-level tool.

### Simple Romp Installer

Just [download the installer](https://inedo.com/romp/download) and run it; the installer will do two things:

1.  Extract Romp execution files to `%ProgramFiles%\Inedo\Romp`
2.  Add Romp to the PATH environment variable, so that you can use it in any directory

If you specify user-level installation, the files will instead be extracted to `%UserProfile%\.romp` folder, and the [userLevel configuration value](/docs/executionengine/romp-overview/installing-configuring-and-maintaining/romp-installation-configuration) will be set to true in the configuration file.

Note that Romp does not have an uninstaller; to uninstall, just remove it from your path and delete files.

### Chocolatey Package

You can also just run `choco install romp` to install the [romp chocolatey package](https://chocolatey.org/packages/romp). This effectively runs the simple romp installer.

### Manual Installation

You can [download the zip file](https://inedo.com/romp/download) containing Romp, and install it in any manner you'd like.

## Uninstallation Script

A Romp package may contain an `uninstall.otter` file. If this file is not present, then the package does not support uninstallation.

The `uninstall.otter` file in the package root contains the OtterScript of the plan that will be executed when the [uninstall command](/docs/executionengine/romp-overview/romp-cli-reference/romp-command-line-command-overview/romp-command-line-command-overview-installation#uninstall) is invoked.

### Basic Package File Uninstallation

If your package contains only content files, then only a trivially simple `uninstall.otter` script is needed.

```
Romp::Delete-Contents
```

This will invoke the Delete-Contents operation, which deletes all content files which were installed using the install command.

### Built-In Variables

The following variables are available to the `uninstall.otter` script:

*   `$TargetDirectory` - specifies the file system path where the package content was deployed, and the `$WorkingDirectory` variable will also be set to this path initially.
*   `$PackageGroup` - specifies the group name of the package being uninstalled.
*   `$PackageName` - specifies the name of the package being uninstalled.
*   `$PackageVersion` - specifies the version of the package being uninstalled.


## Variables

Package variables typically contain configuration values that are required for installing a package in a specific environment, or to allow installation to be customized.

Variables are defined in a `packageVariables.json` file located in the package root with `upack.json`. It is simply an object with variable names and values as properties. The value can be either a string, null, or an object with the following optional properties:


|  |  |
| --- | --- |
| `value` | a _string_ of the variable’s value itself |
| `description` | a _string_ to document the variable's intended usage |
| `required` | a _boolean_ to indicate that a value is required; when a required variable does not have value (i.e. not null or empty), the installation will not proceed; defaults to false if not specified |
| `sensitive` | a _boolean_ to indicate that the value should not be displayed in logs; default is false <p> *Note: an installation script may still Log the value of the variable to the console* |
| `type` | a _string_ of the variable's type; `text` (default), `boolean` (indicates only `true` or `false` string literals are allowed, `list`, or `map` |


When an object is not specified for a variable value, a string indicates a required text variable, while a null represents a required text variable without a value. If you wish to allow an empty string, you will need to use the object syntax.

### Example: packageVariables.json file

```
{
  "myStringValue": "Steve Dennis",
  "myArray": \[1, 2, 3, 4, 5\],
  "myMapValue": {
    "p1": "hdars",
    "p2": "test"
  },
  "requiredButNotSpecifiedValue": null
}
```

The example above defines 3 variables with values. Each property on the JSON object corresponds to a runtime OtterScript variable of `scalar`, `vector`, and `map` types respectively. The final variable, `requiredButNotSpecifiedValue`, is required but not specified. This will cause Romp to prompt for this value when the package is installed.

Package variables specified as command line arguments are returned first, followed by variables in packageVariables.json, followed by raft variables, in the event of variables of the same name.

**You should not store sensitive passwords or other secrets in variables. They are not secure, at all. Instead, consider [resource credentials](/docs/executionengine/romp-overview/romp-cli-reference/romp-command-line-command-overview/romp-command-line-command-overview-credentials).**

### Prompting for Required Variables

If a variable is required for package installation, it may be passed in as an argument to Romp. If you don't pass in a required variable, then you will be prompted for it (when running interactively), or Romp will issue an error and not proceed with installation.

### Best Practices: Variable Descriptions

Think of variable descriptions like comments. If it's not obvious from the name how to use a variable, or how it might work, you should briefly describe that in the field. You can always include additional instructions on how to use the package in the package's description metadata field.

Variable descriptions are primarily used by `{cmd line}` to show a user how to install a package, and should be brief.


## Passwords and Other Secret Credentials

Romp supports _Resource Credentials_, which are named credentials used to access a protected resource. Usually, credentials include secrets such as a username and password combination, and are stored securely using encryption, both in memory (during runtime), and on disk.

Like [variables](#variables), the required credentials are defined in a `packageCredentials.json` file in the package root. It is an array of objects that describe credentials. Each object has the following properties.


|  |  |
| --- | --- |
| `typeR` | a _string_ describing the type of the credential; typically it's `UsernamePassword` |
| `nameR` | a _string_ that contains the name of the credential used by the installation script |
| `description` | a _string_ to document the credential’s intended usage |
| `restricted` | a _boolean_ that indicates whether the `$CredentialValue` variable function may extract a value from the credential; defaults to false |
| `defaults` | an _object_ with key/value pairs representing a non-secured credential property and its default value  <p> *Note: if a secured property (like Password) is defined, it will be ignored* |


An "`R`" denotes a required property. When an object is not specified for a variable, a string indicates a required text variable, while a null represents a required text variable without a value.

### Example:

```
[
  {
    "type": "UsernamePassword",
    "name": "MyServerLogin",
    "description": "Use sdennis",
    "defaults": { // JSON representation of credentials object (plaintext)
      "UserName": "sdennis",
      "Password": null // cannot define because it’s secure
    }
 ]
```

In the above example, three username/password credentials are specified. In the first definition, the credentials are specified as pure JSON with any secrets clearly exposed. In the second definition, the JSON for the credentials object has been base64 encoded to help prevent revealing a secret through casual observation. In the third definition, the object is completely unspecified, and Romp will prompt for it when installing the package.

Embedding credentials in a package is a convenience, and the limited obfuscation provided should not be mistaken for any kind of cryptographically secure storage. The only secure way to store credentials in a package is to not store them at all, or to encrypt the entire package by some other means.

### Locally Stored Credentials

Romp can store named credentials in an encrypted manner in its local configuration database, meaning you won't have to enter it at installation time or pass it in as an argument. See the [romp store credentials](/docs/executionengine/romp-overview/romp-cli-reference/romp-command-line-command-overview/romp-command-line-command-overview-credentials#store) command for more details.

### Prompting for Credentials

Credentials may be passed in as an argument to Romp. Like variables, if you don't pass in a required variable, and one isn't defined as a locally-stored credential, then you will be prompted for it (when running interactively). Otherwise Romp will issue an error and not proceed with installation.

### Credentials as Variable Values

You can use the `$CredentialProperty` variable function to extract a property from a credential, unless that credential is marked as "restricted". When restricted, only operations that can access secure storage will be able to read these properties.

### Rafts
A raft is used to store plans, templates, variables, and asset files. If a Romp Package has any dependency on raft data, the entire raft can be included in the package. Further information about rafts can be found in the [Otter documentation](/docs/otter/scripting-in-otter/otter-rafts-and-git-storage).