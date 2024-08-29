---
title: "Promote Build"
order: 5
---

*Promote Build* is available as both a `pgutil` command and an HTTP Request, and will promote a build of a project to a specified stage.

:::(Info) (🚀 Quick Example: Promoting a build with pgutil)
This example promotes build `1.2.3` of the project `myProject` to the production stage.

````bash
pgutil builds promote --project=myProject --build=1.2.3 --stage=Production
````
:::

## Command Specification (CLI)
The `builds promote` command is used to promote a build of a project.

The `--project`, `--build`, and `--stage` options are always required.

**Promoting a build of a project** requires the project name (e.g. `myProject`), build number (e.g. `1.2.3`), and stage to promote to (e.g. `Production`)

```bash
pgutil builds promote --project=myProject --build=1.2.3 --stage=Production
```

## HTTP Request Specification
To promote a build of a project, simply `POST` to the URL with an [appropriate API Key](/docs/proget/reference-api/proget-api-sca#authentication).

```plaintext
POST /api/sca/promote-build?project=«project»&version=«version»&stage=«stage»
```

## HTTP Response Specification

| Response | Details |
| --- | --- |
| **200 (Success)** | The build was promoted to the specified stage |
| **400 (Invalid Input)** | indicates invalid or missing properties |
| **403 (Unauthorized API Key)** | indicates a [missing, unknown, or unauthorized API Key](/docs/proget/reference-api/proget-api-sca#authentication); the body will be empty |
| **404 (Project or Build Not Found)** | indicates that the specified project or build does not exist | 
| **500 (Server Error)** | indicates an unexpected error; the body will contain the message and stack trace, and this will also be logged |