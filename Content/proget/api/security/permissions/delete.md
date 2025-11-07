---
title: "Delete Permission"
order: 3
---

*Delete Permission* is available as both a `pgutil` command and an HTTP Request, and will delete a granted or denied permission from a User or Group.

:::(Info) (🚀 Quick Example: Deleting a permission with pgutil)
This example will delete the configured permission with an id `3`:
```bash
pgutil security permissions delete --id=3
```
:::

## Command Specification (CLI)
The `security permissions delete` command is used to delete a granted or denied permission from a User or Group.

The `--id` options is always required. 

:::(Info) (💡 Permission IDs)
This command requires the ID of the configured permission. This can be found by running the `pgutil` command [`security permissions list`](/docs/proget/api/security/permissions/list) which will return all existing permissions, for example: 

```yaml
1: allow View & Download Packages globally for user Anonymous
2: allow Administer globally for group Administrators
3: allow Administer globally for user Anonymous
...
```
:::

**Deleting a permission** requires the permission id (e.g. `3`):

```bash
pgutil security permission delete --id=3
```

## HTTP Request Specification

To delete a permission, simply `DELETE` to the URL with the `permissionId` query argument specifying the permission id:

```plaintext
DELETE /api/security/permissions/delete?permissionId=«permission-id»
```

## HTTP Response Specification

| Response | Details |
| --- | --- |
| **200 (Success)** | the permission is deleted from ProGet |
| **400 (Invalid Input)** | indicates invalid or missing properties; the body will provide some details as text |
| **401 (Authentication Required)** | indicates a [missing, unknown, or unauthorized API Key](/docs/proget/api/assets#authentication) |
| **500 (Server Error)** | indicates an unexpected error; the body will contain the message and stack trace, and this will also be logged |