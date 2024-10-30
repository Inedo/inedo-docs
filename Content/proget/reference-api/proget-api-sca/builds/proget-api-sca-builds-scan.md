---
title: "Scan Build"
order: 6
---

*Scan Build* is available as a `pgutil` command, and will generate a minimal SBOM from project dependencies and uploads it to ProGet.

:::(Info) (🚀 Quick Example: Scanning a build with pgutil)
This example scans build version `1.2.3` of the project `myProject`

```bash
pgutil builds scan --input=myApplication.csproj --project-name="myProject" --version=1.2.3
```
:::

## Command Specification (CLI)
The `builds scan` command is used to scan a build of a project.

The `--input`, `--project-name` and `--version` options are always required.

**Scanning a project** requires the input (e.g. `myApplication.csproj`), project name (e.g. `myProject`) and build version (e.g. `1.2.3`):

```bash
pgutil builds scan --input=myApplication.csproj --project-name="myProject" --version=1.2.3
```

## Scanning npm Projects
To use pgutil to scan npm projects for dependencies, you will need to scan either the root project directory or the package-lock.json file.  When scanning an npm directory, pgutil will scan the root and all sub-folders for package-lock.json files.  

### Additional Options

| Parameter | Description |
| --------- | ----------- |
| `--do-not-scan-node_modules` | Do not scan the node_modules directory when scanning for package-lock.json files. |
| `--include-dev-dependencies` | Include npm development dependencies from the package-lock.json file in the generated SBOM document. |

### Examples

#### Example: Scanning a directory
```
pgutil builds scan --input="c:\path\to\npm\project" --project-name="myProject" --version=1.2.3 --scanner-type=npm
```

#### Example: Scanning a package-lock.json
```
pgutil builds scan --input="c:\path\to\npm\project\package-lock.json" --project-name="myProject" --version=1.2.3 --scanner-type=npm
```

## Scanning NuGet Projects
To use pgutil to scan for NuGet packages used in your .NET projects and solutions.  When scanning a .NET project or solution, if the `--scanner-type` is set to `auto`, it will also [scan for npm packages](#scanning-npm-projects).

### Additional Options

| Parameter | Description |
| --------- | ----------- |
| `--include-project-reference` | Include dependencies from referenced projects in the generated SBOM document. |
| `--do-not-scan-node_modules` | Do not scan the node_modules directory when scanning for npm package-lock.json files. |
| `--include-dev-dependencies` | Include npm development dependencies from the package-lock.json file in the generated SBOM document. |

### Examples

#### Example: Scanning a C# Project
```
pgutil builds scan --input="c:\path\to\project.csproj" --project-name="myProject" --version=1.2.3 --scanner-type=NuGet
```

#### Example: Scanning a .NET Solution
```
pgutil builds scan --input="c:\path\to\solution.sln" --project-name="myProject" --version=1.2.3 --scanner-type=NuGet
```

## Scanning PyPi Projects
To use pgutil to scan dependencies in PyPI applications and libraries by scanning the environment file.  When scanning a PyPI environment file, if the `--scanner-type` is set to `auto`, it will also [scan for npm packages](#scanning-npm-projects).

### Example
```bash
conda list --explicit > requirments.txt
pgutil builds scan --input="requirements.txt" --project-name="myProject" --version=1.2.3
```

## Scanning Conda Projects
To use pgutil to scan dependencies in Conda applications and libraries, you will need to create a Conda environment file.  To create a Conda environment file, you will need to run either:

```bash
conda list --explicit > requirments.txt
```

or

```bash
conda list --export > requirments.txt
```

This will list create a text file with a list of dependencies used by your Conda library or application.  Once this file has been generated, you will need to run `pgutil builds scan` passing your requirements.txt to the `--input` command.  

### Example
```bash
conda list --explicit > requirments.txt
pgutil builds scan --input="requirements.txt" --project-name="myProject" --version=1.2.3 --scanner-type=Conda
```