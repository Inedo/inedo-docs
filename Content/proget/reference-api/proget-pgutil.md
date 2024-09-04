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

```plaintext
$> pgutil
    .--. --. ..- - .. .-..
        pgutil v2.0.0.0
    .--. --. ..- - .. .-..

Description:
  Perform operations against a ProGet server.

Usage:
  pgutil [command] [options]

Commands:
  sources     Manages the list of sources located in the configuration file located at
              C:\Users\Vasse\AppData\Roaming\pgutil\pgutil.config
  health      Displays health and status information
  packages    Works with packages on a ProGet server
  vulns       Audits packages and assesses vulnerabilities
  builds      Manages SCA builds and SBOM documents
  licenses    Manages license definitions and audits package licenses
  apikeys     Manages ProGet API keys
  assets      Works with a ProGet asset directory
  settings    Manages ProGet Settings
  feeds       Views and manages ProGet feeds
  connectors  Views and manages ProGet connectors
```

### pgutil Commands

If you run `pgutil` with a command (e.g. `pgutil packages`), it will return the description and usage of the command, along with all the options categorized under that particular set:

```plaintext
$> pgutil packages

Description:
  Works with packages on a ProGet server

Usage:
  pgutil packages [command] [options]

Commands:
  download   Downloads a package file from ProGet
  upload     Uploads a package file to ProGet.
  delete     Deletes a package from ProGet
  status     Changes the status of a package on ProGet
  repackage  Repackages a package in ProGet to a package with a different version
  promote    Promotes a package from one feed to another
  audit      Analyzes a package for compliance issues
  list       Displays latest versions of packages in a feed
  versions   Displays all versions of packages in a feed
```

To return a detailed list of options for any command, you can use the `--help` option with the respective command. For example, to view all available options for the command `pgutil packages download`, run:

```plaintext
pgutil packages download --help
```

The [API documentation](/docs/proget/reference-api) is organized around these command sets, allowing easier navigation and understanding of the available functions.

### Feature Requests for new Commands

We're very open to developing and enhancing `pgutil`, and working closely with users to do that. You're welcome to simply post a request to the [Inedo Forums](https://forums.inedo.com/), but may also be able to show us exactly how you'd like a command to work by prototyping something in the [pgutil GitHub repository](https://github.com/inedo/pgutil).

We are unlikely to remove any commands once they're added, except during a major version update, and we'll make sure to notify users beforehand.

### How is pgutil Pronounced?

In addition to a growing number of commands options, `pgutil` offers multiple pronunciation options for maximum flexibility. 

The most popular option -- and our absolute favorite -- is /pəˈɡuːtəl/ ("puh-goo-tuhl"), which uses a soft "puh" sound, followed by a stressed "goo" sound, and another soft "tuhl." 

We've also heard /pəˈɡʌtəl/ ("puh-guh-tuhl") and /piːˈɡjuːtɪl/ ("pee-GYOO-till"). Every now and then, we'll hear /piːdʒiːʌtɪl/ ("pee-gee-utt-il").


## Working with Sources { #sources }

To help manage urls, feeds, and credentials (API keys), you can register one or more "sources" with `pgutil`. These sources are stored in the configuration file located at `%appdata%\pgutil\pgutil.config` (Windows) and `~/.config/pgutil/pgutil.config` (Mac/Linux).

### Adding Sources to pgutil { #sources-add }

The `sources add` command is used to add a source to the configuration file. 

At a minimum, a source requires a name (e.g. `Default`) and a ProGet server url (e.g. `https://proget.corp.local/`), but you you'll likely also want to add an API key (e.g. `abc12345`).

:::(Info) (🚀 Quick Example: Adding Default Source with an API Key)
```plaintext
pgutil sources add --name=Default --url=https://proget.corp.local/ --api-key=abc12345
```
:::

Instead of specifying `--api-key`, you can specify a username and password (e.g. 
`--username devbuilds --password myP@ssw0rd`). However, API keys are generally recommended, especially since sources are persisted in configuration files.

#### "Default" source
A source named "Default" has special behavior. When a source name is not specified in other commands, then "Default" will be used.  This means that, when there is a "Default" source, then that will be used on all commands unless `--source` is specified instead.

#### Passwords and Encryption

Unless you specify the `--plain-text` option, Passwords and API Keys will not be stored in plain text. Instead, they will be  obscured from casual consumption using a Data Protection scheme . However, note that anyone with `pgutil` will be able to read and decode the password or API key.

### Listing Sources { #sources-list }

The `sources list` command will list all sources located in the configuration file.

```plaintext
$> pgutil sources list

1. Default
   https://api@proget.corp.local/

2. MyPackages (unapproved-nuget feed)
   https://devbuilds@othersource.somewhere.dev/
```

The `sources list` command does not require any arguments.

### Removing Sources { #sources-remove }

The `sources remove` command will remove a source from the configuration file.

```plaintext
$> pgutil sources remove --name=MyPackages

Removed MyPackages source.
```

Note that you must specify the `--name` when removing a source, even for the "Default" source.

### Test Sources Added to pgutil { #sources-test }

The `sources test` command is used to test the connections to your sources. 

```plaintext
$: pgutil sources test

[Default] Successfully contacted ProGet 24.0.0
[MyOtherServer] Successfully contacted ProGet 23.0.32
[MyBadSource] Error contacting ProGet: invalid host name
```

 If you specify the `--name` option, then only that source will be tested. 
 
 Note that any non-successful test will report a nonzero exit code.
