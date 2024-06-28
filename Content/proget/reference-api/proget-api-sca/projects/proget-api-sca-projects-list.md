---
title: "List Projects"
order: 3
---

*List Projects* is available as both a `pgutil` command and an HTTP Request, and will return an array of [ProjectInfo Object](/docs/proget/reference-api/proget-api-sca#projectinfo-object) objects describing all projects.

:::(Info) (🚀 Quick Example: Listing Projects with Curl)
This example lists all projects, authenticating with the API key `abc12345`:

````
curl -X GET -H "X-ApiKey: abc12345" "https://proget.corp.local/api/sca/projects"
````
:::

## Command Specification (CLI)
:::(Info) (🚧 Coming Soon 🚧)
The `sca projects list` command is coming soon. Similar to the HTTP Request, it will list all specified projects.
:::

## HTTP Request Specification
To list all projects, simply `GET` to the URL with an [appropriate API Key](/docs/proget/reference-api/proget-api-sca#authentication).

```
GET /api/sca/projects
```

## HTTP Response Specification

A successful (`200`) response body will contain an array of [ProjectInfo Object](/docs/proget/reference-api/proget-api-sca#projectinfo-object) objects. For example, to listing all projects, the request would return this:

```json
GET /api/sca/projects

[
  { 
    "id":1,
    "name":"myProject"
  },
  { 
    "id":2,
    "name":"anotherProject"
  },
  { 
    "id":3,
    "name":"yetAnotherProject"
  },
  {...}
]
```

| Response | Details |
| --- | --- |
| **200 (Success)** | body will contain an array of [ProjectInfo](/docs/proget/reference-api/proget-api-sca#projectinfo-object) objects |
| **400 (Invalid Input)** | indicates invalid or missing properties |
|  **403 (Unauthorized API Key)** | indicates a [missing, unknown, or unauthorized API Key](/docs/proget/reference-api/proget-api-sca#authentication); the body will be empty |
|  **404 (Project or Release Not Found)** | indicates that the specified project | 
| **500 (Server Error)** | indicates an unexpected error; the body will contain the message and stack trace, and this will also be logged |