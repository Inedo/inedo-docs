---
title: "Create/Update Project"
order: 1
---

*Create/Update Project* is available as both a `pgutil` command and an HTTP Request, and will create a new project or update properties on an existing project, and return a [ProjectInfo Object](/docs/proget/reference-api/proget-api-sca#projectinfo-object) object describing the new or updated project.

Note: this API endpoint functions by creating a new project when the one specified in the API request does not exist, else it updates an existing project.

:::(Info) (🚀 Quick Example: Creating a new project with pgutil)
This example creates a project named `myProject:

````
pgutil builds projects create --project=myProject
````
:::

## Command Specification (CLI)
The `builds projects create` command is used to create a new project or update an existing project.

The `--project` option is always required. The  `--type` and `--url` options are optional. They can be included in the initial creation of a project, or to update an existing project

**Creating a project** requires the project name (e.g. `myProject`)
```
pgutil builds projects create --project=myProject
```

**Updating the URL of a project** requires the project name (e.g. `myApplication`) and the URL (e.g. `proget.corp.local`)

```
pgutil builds projects create --project=myApplication --url=proget.corp.local
```

## HTTP Request Specification
To create or update a project, simply `POST` to the URL with an [appropriate API Key](/docs/proget/reference-api/proget-api-sca#authentication) and a [ProjectInfo Object](/docs/proget/reference-api/proget-api-sca#projectinfo-object) object as the request body.

```
POST /api/sca/projects
```

**Creating a project** with a `ProjectInfo` body requires the project `name` (e.g. `myProject`):

```json
POST /api/sca/projects

{
  "name":"myProject",
}
```

**Updating a project** with a `ProjectInfo` body requires the `project` name (e.g. `myProject`), and the property to update (e.g. `description`):

```json
POST /api/sca/projects

{
  "name":"myProject",
  "description":"This is an example"
}
```

## HTTP Response Specification
A successful (`200`) response body will contain a [ProjectInfo Object](/docs/proget/reference-api/proget-api-sca#projectinfo-object) object. For example, to creating a new project `myProject`, the request would return this:

```json
POST /api/sca/projects
{ 
  "id":1,
  "name":"myProject"
}
```

| Response | Details |
| --- | --- |
| **200 (Success)** | body will contain a [ProjectInfo](/docs/proget/reference-api/proget-api-sca#projectinfo-object) object |
| **400 (Invalid Input)** | indicates invalid or missing properties on the [ProjectInfo](/docs/proget/reference-api/proget-api-sca#projectinfo-object) object; the body will provide some details as text |
|  **403 (Unauthorized API Key)** | indicates a [missing, unknown, or unauthorized API Key](/docs/proget/reference-api/proget-api-sca#authentication); the body will be empty |
| **500 (Server Error)** | indicates an unexpected error; the body will contain the message and stack trace, and this will also be logged |
