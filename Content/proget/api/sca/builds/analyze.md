---
title: "Audit Build"
order: 4
---

*Audit Build* is available as both a `pgutil` command and an HTTP Request, will provide information about the packages in a given build, running an analysis of the specified build (similar to clicking the "analyze" button in the user interface) and providing a build overview and package information. 

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
To analyze a build, simply `POST` to the URL with the `project name`, `build version`, and an [appropiate API Key](/docs/proget/api/sca#authentication).

```plaintext
POST /api/sca/audit-build?project=«projectName»&build=«buildNumber»
```

## HTTP Response Specification
A `BuildAnalysisResults` object (see [BuildAnalysisResults.cs](https://github.com/Inedo/pgutil/blob/thousand/Inedo.ProGet/BuildAnalysisResults.cs)) will be returned on a successful `200` response. A `403` response indicates a [missing, unknown, or unauthorized API Key](/docs/proget/api/sca#authentication).
