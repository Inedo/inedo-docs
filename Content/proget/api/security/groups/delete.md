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

To delete a Group, simply `DELETE` to the URL with the `group` query argument specifying the username:

```plaintext
DELETE /api/security/groups/delete?group=«groupname»
```

## HTTP Response Specification

| Response | Details |
| --- | --- |
| **200 (Success)** | the group is deleted from ProGet |
| **400 (Invalid Input)** | indicates invalid or missing properties; the body will provide some details as text |
| **401 (Authentication Required)** | indicates a [missing, unknown, or unauthorized API Key](/docs/proget/api/assets#authentication) |
| **500 (Server Error)** | indicates an unexpected error; the body will contain the message and stack trace, and this will also be logged |