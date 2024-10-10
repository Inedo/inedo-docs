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