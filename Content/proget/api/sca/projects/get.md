---
title: "Get Project"
order: 2
---

*Get Project* is available as an HTTP Request, and will return information on a specified project.

:::(Info) (🚀 Quick Example: Getting a Project with Curl)
This example gets a project `myProject`, authenticating with the API key `abc12345`:

```bash
curl -X GET -H "X-ApiKey: abc12345" "https://proget.corp.local/api/sca/projects?project=myProject"
```
:::

## HTTP Request Specification
To return a specified project, simply `GET` to the URL with the `project name` and an [appropiate API Key](/docs/proget/api/sca#authentication).

```plaintext
GET /api/sca/projects?name=«projectName»
```

## HTTP Response Specification
A successful `200` response body will contain a `ProjectInfo` object (see [ProjectInfo.cs](https://github.com/Inedo/pgutil/blob/thousand/Inedo.ProGet/ProjectInfo.cs)). A `403` response indicates a [missing, unknown, or unauthorized API Key](/docs/proget/api/sca#authentication).
