---
title: "Get Project"
order: 2
---

*Get Project* is available as an HTTP Request, and will return information on a specified project, and return a [ProjectInfo Object](/docs/proget/reference-api/proget-api-sca#projectinfo-object) object describing the project.

:::(Info) (🚀 Quick Example: Getting a Project with Curl)
This example gets a project `myProject`, authenticating with the API key `abc12345`:

```bash
curl -X GET -H "X-ApiKey: abc12345" "https://proget.corp.local/api/sca/projects?project=myProject"
```
:::

## HTTP Request Specification
To get a project, simply `GET` to the URL with an [appropriate API Key](/docs/proget/reference-api/proget-api-sca#authentication).

```plaintext
GET /api/sca/projects?name=«projectName»
```

**Getting a project** requires the project `name` (e.g. `myProject`):

```json
GET /api/sca/projects?project=myProject
```

## HTTP Response Specification

A successful (`200`) response body will contain a [ProjectInfo Object](/docs/proget/reference-api/proget-api-sca#projectinfo-object) object. For example, to getting project `myProject`, the request would return this:

```json
GET /api/sca/projects?project=myProject

{ 
  "id":1,
  "name":"myProject"
}
```

| Response | Details |
| --- | --- |
| **200 (Success)** | body will contain a [ProjectInfo](/docs/proget/reference-api/proget-api-sca#projectinfo-object) object |
| **400 (Invalid Input)** | indicates invalid or missing properties |
| **403 (Unauthorized API Key)** | indicates a [missing, unknown, or unauthorized API Key](/docs/proget/reference-api/proget-api-sca#authentication); the body will be empty |
| **404 (Project Not Found)** | indicates that the specified project does not exist | 
| **500 (Server Error)** | indicates an unexpected error; the body will contain the message and stack trace, and this will also be logged |