---
title: "Remove Permission"
order: 3
---

*Remove Permission* is available as both a `pgutil` command and an HTTP Request, and will remove a granted or denied Permission from a User or Group.

:::(Info) (🚀 Quick Example: Deleting a Permission with pgutil)
This example will remove the configured Permission with an id `3`:
```bash
pgutil security permissions remove --id=3
```
:::

## Command Specification (CLI)
The `security permissions remove` command is used to remove a granted or denied Permission from a User or Group.

The `--id` options is always required. 

:::(Info) (💡 Permission IDs)
This command requires the ID of the configured Permission. This can be found by running the `pgutil` command [`security permissions list`](/docs/proget/api/security/permissions/list) which will return all existing permissions, for example: 

```yaml
1: allow View & Download Packages globally for user Anonymous
2: allow Administer globally for group Administrators
3: allow Administer globally for user Anonymous
...
```
:::

:::(info)(📄 Note: pgutil Sources)
Source options must also be specified unless you have the "Default" source configured. See [Working with Sources](/docs/proget/api/pgutil#sources) to learn more.
:::

**Removing a permission** requires the Permission id (e.g. `3`):

```bash
pgutil security permission remove --id=3
```

## HTTP Request Specification

To remove a Permission, simply `DELETE` to the following URL with the `permissionId` query argument specifying the Permission id:

```plaintext
DELETE /api/security/permissions/delete?permissionId=«permission-id»
```

## HTTP Response Specification

A `200` response will indicate the Permission has been removed from ProGet. A `403` response indicates a [missing, unknown, or unauthorized API Key](/docs/proget/api/security#authentication).