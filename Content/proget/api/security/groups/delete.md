---
title: "Delete Group"
order: 2
---

*Delete Group* is available as both a `pgutil` command and an HTTP Request, and will delete a specified group from ProGet. Note that it's *not* considered an error to delete a group that does not exist. 

:::(Info) (🚀 Quick Example: Deleting a group with pgutil)
This example will delete a group `Developers`:

```bash
pgutil security groups delete --name=Developers
```
:::

## Command Specification (CLI)
The `security groups delete` command is used to delete a group from ProGet.

The `--name` option is always required. 

**Deleting a group** requires the group name (e.g. `Developers`):

```bash
pgutil security groups delete --name=Developers
```

## HTTP Request Specification

To delete a group, simply `DELETE` to the URL with the `group` query argument specifying the username:

```plaintext
DELETE /api/security/groups/delete?group=«groupname»
```

## HTTP Response Specification

| Response | Details |
| --- | --- |
| **200 (Success)** | the group is deleted from ProGet |
| **401 (Authentication Required)** | indicates a [missing, unknown, or unauthorized API Key](/docs/proget/api/assets#authentication) |
