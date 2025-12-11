---
title: "Audit Build"
order: 4
---

*Audit Build* is available as both a `pgutil` command and an HTTP Request, and provide information about the packages in a build, including their licenses, vulnerabilities, and compliance status. The command will run an analysis of the specified build (similar to clicking the "analyze" button in the user interface) and provide build overview and packages information. It will return an [AuditPackageResults](https://github.com/Inedo/pgutil/blob/thousand/Inedo.ProGet/AuditPackageResults.cs) object describing the analysis.


:::(Info) (🚀 Quick Example: Analyzing a build with pgutil)
This example analyzes build `1.2.3` of the project `myProject`

```bash
pgutil builds audit --project=myProject --build=1.2.3
```
:::

## Command Specification (CLI)
The `builds audit` command is used to analyze a build of a project.

The `--project` and `--build` options are always required; `--brief` may be specified to display only the build overview information.

**Analyzing a build** requires the project name (e.g. `myProject`) and build number (e.g. `1.2.3`). For example:

```plaintext
$> pgutil builds audit --project=myProject --build=1.2.3

Requesting audit of packages in myProject 1.2.3...

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

When the build status is "Noncompliant (Unresolved)", this command will return a nonzero exit code.

## HTTP Request Specification
To analyze a build, simply `POST` to the URL with an [appropriate API Key](/docs/proget/api/sca#authentication)

```plaintext
POST /api/sca/audit-build?project=«projectName»&build=«buildNumber»
```

## HTTP Response Specification

| Response | Details |
| --- | --- |
| **200 (Success)** | an `BuildAnalysisResults` JSON object (see [BuildAnalysisResults.cs](https://github.com/Inedo/pgutil/blob/thousand/Inedo.ProGet/BuildAnalysisResults.cs)) |
| **400 (Invalid Input)** | indicates invalid or missing arguments; the body will provide some details as text |
| **403 (Unauthorized API Key)** | indicates a [missing, unknown, or unauthorized API Key](/docs/proget/api/sca#authentication); the body will be empty |
| **500 (Server Error)** | indicates an unexpected error; the body will contain the message and stack trace, and this will also be logged |
