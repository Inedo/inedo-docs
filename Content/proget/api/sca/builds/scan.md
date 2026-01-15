---
title: "Scan Build"
order: 6
---

*Scan Build* is available as a `pgutil` command, and will generate a minimal SBOM from project dependencies, upload it to ProGet, then audit the build.

:::(Info) (🚀 Quick Example: Scanning a build with pgutil)
This example scans build version `1.2.3` of the project `myProject` from the current working directory.

```bash
pgutil builds scan --project-name="myProject" --version=1.2.3
```
:::

## Command Specification (CLI)
The `builds scan` command is used to scan a build of a project and [audits the build](/docs/proget/api/sca/builds/analyze).

The `--project-name` and `--version` options are always required and will scan the current working directory for .NET solutions or projects, npm `package-lock.json` files, cargo `Cargo.lock` files, composer `composer.lock`, or PyPI and Conda `requirements.txt` files.

If you would like to scan a specific file or folder, you can specify it using the `--input` option.  If you have multiple project types within the same folder, you can specify the package type using the `--scanner-type` parameter.  


| Option | Description 
| --------- | ----------- 
| <span style="white-space:nowrap">`--project-name=«project-name»`</span> | [Required] Name of the component consuming the dependencies
| `--version=«version»` | [Required] Version of the component consuming the dependencies
| `--input=«input»` | Project to scan for dependencies (default=/)
| `--scanner-type=«scanner-type»` | Type of project scanner to use; auto, npm, NuGet, PyPI, Conda, Composer, or Cargo (default=auto)
| `--noaudit` | Do not run audit after scan
| `--project-type=«project-type»` | Type of the consuming project (default=library)
| `--include-project-reference` | For NuGet, Include dependencies from referenced projects in the generated SBOM document.
| `--include-dev-dependencies` | For NuGet, Composer, and npm, Include development dependencies for npm or composer in the generated SBOM document.
| `--do-not-scan-node_modules` | For NuGet and npm, Do not scan the node_modules directory when scanning for package-lock.json files.


::: (Info) (Note:)
When scanning a .NET project or solution or a PyPI environment, if the `--scanner-type` is set to `auto`, it will also scan for npm packages.
:::

```plaintext
$> pgutil builds scan --project=myProject --build=1.2.3
Scanning for dependencies in C:\projects\myProject...
Publishing SBOM to ProGet...
SBOM published.
Auditing myProject 1.2.3...

Created        : 11/19/2024 4:22:35 PM
Status         : Active
Release        : -
Build stage    : Build
Last Analysis  : Noncompliant (Resolved) on 11/19/2024 5:06:11 PM
Total Packages : 1478

-= Packages =-

Azure.Core-1.35.0
 Compliance      : Compliant
 License         : MIT
 Vulnerabilities : None

Microsoft.Extensions.Configuration.EnvironmentVariables 8.0.0 
 Compliance      : Noncompliant
 License         : MIT, Apache-2.0
 Vulnerabilities : PG-123456 (High)
   the vulnerability title of this vulnerability goes here
```

When the build status is "Noncompliant (Unresolved)", this command will return a nonzero exit code.  If the `--noaudit` flag is passed and an error scanning or uploading to ProGet, this command will return a nonzero exit code.

## Example Commands

#### Example: Scanning the working directory for an npm project
```
pgutil builds scan --project-name="myProject" --version=1.2.3 --scanner-type=npm
```

#### Example: Specifying package-lock.json file for npm
```
pgutil builds scan --input="c:\path\to\npm\project\package-lock.json" --project-name="myProject" --version=1.2.3 --scanner-type=npm
```

#### Example: Scanning a .NET Solution using the Working Directory
```
pgutil builds scan --project-name="myProject" --version=1.2.3 --scanner-type=NuGet
```

#### Example: Specifying a C# Project
```
pgutil builds scan --input="c:\path\to\project.csproj" --project-name="myProject" --version=1.2.3 --scanner-type=NuGet
```

#### Example: Specifying a .NET Solution
```
pgutil builds scan --input="c:\path\to\solution.sln" --project-name="myProject" --version=1.2.3 --scanner-type=NuGet
```

#### Example: Specifying a .NET Solution for NuGet and npm dependencies
```
pgutil builds scan --input="c:\path\to\solution.sln" --project-name="myProject" --version=1.2.3
```

#### Example: Scanning a Cargo project using the Working Directory
```bash
pgutil builds scan --project-name="myProject" --version=1.2.3 --scanner-type=Cargo
```

#### Example: Scanning a Composer project using the Working Directory
```bash
pgutil builds scan --project-name="myProject" --version=1.2.3 --scanner-type=Composer
```

#### Example: Scanning a PyPI environment using the Working Directory
```bash
pgutil builds scan --project-name="myProject" --version=1.2.3 --scanner-type=PyPI
```

#### Example: Scanning Conda Projects
To use pgutil to scan dependencies in Conda applications and libraries, you will need to create a Conda environment file.  To create a Conda environment file, you will need to run either:

```bash
conda list --explicit > requirments.txt
```

or

```bash
conda list --export > requirments.txt
```

This will list create a text file with a list of dependencies used by your Conda library or application.  Once this file has been generated, you will need to run `pgutil builds scan`.

```bash
conda list --explicit > requirments.txt
pgutil builds scan --project-name="myProject" --version=1.2.3 --scanner-type=Conda
```
