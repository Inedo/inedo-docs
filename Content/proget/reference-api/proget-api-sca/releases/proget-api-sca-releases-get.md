---
title: "Get Release"
order: 2
---

*Get Release* is available as both a `pgutil` command and an HTTP Request, and will get a specified project release, and return a [ReleaseInfo Object](/docs/proget/reference-api/proget-api-sca#releaseinfo-object) object describing the release.

:::(Info) (🚀 Quick Example: Getting a Release with Curl)
This example gets version `1.2.3` of a release for a project named `myProject`, authenticating with the API key `abc12345`:

````
curl -X GET -H "X-ApiKey: abc12345" "https://proget.corp.local/api/sca/releases?project=myProject&version=1.2.3"
````
:::

## Command Specification (CLI)
:::(Info) (🚧 Coming Soon 🚧)
The `sca releases get` command is coming soon. Similar to the HTTP Request, it will display a specified project release.
:::

## HTTP Request Specification
To get a release, simply `GET` to the URL with an [appropriate API Key](/docs/proget/reference-api/proget-api-sca#authentication).

```
GET /api/sca/releases?name=«projectName»&version=«versionNumber»
```

**Getting a release** requires the `project` name (e.g. `myProject`) and the `version` (e.g. `1.2.3`):

```json
GET /api/sca/releases?project=myProject&version=1.2.3
```

## HTTP Response Specification

A successful (`200`) response body will contain a [ReleaseInfo Object](/docs/proget/reference-api/proget-api-sca#releaseinfo-object) object. For example, to getting release version `1.2.3` of a project named `myProject`, the request would return this:

```json
GET /api/sca/releases?project=myProject&version=1.2.3

{
  "version":"1.2.3",
  "active":true,
  "viewReleaseUrl":"http://192.168.0.189:8624/projects/release?projectReleaseId=2", 
  "viewIssuesUrl":"http://192.168.0.189:8624/projects/release/issues?projectReleaseId=2",
  "comments":[],
  "packages":[]
}
```

| Response | Details |
| --- | --- |
| **200 (Success)** | body will contain a [ReleaseInfo](/docs/proget/reference-api/proget-api-sca#releaseinfo-object) object |
| **400 (Invalid Input)** | indicates invalid or missing properties |
| **403 (Unauthorized API Key)** | indicates a [missing, unknown, or unauthorized API Key](/docs/proget/reference-api/proget-api-sca#authentication); the body will be empty |
|  **404 (Project or Release Not Found)** | indicates that the specified project or release does not exist | 
| **500 (Server Error)** | indicates an unexpected error; the body will contain the message and stack trace, and this will also be logged |