---
title: "List Issues"
order: 1
---

*List Issues* is available as both a `pgutil` command and an HTTP Request, and will return an array of [IssueInfo Object](/docs/proget/reference-api/proget-api-sca#issueinfo-object) objects describing all issues in a project release.

Note, that if there are no issues in a release, an empty array is returned.

:::(Info) (🚀 Quick Example: Listing Issues with Curl)
This example lists all issues in release version `1.2.3` of a project `myProject`, authenticating with the API key `abc12345`:

````
curl -X GET -H "X-ApiKey: abc12345" "https://proget.corp.local/api/sca/issues?project=myProject&version=1.2.3"
````
:::

## Command Specification (CLI)
:::(Info) (🚧 Coming Soon 🚧)
The `sca issues list` command is coming soon. Similar to the HTTP Request, it will list all issues in a specified project release.
:::

## HTTP Request Specification
To list all issues of a release, simply `GET` to the URL with an [appropriate API Key](/docs/proget/reference-api/proget-api-sca#authentication).

```
GET /api/sca/issues?project=«projectName»&version=«releaseVersion»
```

**Listing issues** requires the `project` name (e.g. `myProject`) and version (e.g. `1.0.0`):

```json
GET /api/sca/issues?project=myProject&version=1.2.3
```

## HTTP Response Specification

A successful (`200`) response body will contain an array of [IssueInfo Object](/docs/proget/reference-api/proget-api-sca#issueinfo-object) objects. For example, to listing all issues in release version `1.2.3` of a project `myProject`, the request would return this:

```json
GET /api/sca/issues?project=myProject&version=1.2.3

[
  {
    "number":1,
    "created":"2024-02-09T07:50:09.52Z",
    "type":"L",
    "purl":"pkg:nuget/Newtonsoft.Json@5.0.8"
  },
  {
    "number": 2, 
    "created": "2024-01-23T12:00:00Z", 
    "type": "V", 
    "purl": "pkg:myGroup/myPackage@v1.2.3", 
    "vulnerability": "12345"
  }
  {...}
]
```

| Response | Details |
| --- | --- |
| **200 (Success)** | body will contain an array of [IssueInfo](/docs/proget/reference-api/proget-api-sca#issueinfo-object) objects |
| **400 (Invalid Input)** | indicates invalid or missing properties |
|  **403 (Unauthorized API Key)** | indicates a [missing, unknown, or unauthorized API Key](/docs/proget/reference-api/proget-api-sca#authentication); the body will be empty |
|  **404 (Project or Release Not Found)** | indicates that the specified project | 
| **500 (Server Error)** | indicates an unexpected error; the body will contain the message and stack trace, and this will also be logged |