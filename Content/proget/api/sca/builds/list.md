---
title: "List Builds"
order: 3
---

*List Builds* is available as both a `pgutil` command and an HTTP Request, and will return an array of [BuildInfo Object](/docs/proget/api/sca#buildinfo-object) objects describing the builds of a specified project.

:::(Info) (🚀 Quick Example: Listing build of a project with pgutil)
This example lists all builds of the project `myProject`

```bash
pgutil builds list --project=myProject 
```
:::

## Command Specification (CLI)
The `builds list` command is used to list all builds of a project.

The `--project` option is always required.

**Listing builds of a project** requires the project name (e.g. `myProject`)

```bash
pgutil builds list --project=myProject 
```

Example output:

```plaintext
1.0.0
1.1.0
1.2.3
```

## HTTP Request Specification
To list all builds of a project, simply `GET` to the URL with an [appropriate API Key](/docs/proget/api/sca#authentication).

```plaintext
GET /api/sca/releases?name=«projectName»
```

## HTTP Response Specification

A successful (`200`) response body will contain an array of [BuildInfo Object](/docs/proget/api/sca#buildinfo-object) objects. For example, to listing release versions of a project named `myProject`, the request would return this:

```json
GET /api/sca/releases?project=myProject

[
  {
    "version":"1.0.0",
    "active":true,
    "viewReleaseUrl":"https://proget.corp.local/projects/release?projectReleaseId=1",
    "viewIssuesUrl":"https://proget.corp.local/projects/release/issues?projectReleaseId=1"
  },
  {
    "version":"1.1.0",
    "active":true,
    "viewReleaseUrl":"https://proget.corp.local/projects/release?projectReleaseId=2",
    "viewIssuesUrl":"https://proget.corp.local/projects/release/issues?projectReleaseId=2"
  },
  {
    "version":"1.2.3",
    "active":true,
    "viewReleaseUrl":"https://proget.corp.local/projects/release?projectReleaseId=3",
    "viewIssuesUrl":"https://proget.corp.local/projects/release/issues?projectReleaseId=3"
  },
  {...}
]
```

| Response | Details |
| --- | --- |
| **200 (Success)** | body will contain an array of [BuildInfo](/docs/proget/api/sca#buildinfo-object) objects |
| **400 (Invalid Input)** | indicates invalid or missing properties |
| **403 (Unauthorized API Key)** | indicates a [missing, unknown, or unauthorized API Key](/docs/proget/api/sca#authentication); the body will be empty |
| **404 (Project Not Found)** | indicates that the specified project does not exist | 
| **500 (Server Error)** | indicates an unexpected error; the body will contain the message and stack trace, and this will also be logged |