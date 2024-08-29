---
title: "Scan Project"
order: 4
---

*Scan Project* is available as both a `pgutil` command and an HTTP Request, and will generate a minimal SBOM from project dependencies and uploads it to ProGet

:::(Info) (🚀 Quick Example: Scanning a project with pgutil)
This example scans version `1.2.3` of the project `myProject`

````
pgutil builds scan --input=myApplication.csproj --project-name="myProject" --version=1.2.3
````
:::

## Command Specification (CLI)
The `builds scan` command is used to scan a build of a project.

The `--input`, `--project-name` and `--version` options are always required.

**Scanning a project** requires the input (e.g. `myApplication.csproj`), project name (e.g. `myProject`) and version (e.g. `1.2.3`):

```
pgutil builds scan --input=myApplication.csproj --project-name="myProject" --version=1.2.3
```

## HTTP Request Specification
To scan a project, simply `POST` to the URL with an [appropriate API Key](/docs/proget/reference-api/proget-api-sca#authentication)

```
POST /api/sca/
```

## HTTP Response Specification

| Response | Details |
| --- | --- |
| **200 (Success)** | The scan was successful |
| **400 (Invalid Input)** | indicates invalid or missing arguments; the body will provide some details as text |
|  **403 (Unauthorized API Key)** | indicates a [missing, unknown, or unauthorized API Key](/docs/proget/reference-api/proget-api-sca#authentication); the body will be empty |
| **500 (Server Error)** | indicates an unexpected error; the body will contain the message and stack trace, and this will also be logged |