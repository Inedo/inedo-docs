---
title: "List Builds"
order: 3
---

*List Builds* is available as both a `pgutil` command and an HTTP Request, and will list all existing builds within a specified project.

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
To list the builds of a project, simply `GET` to the URL with the `project name` and an [appropriate API Key](/docs/proget/api/sca#authentication).

```plaintext
GET /api/sca/releases?project=«projectName»
```

## HTTP Response Specification

A successful `200` response body will contain an array of [BuildInfo.cs](https://github.com/Inedo/pgutil/blob/thousand/Inedo.ProGet/BuildInfo.cs) objects. A `403` response indicates a [missing, unknown, or unauthorized API Key](/docs/proget/api/sca#authentication).
