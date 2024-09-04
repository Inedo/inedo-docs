---
title: "Get Build"
order: 2
---

*Get Build* is available as both a `pgutil` command and an HTTP Request, and will get a specified project build, and return a [BuildInfo Object](/docs/proget/reference-api/proget-api-sca#buildinfo-object) object describing the build.

:::(Info) (🚀 Quick Example: Getting information of a build with pgutil)
This example gets information on build `1.2.3` of the project `myProject`

```bash
pgutil builds info --build=1.2.3 --project=myProject 
```
:::

## Command Specification (CLI)
The `builds info` command is used to get information on a build of a project.

The `--project` and `--build` options are always required.

**Getting information on a build** requires the project name (e.g. `myProject`) and the build number (e.g `1.2.3`)
```bash
pgutil builds info --build=1.2.3 --project=myProject 
```

Example output:

```plaintext
Build 1.2.3
Packages:
  pkg:nuget/CsvHelper@30.0.1
  pkg:nuget/Newtonsoft.Json@12.0.3
  pkg:nuget/Polly@7.2.3
Comments:
1 - This is a comment
2 - This is another comment.
```


## HTTP Request Specification
To get a build, simply `GET` to the URL with an [appropriate API Key](/docs/proget/reference-api/proget-api-sca#authentication).

```plaintext
GET /api/sca/releases?name=«projectName»&version=«versionNumber»
```

## HTTP Response Specification

A successful (`200`) response body will contain a [BuildInfo Object](/docs/proget/reference-api/proget-api-sca#buildinfo-object) object. For example, to getting build version `1.2.3` of a project named `myProject`, the request would return this:

```json
GET /api/sca/releases?project=myProject&version=1.2.3

{
  "version": "1.2.3",
  "active": true,
  "viewBuildUrl": "http://proget.corp.local/projects2/builds/build?buildId=3",
  "comments": [],
  "packages": [
    {
      "purl": "pkg:nuget/CsvHelper@30.0.1",
      "licenses": [
        "Apache-2.0",
        "MS-PL"
      ],
      "compliance": {
        "result": "Compliant",
        "date": "2024-07-11T08:53:41.82Z"
      },
      "vulnerabilities": []
    },
    {
      "purl": "pkg:nuget/Newtonsoft.Json@12.0.3",
      "licenses": [
        "MIT"
      ],
      "compliance": {
        "result": "Noncompliant",
        "detail": " because of Vulnerability (PGV-2245804).",
        "date": "2024-07-11T08:53:41.827Z"
      },
      "vulnerabilities": [
        {
          "id": "PGV-2245804",
          "title": "Improper Handling of Exceptional Conditions in Newtonsoft.Json",
          "description": "Description in markdown,
          "score": 7.5,
          "assessment": "Blocked"
        },
        {
          "id": "PGV-2400707",
          "title": "Duplicate Advisory: Improper Handling of Exceptional Conditions in Newtonsoft.Json",
          "description": "Description in markdown"
        }
      ]
    }
  ]
```

| Response | Details |
| --- | --- |
| **200 (Success)** | body will contain a [BuildInfo](/docs/proget/reference-api/proget-api-sca#buildinfo-object) object |
| **400 (Invalid Input)** | indicates invalid or missing properties |
| **403 (Unauthorized API Key)** | indicates a [missing, unknown, or unauthorized API Key](/docs/proget/reference-api/proget-api-sca#authentication); the body will be empty |
| **404 (Project Not Found)** | indicates that the specified project does not exist | 
| **500 (Server Error)** | indicates an unexpected error; the body will contain the message and stack trace, and this will also be logged |