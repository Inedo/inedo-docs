---
title: "Edit User"
order: 2
---

*Edit User* is available as both a `pgutil` command and an HTTP Request, and will edit an existing User account in ProGet. The `username` must exist to edit the user. 

:::(Info) (🚀 Quick Example: Editing an existing User account with pgutil)
This example edits the User `"John Smith` with a new email `johnsmith@kramerica.com`:

```bash
pgutil security users edit --username="John Smith" --email=johnsmith@kramerica.com
```
:::

## Command Specification (CLI)
The `security users edit` command is used to edit an existing User.

The `--username` option is always required.

One or more of the `--displayname`, `--email`, and `--password` options may be included, and will overwrite the existing value(s).

**Editing a user** requires the the user name (e.g. `"John Smith"`) and may include the `--displayname`, `--email`, and or `--password`. The example below edits the User's display name:

```bash
pgutil security users edit --username="John Smith" --displayname=johnsmith
```

## HTTP Request Specification

To edit an existing User account, simply `POST` to the URL with an [appropriate API Key](/docs/proget/api/security#authentication) and a `SecurityUser` object (see [SecurityUser.cs](https://github.com/Inedo/pgutil/blob/thousand/Inedo.ProGet/SecurityUser.cs)) object as the request body. The `Name` property's value must already exist in ProGet.

```plaintext
POST /api/security/users/update
```

## HTTP Response Specification

A `SecurityUser` object will be returned on a successful `200` response. A `403` response indicates a [missing, unknown, or unauthorized API Key](/docs/proget/api/security#authentication).