---
title: "Delete User"
order: 3
---

*Delete User* is available as both a `pgutil` command and an HTTP Request, and will delete a specified user account from ProGet. Note that it's *not* considered an error to delete a user that does not exist. 

:::(Info) (🚀 Quick Example: Deleting a user with pgutil)
This example will delete a user account `John Smith`:

```bash
pgutil security users delete --username="John Smith"
```
:::

## Command Specification (CLI)
The `security users delete` command is used to delete a user account from ProGet.

The `--username` options is always required. 

**Deleting a user** requires the user name (e.g. `"John Smith"`):

```bash
pgutil security users delete --username="John Smith"
```

## HTTP Request Specification

To delete a user, simply `DELETE` to the URL with the `user` query argument specifying the username:

```plaintext
DELETE /api/security/users/delete?user=«username»
```

## HTTP Response Specification

| Response | Details |
| --- | --- |
| **200 (Success)** | the user is deleted from ProGet |
| **401 (Authentication Required)** | indicates a [missing, unknown, or unauthorized API Key](/docs/proget/api/assets#authentication) |
