---
title: "Get Build"
order: 2
---

*Get Build* is available as both a `pgutil` command and an HTTP Request, and will return info on a single build.

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

To return a specified build, simply `GET` to the URL with the `project name`, `build version`, and an [appropriate API Key](/docs/proget/api/sca#authentication).

```plaintext
GET /api/sca/releases?name=«projectName»&version=«versionNumber»
```

## HTTP Response Specification

A [BuildInfo.cs](https://github.com/Inedo/pgutil/blob/thousand/Inedo.ProGet/BuildInfo.cs) object will be returned on a successful `200` response. A `403` response indicates a [missing, unknown, or unauthorized API Key](/docs/proget/api/sca#authentication).
