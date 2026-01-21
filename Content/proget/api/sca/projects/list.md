---
title: "List Projects"
order: 3
---

*List Projects* is available as both a `pgutil` command and an HTTP Request, and will return a list of all existing projects.

:::(Info) (🚀 Quick Example: Listing Projects with pgutil)
This example lists all existing projects:

```bash
pgutil builds projects list
```
:::

## Command Specification (CLI)
The `builds projects list` command is used to list all existing projects.

:::(info)(📄 Note: pgutil Sources)
Source options must also be specified unless you have the "Default" source configured. See [Working with Sources](/docs/proget/api/pgutil#sources) to learn more.
:::

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
A successful `200` response body will contain an array of [ProjectInfo.cs](https://github.com/Inedo/pgutil/blob/thousand/Inedo.ProGet/ProjectInfo.cs) objects. A `403` response indicates a [missing, unknown, or unauthorized API Key](/docs/proget/api/sca#authentication).
