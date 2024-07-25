---
title: "Getting Started with pgutil"
order: 1
---

`pgutil` is an [open-source (github.com)](https://github.com/inedo/pgutil), cross-platform command line tool that provides a variety of commands to upload/download packages, manage feeds, audit package compliance, assess vulnerabilities, etc. 

## Installation & Configuration 

`pgutil` is a single-file executable program that you can download from the [releases page](https://github.com/Inedo/pgutil/releases) in the GitHub repository. The Windows archive (`pgutil-win-x64.zip`) will contain `pgutil.exe` and the Linux archives (`pgutil-linux-musl-x64.zip` and `pgutil-linux-x64.zip`) will contain `pgutil`.

No installation or configuration is necessary, though a [configuration file](#sources) may be created.

### Install as a .NET Global tool

If you have the [.NET Core SDK](https://learn.microsoft.com/en-us/dotnet/core/sdk) installed, you can install `pgutill` as a [.NET Global tool](https://learn.microsoft.com/en-us/dotnet/core/tools/global-tools):

## Commands & Usage
The easiest way to explore the available commands is by running `pgutil`, which provides a comprehensive list of "command sets". 

```cli
$> pgutil

    .--. --. ..- - .. .-..
        pgutil v1.0.0
    .--. --. ..- - .. .-..

Description:
  Perform operations against a ProGet server.

Usage:
  pgutil [command] [options]

Options:
  -?, --help  Show help and usage information

Commands:
  sources   Manages the list of sources located in the configuration file located at
            C:\Users\sdennis\AppData\Roaming\pgutil\pgutil.config
  packages  Work with packages on a ProGet server
  vulns     Audit packages and assess vulnerabilities
  builds    Manage SCA builds and SBOM documents
  licenses  Manage license definitions and audit package licenses
```

### pgutil Commands

If you run `pgutil` with a command (e.g. `pgutil packages`), it will return the description and usage of the command, along with all the options categorized under that particular set:

```
$> pgutil packages

Description:
  Work with packages on a ProGet server

Usage:
  pgutil packages [command] [options]


Common Options (packages):
  --api-key=<api-key>    ProGet API key used to authorize access
  --feed=<feed>          Name of feed in ProGet
  --no-connectors        Only include local (non-connector) package data in results
  --password=<password>  ProGet user password used to authorize access
  --source=<source>      Named source or URL of ProGet
  --username=<username>  ProGet user name used to authorize access
  -?, --help             Show help and usage information

Commands:
  download   Download a package file from ProGet
  upload     Upload a package file to ProGet.
  delete     Deletes a package from ProGet
  status     Change the status of a package on ProGet
  repackage  Repackages a package in ProGet to a package with a different version
  promote    Promotes a package from one feed to another
  audit      Analyze a package for compliance issues
```
To return a detailed list of options for any command, you can use the `--help` option with the respective command. For example, to view all available options for the command `pgutil packages download`, run:

```
pgutil packages download --help
```

The [API documentation](/docs/proget/reference-api) is organized around these command sets, allowing easier navigation and understanding of the available functions.

### Feature Requests for new Commands

We're very open to developing and enhancing `pgutil`, and working closely with users to do that. You're welcome to simply post a request to the [Inedo Forums](https://forums.inedo.com/), but may also be able to show us exactly how you'd like a command to work by prototyping something in the [pgutil GitHub repository](https://github.com/inedo/pgutil).

We are unlikely to remove any commands once they're added, except during a major version update, and we'll make sure to notify users beforehand.

## Working with Sources { #sources }

To help manage urls, feeds, and credentials (API keys), you can register one or more "sources" with `pgutil`. These sources are stored in the configuration file located at `%appdata%\pgutil\pgutil.config` (Windows) and `~/.config/pgutil/pgutil.config` (Mac/Linux).

### Adding Sources to pgutil { #sources-add }

The `sources add` command is used to add a source to the configuration file. 

At a minimum, a source requires a name (e.g. `Default`) and a ProGet server url (e.g. `https://proget.corp.local/`), but you you'll likely also want to add an API key (e.g. `abc12345`).

:::(Info) (🚀 Quick Example: Adding Default Source with an API Key)
```cli
pgutil sources add --name=Default --url=https://proget.corp.local/ --api-key=abc12345
```
:::

Instead of specifying `--api-key`, you can specify a username and password (e.g. 
`--username devbuilds --password myP@ssw0rd`). However, API keys are generally recommended, especially since sources are persisted in configuration files.

#### "Default" source
A source named "Default" has special behavior. When a source name is not specified in other commands, then "Default" will be used.  This means that, when there is a "Default" source, then that will be used on all commands unless `--source` is specified instead.

#### Passwords and Encryption

Unless you specify the `--plain-text` option, Passwords and API Keys will not be stored in plain text. Instead, they will be  obscured from casual consumption using a Data Protection scheme . However, note that anyone with `pgutil` will be able to read and decode the password or API key.


### Test Sources Added to pgutil { #sources-test }

The `sources test` command is used to test the connections to your sources. 

```cli
$: pgutil sources test

[Default] Successfully contacted ProGet 24.0.0
[MyOtherServer] Successfully contacted ProGet 23.0.32
[MyBadSource] Error contacting ProGet: invalid host name
```
 If you specify the `--name` option, then only that source will be tested. 
 
 Note that any non-successful test will report a nonzero exit code.

### Listing Sources { #sources-list }

The `sources list` command will list all sources located in the configuration file.

```cli
$> pgutil sources list

1. Default
   https://api@proget.corp.local/

2. MyPackages (unapproved-nuget feed)
   https://devbuilds@othersource.somewhere.dev/
```

The `sources list` command does not require any arguments.

### Removing Sources { #sources-remove }

The `sources remove` command will remove a source from the configuration file.

```cli
$> pgutil sources remove --name=MyPackages

Removed MyPackages source.
```

Note that you must specify the `--name` when removing a source, even for the "Default" source.
