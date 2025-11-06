---
title: "Add Permission"
order: 1
---

*Add Permission* is available as both a `pgutil` command and an HTTP Request. It will either grant or deny a task attribute permission to a user or group, and return a [SecurityPermission](/docs/proget/api/security#securitypermission-object) object describing the permission configured.

:::(Info) (🚀 Quick Example: Adding a permission with pgutil)
This example adds a permission `"Basic Feed Access"` with the attributes `Feeds_ViewFeed`, `Feeds_DownloadPackage` and `Feeds_PullPackage`:

```bash
pgutil security tasks create --name="Basic Feed Access" --attributes=Feeds_ViewFeed,Feeds_DownloadPackage,Feeds_PullPackage --description="Allows basic feed access"
```
:::

## Command Specification (CLI)
The `security tasks create` command is used to create a new task.

The `--name` and `--attributes` options are always required.

Note: The list of existing attributes can be quickly returned using the [`pgutil attributes list`](/docs/proget/api/security/tasks/list-attributes) command. 

**Creating a task** requires the the task name (e.g. `"Basic Feed Access"`), attributes (e.g. `Feeds_ViewFeed`, `Feeds_DownloadPackage` and `Feeds_PullPackage`):

```bash
pgutil security tasks create --name="Basic Feed Access" --attributes=Feeds_ViewFeed,Feeds_DownloadPackage,Feeds_PullPackage
```

## HTTP Request Specification
To create a new task account, simply `POST` to the URL with an [appropriate API Key](/docs/proget/api/security#authentication) and a [SecurityTask](/docs/proget/api/security#securitytask-object) object as the request body.

```plaintext
POST /api/security/tasks/add
```

## HTTP Response Specification

A successful (`200`) response body will contain a [Securitytask](/docs/proget/api/security#securitytask-object) object. For example, to creating a new task with the name `"Basic Feed Access"` and the attributes `Feeds_ViewFeed`, `Feeds_DownloadPackage` and `Feeds_PullPackage`, the request would return this:

```json
POST /api/security/tasks/add

{
  "name": "Basic Access",
  "attributes": 
    [
    "Feeds_ViewFeed",
    "Feeds_DownloadPackage",
    "Feeds_PullPackage"
    ]
}
```

| Response | Details |
| --- | --- |
| **200 (Success)** | body will contain a [SecurityTask](/docs/proget/api/security#securitytask-object) object |
| **400 (Invalid Input)** | indicates invalid or missing properties on the [SecurityTask](/docs/proget/api/security#securitytask-object) object; the body will provide some details as text |
| **403 (Unauthorized API Key)** | indicates a [missing, unknown, or unauthorized API Key](/docs/proget/api/sca#authentication); the body will be empty |
| **500 (Server Error)** | indicates an unexpected error; the body will contain the message and stack trace, and this will also be logged |