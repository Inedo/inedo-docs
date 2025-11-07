---
title: "Delete User"
order: 3
---

*Delete User* is available as both a `pgutil` command and an HTTP Request, and will delete a specified User account from ProGet. Note that it's *not* considered an error to delete a User that does not exist. 

:::(Info) (🚀 Quick Example: Deleting a User with pgutil)
This example will delete a User account `John Smith`:

```bash
pgutil security users delete --username="John Smith"
```
:::

## Command Specification (CLI)
The `security users delete` command is used to delete a User account from ProGet.

The `--username` options is always required. 

**Deleting a User** requires the user name (e.g. `"John Smith"`):

```bash
pgutil security users delete --username="John Smith"
```

## HTTP Request Specification

To delete a User, simply `DELETE` to the URL with the `user` query argument specifying the username:

```plaintext
DELETE /api/security/users/delete?user=«username»
```

## HTTP Response Specification

| Response | Details |
| --- | --- |
| **200 (Success)** | the User is deleted from ProGet |
| **400 (Invalid Input)** | indicates invalid or missing properties; the body will provide some details as text |
| **401 (Authentication Required)** | indicates a [missing, unknown, or unauthorized API Key](/docs/proget/api/assets#authentication) |
| **500 (Server Error)** | indicates an unexpected error; the body will contain the message and stack trace, and this will also be logged |
