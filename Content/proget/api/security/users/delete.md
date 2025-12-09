---
title: "Delete User"
order: 3
---

*Delete User* is available as both a `pgutil` command and an HTTP Request, and will delete a specified User account from ProGet. Note that it's *not* considered an error to delete a User that does not exist. 

:::(Info) (🚀 Quick Example: Deleting a User with pgutil)
This example will delete a User account `jsmith`:

```bash
pgutil security users delete --username="jsmith"
```
:::

## Command Specification (CLI)
The `security users delete` command is used to delete a User account from ProGet.

The `--username` options is always required. 

**Deleting a User** requires the user name (e.g. `"jsmith"`):

```bash
pgutil security users delete --username="jsmith"
```

## HTTP Request Specification

To delete a User, simply `DELETE` to the following URL with the `user` query argument specifying the username:

```plaintext
DELETE /api/security/users/delete?user=«username»
```

## HTTP Response Specification

A `200` response will indicate the User has been deleted from ProGet. a `403` response indicates a [missing, unknown, or unauthorized API Key](/docs/proget/api/security#authentication).