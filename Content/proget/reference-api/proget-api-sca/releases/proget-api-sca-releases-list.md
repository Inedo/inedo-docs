---
title: "List Releases"
order: 3
---

*List Releases* is available as both a `pgutil` command and an HTTP Request, and will return an array of [ReleaseInfo Object](/docs/proget/reference-api/proget-api-sca#releaseinfo-object) objects describing the releases of a specified project.

:::(Info) (🚀 Quick Example: Listing Releases of a Project with Curl)
This example lists releases of a project named `myProject`, authenticating with the API key `abc12345`:

````
curl -X GET -H "X-ApiKey: abc12345" "https://proget.corp.local/api/sca/releases?project=myProject"
````
:::

## Command Specification (CLI)
:::(Info) (🚧 Coming Soon 🚧)
The `sca releases list` command is coming soon. Similar to the HTTP Request, it will list all specified project releases.
:::

## HTTP Request Specification
To list all releases of a project, simply `GET` to the URL with an [appropriate API Key](/docs/proget/reference-api/proget-api-sca#authentication).

```
GET /api/sca/releases?name=«projectName»
```

**Listing releases** requires the `project` name (e.g. `myProject`):

```json
GET /api/sca/releases?project=myProject
```

## HTTP Response Specification

A successful (`200`) response body will contain an array of [ReleaseInfo Object](/docs/proget/reference-api/proget-api-sca#releaseinfo-object) objects. For example, to listing release versions of a project named `myProject`, the request would return this:

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
| **200 (Success)** | body will contain an array of [ReleaseInfo](/docs/proget/reference-api/proget-api-sca#releaseinfo-object) objects |
| **400 (Invalid Input)** | indicates invalid or missing properties |
|  **403 (Unauthorized API Key)** | indicates a [missing, unknown, or unauthorized API Key](/docs/proget/reference-api/proget-api-sca#authentication); the body will be empty |
|  **404 (Project or Release Not Found)** | indicates that the specified project | 
| **500 (Server Error)** | indicates an unexpected error; the body will contain the message and stack trace, and this will also be logged |