---
title: "Remove Group Member"
order: 5
---

*Remove Group Member* is available as both a `pgutil` command and an HTTP Request, and will remove a specified group member from a group in ProGet.

:::(Info) (🚀 Quick Example: Removing a group member from a group with pgutil)
This example will remove the group member `"John Smith"` from the group `Developers`:

```bash
pgutil security groups members delete --name=Developers --member="John Smith"
```
:::

## Command Specification (CLI)
The `security groups members delete` command is used to remove a group member from a group in ProGet.

The `--name` and `--member` options are always required. 

**Removing a group member from a group** requires the group name (e.g. `Developers`) and the group member name (e.g. `"John Smith"`):

```bash
pgutil security groups members delete --name=Developers --member="John Smith"
```


:::(Internal) (TODO)
***TRY REMOVING WHEN NOT ADDED***
:::


## HTTP Request Specification

To delete a group, simply `DELETE` to the URL with the `group` query argument specifying the username:

```plaintext
DELETE /api/security/groups/delete?group=«groupname»
```

## HTTP Response Specification

| Response | Details |
| --- | --- |
| **200 (Success)** | the user is deleted from ProGet |
| **401 (Authentication Required)** | indicates a [missing, unknown, or unauthorized API Key](/docs/proget/api/assets#authentication) |

:::(Internal) (TODO)
***BULK DELETE***
:::