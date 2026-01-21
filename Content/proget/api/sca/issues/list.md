---
title: "List Issues"
order: 1
---

*List Issues* is available as both a `pgutil` command and an HTTP Request, and will list all issues in a project release.

:::(Info) (🚀 Quick Example: Listing Issues with pgutil)
This example lists all issues in build version `1.2.3` of a project `myProject`:

```bash
pgutil builds issues list --project=myProject --build=1.2.3
```
:::

## Command Specification (CLI)
The `builds issues list` command is used to list all existing issues in a project's build.

The `--project` and `--build` options are always required. 

:::(info)(📄 Note: pgutil Sources)
Source options must also be specified unless you have the "Default" source configured. See [Working with Sources](/docs/proget/api/pgutil#sources) to learn more.
:::

**Listing issues** requires the project (e.g. `myProject`) and the build version (e.g. `1.2.3`)

```bash
pgutil builds issues list --project=myProject --build=1.2.3
```

Example output:

```plaintext
#1 -  because of Vulnerability (PGV-2245804), Package Status (Unlisted, Deprecated) is unknown, No license detected.
```

## HTTP Request Specification
To list all issues of a release, simply `GET` to the URL with an [appropriate API Key](/docs/proget/api/sca#authentication), the `project` name and version:

```plaintext
GET /api/sca/issues?project=«projectName»&version=«releaseVersion»
```

## HTTP Response Specification
An array of `BuildIssue` (see [BuildIssue.cs](https://github.com/Inedo/pgutil/blob/thousand/Inedo.ProGet/BuildIssue.cs)) objects will be returned on a successful `200` response. A `403` response indicates a [missing, unknown, or unauthorized API Key](/docs/proget/api/sca#authentication). Note, that if there are no issues in a release, an empty array is returned.
