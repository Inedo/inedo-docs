---
title: "Delete Permission"
order: 3
---

*Delete Permission* is available as both a `pgutil` command and an HTTP Request, and will delete a granted or denied permission from a user or group.

:::(Info) (🚀 Quick Example: Deleting a permission with pgutil)
This example will delete the permission `` from the group ``:

```bash
pgutil security permissions delete
```
:::

## Command Specification (CLI)
The `security permissions delete` command is used to delete a granted or denied permission from a user or group.

The `--name` options is always required. 

**Deleting a permission** requires the user name (e.g. `"Basic Access"`):

```bash
pgutil security permission delete --name="Basic Access"
```

## HTTP Request Specification

To delete a permission, simply `DELETE` to the URL with the `task` query argument specifying the task:

```plaintext
DELETE /api/security/tasks/delete?task=«taskname»
```

## HTTP Response Specification

| Response | Details |
| --- | --- |
| **200 (Success)** | the user is deleted from ProGet |
| **400 (Invalid Input)** | indicates invalid or missing properties; the body will provide some details as text |
| **401 (Authentication Required)** | indicates a [missing, unknown, or unauthorized API Key](/docs/proget/api/assets#authentication) |
