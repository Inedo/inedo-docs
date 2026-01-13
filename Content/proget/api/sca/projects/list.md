---
title: "List Projects"
order: 3
---

*List Projects* is available as both a `pgutil` command and an HTTP Request, and will return an array of [ProjectInfo](https://github.com/Inedo/pgutil/blob/thousand/Inedo.ProGet/ProjectInfo.cs) objects describing all projects.

:::(Info) (🚀 Quick Example: Listing Projects with pgutil)
This example lists all existing projects:

```bash
pgutil builds projects list
```
:::

## Command Specification (CLI)
The `builds projects list` command is used to list all existing projects.

**Listing projects** requires no additional options.

Example output:
```plaintext
myProject
anotherProject (Application)
yetAnotherProject (Application)
testApplication
```
## HTTP Request Specification
To list all projects, simply `GET` to the URL with an [appropriate API Key](/docs/proget/api/sca#authentication).

```plaintext
GET /api/sca/projects
```

## HTTP Response Specification

A successful (`200`) response body will contain an array of `ProjectInfo` objects (see [ProjectInfo.cs](https://github.com/Inedo/pgutil/blob/thousand/Inedo.ProGet/ProjectInfo.cs)). A `403` response indicates a [missing, unknown, or unauthorized API Key](/docs/proget/api/security#authentication).