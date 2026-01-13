---
title: "Promote Build"
order: 5
---

*Promote Build* is available as both a `pgutil` command and an HTTP Request, and will promote a build of a project to a specified stage.

:::(Info) (🚀 Quick Example: Promoting a build with pgutil)
This example promotes build `1.2.3` of the project `myProject` to the production stage.

```bash
pgutil builds promote --project=myProject --build=1.2.3 --stage=Production
```
:::

## Command Specification (CLI)
The `builds promote` command is used to promote a build of a project.

The `--project`, `--build`, and `--stage` options are always required.

**Promoting a build of a project** requires the project name (e.g. `myProject`), build number (e.g. `1.2.3`), and stage to promote to (e.g. `Production`)

```bash
pgutil builds promote --project=myProject --build=1.2.3 --stage=Production
```

## HTTP Request Specification
To promote a build, simply `POST` to the URL with both the name of the `project` and the `build` number, and an [appropriate API Key](/docs/proget/api/sca#authentication):

```plaintext
POST /api/sca/promote-build?project=«project»&build=«build»&stage=«stage»
```

## HTTP Response Specification
A successful `200` response indicates that the build was promoted. A `403` response indicates a [missing, unknown, or unauthorized API Key](/docs/proget/api/security#authentication).

:::(internal) (⚠ Not Working)
There is an object returned but it does not match anything in GitHub exactly.
:::