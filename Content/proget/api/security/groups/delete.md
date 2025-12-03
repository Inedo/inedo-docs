---
title: "Delete Group"
order: 2
---

*Delete Group* is available as both a `pgutil` command and an HTTP Request, and will delete a specified Group from ProGet. Note that it's *not* considered an error to delete a Group that does not exist. 

:::(Info) (🚀 Quick Example: Deleting a Group with pgutil)
This example will delete a Group `Developers`:

```bash
pgutil security groups delete --name=Developers
```
:::

## Command Specification (CLI)
The `security groups delete` command is used to delete a Group from ProGet.

The `--name` option is always required. 

**Deleting a Group** requires the Group name (e.g. `Developers`):

```bash
pgutil security groups delete --name=Developers
```

## HTTP Request Specification

To delete a Group, simply `DELETE` to the following URL with the `group` query argument specifying the username:

```plaintext
DELETE /api/security/groups/delete?group=«groupname»
```

## HTTP Response Specification

A `200` response will indicate the Group has been deleted from ProGet. a `403` response indicates a [missing, unknown, or unauthorized API Key](/docs/proget/api/security#authentication).