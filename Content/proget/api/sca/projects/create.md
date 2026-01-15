---
title: "Create/Update Project"
order: 1
---

*Create/Update Project* is available as both a `pgutil` command and an HTTP Request, and will create a new project or update properties on an existing project.

:::(Info) (🚀 Quick Example: Creating a new project with pgutil)
This example creates a project named `myProject:

```bash
pgutil builds projects create --project=myProject
```
:::

## Command Specification (CLI)
The `builds projects create` command is used to create a new project or update an existing project.

The `--project` option is always required. The  `--type` and `--url` options are optional. They can be included in the initial creation of a project, or to update an existing project

**Creating a project** requires the project name (e.g. `myProject`)
```bash
pgutil builds projects create --project=myProject
```

**Updating the URL of a project** requires the project name (e.g. `myApplication`) and the URL (e.g. `proget.corp.local`)

```bash
pgutil builds projects create --project=myApplication --url=proget.corp.local
```

## HTTP Request Specification
To create or update a project, simply `POST` to the URL with an [appropriate API Key](/docs/proget/api/sca#authentication) and a `ProjectInfo` object (see [ProjectInfo.cs](https://github.com/Inedo/pgutil/blob/thousand/Inedo.ProGet/ProjectInfo.cs)) as the request body.

```plaintext
POST /api/sca/projects
```

## HTTP Response Specification
A successful `200` response body will contain a `ProjectInfo` object. A `403` response indicates a [missing, unknown, or unauthorized API Key](https://docs.inedo.com/docs/proget/api/licenses#authentication).