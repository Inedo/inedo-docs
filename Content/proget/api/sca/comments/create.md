---
title: "Create/Update Comment"
order: 1
---

*Create/Update Comment* is available as both a `pgutil` command and an HTTP Request, and will create a new comment or update properties on an existing comment, and return a [BuildComment](/docs/proget/api/sca#commentinfo-object) object describing the new or updated comment.

:::(Info) (🚀 Quick Example: Creating a New Comment with pgutil)
This example creates a comment (`Checked for errors on 01/01`) in version `1.2.3` of a project named `myProject`:

```bash
pgutil builds comments create --project=myProject --build=1.2.3 --comment="Checked for errors on 01/01"
```
:::

## Command Specification (CLI)
The `builds comments create` command is used to create a comment in a project's build.

The `--project`, `--build` and `--comment` options are always required.

:::(info)(📄 Note: pgutil Sources)
Source options must also be specified unless you have the "Default" source configured. See [Working with Sources](/docs/proget/api/pgutil#sources) to learn more.
:::

**Creating a comment** requires the project (e.g. `myProject`), the build version number (e.g. `1.2.3`), and the comment (e.g. `This is a comment`)

```bash
pgutil builds comments create --project=myProject --build=1.2.3 --comment="This is a comment"
```

## HTTP Request Specification
To create or update a comment, simply `POST` to the URL with an [appropriate API Key](/docs/proget/api/sca#authentication) and a `BuildComment` (see [BuildComment.cs](https://github.com/Inedo/pgutil/blob/thousand/Inedo.ProGet/BuildComment.cs)) object as the request body.

```plaintext
POST /api/sca/comments
```

## HTTP Response Specification

A successful `200` response will indicate the comment was successfully entered. A `403` response indicates a [missing, unknown, or unauthorized API Key](/docs/proget/api/sca#authentication).