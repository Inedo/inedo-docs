---
title: "Create Task"
order: 1
---

*Create Task* is available as both a `pgutil` command and an HTTP Request, and will create a new Task in ProGet.

:::(Info) (🚀 Quick Example: Creating a new Task with pgutil)
This example creates the Task `"Basic Feed Access"` with the attributes `Feeds_ViewFeed`, `Feeds_DownloadPackage` and `Feeds_PullPackage`:

```bash
pgutil security tasks create --name="Basic Feed Access" --attributes=Feeds_ViewFeed,Feeds_DownloadPackage,Feeds_PullPackage --description="Allows basic feed access"
```
:::

## Command Specification (CLI)
The `security tasks create` command is used to create a new Task.

The `--name` and `--attributes` options are always required.

Note: The list of existing attributes can be quickly returned using the [`pgutil attributes list`](/docs/proget/api/security/tasks/list-attributes) command. 

:::(info)(📄 Note: pgutil Sources)
Source options must also be specified unless you have the "Default" source configured. See [Working with Sources](/docs/proget/api/pgutil#sources) to learn more.
:::

**Creating a task** requires the the Task name (e.g. `"Basic Feed Access"`), attributes (e.g. `Feeds_ViewFeed`, `Feeds_DownloadPackage` and `Feeds_PullPackage`):

```bash
pgutil security tasks create --name="Basic Feed Access" --attributes=Feeds_ViewFeed,Feeds_DownloadPackage,Feeds_PullPackage
```

## HTTP Request Specification
To create a new Task, simply `POST` to the following URL with an [appropriate API Key](/docs/proget/api/security#authentication) and a `SecurityTask` (see [SecurityTask.cs](https://github.com/Inedo/pgutil/blob/thousand/Inedo.ProGet/SecurityTask.cs)) object as the request body.

```plaintext
POST /api/security/tasks/add
```

## HTTP Response Specification

A `SecurityTask` object will be returned on a successful `200` response and indicates the task has been created. A `403` response indicates a [missing, unknown, or unauthorized API Key](/docs/proget/api/security#authentication).
