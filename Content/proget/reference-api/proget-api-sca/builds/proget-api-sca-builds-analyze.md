---
title: "Analyze Build"
order: 4
---

*Analyze Build* is available as both a `pgutil` command and an HTTP Request, and will run an analysis of a single build, similar to clicking the "analyze" button in the user interface, on the build overview page.

:::(Info) (🚀 Quick Example: Analyzing a build with pgutil)
This example analyzes build `1.2.3` of the project `myProject`

````
pgutil builds audit --project=myProject --build=1.2.3
````
:::

## Command Specification (CLI)
The `builds audit` command is used to analyze a build of a project.

The `--project` and `--build` options are always required.

**Analyzing a build** requires the project name (e.g. `myProject`) and build number (e.g. `1.2.3`):

```
pgutil builds audit --project=myProject --build=1.2.3
```

Example output (Project analyzed):
```
Auditing myProject 1.2.3...
Analyzed: 7/21/2024 5:49:38 PM
Status: Active
```

Example output (Project not analyzed):
```
Auditing myProject 1.2.3...
ProGet reported that the build was not analyzed.
```

## HTTP Request Specification
To analyze a build, simply `POST` to the URL with an [appropriate API Key](/docs/proget/reference-api/proget-api-sca#authentication)

```
POST /api/sca/analyze-release?project=«projectName»&version=«releaseVersion»
```

**Analyzing a build** requires the `project` name (e.g. `myProject` ) and build `version` (e.g. `1.2.3`) properties.

```
POST /api/sca/analyze-release?project=myProject»&version=1.2.3
```

## HTTP Response Specification

| Response | Details |
| --- | --- |
| **200 (Success)** | analysis was successful |
| **400 (Invalid Input)** | indicates invalid or missing arguments; the body will provide some details as text |
|  **403 (Unauthorized API Key)** | indicates a [missing, unknown, or unauthorized API Key](/docs/proget/reference-api/proget-api-sca#authentication); the body will be empty |
| **500 (Server Error)** | indicates an unexpected error; the body will contain the message and stack trace, and this will also be logged |