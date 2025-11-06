---
title: "Edit User"
order: 2
---

*Edit User* is available as both a `pgutil` command and an HTTP Request. It will edit an existing user account in ProGet, and return an updated [SecurityUser](/docs/proget/api/security#securityuser-object) object. The `username` must exist to edit the user. 

:::(Info) (🚀 Quick Example: Editing an existing user account with pgutil)
This example edits the user `"John Smith` with a new email `johnsmith@kramerica.com`:

```bash
pgutil security users edit --username="John Smith" --email=johnsmith@kramerica.com
```
:::

## Command Specification (CLI)
The `security users edit` command is used to edit an existing user.

The `--username` option is always required.

One or more of the `--displayname`, `--email`, and `--password` options may be included, and will overwrite the existing value(s).

**Editing a user** requires the the username (e.g. `"John Smith"`) and may include the `--displayname`, `--email`, and or `--password`. The example below edits the user's display name:

```bash
pgutil security users edit --username="John Smith" --displayname=johnsmith
```

## HTTP Request Specification
To edit an existing user account, simply `POST` to the URL with an [appropriate API Key](/docs/proget/api/security#authentication) and a [SecurityUser](/docs/proget/api/security#securityuser-object) object as the request body.

```plaintext
POST /api/security/users/update
```

## HTTP Response Specification

A successful (`200`) response body will contain a [SecurityUser](/docs/proget/api/security#securityuser-object) object. For example, to updating an existing account with the user name `"John Smith"` and an updated display name of `johnsmith` the request would return this:

```json
POST /api/security/users/update

{
    "name": "John Smith",
    "displayName": "johnsmith",
}
```

| Response | Details |
| --- | --- |
| **200 (Success)** | body will contain a [SecurityUser](/docs/proget/api/security#securityuser-object) object |
| **400 (Invalid Input)** | indicates invalid or missing properties on the [SecurityUser](/docs/proget/api/security#securityuser-object) object; the body will provide some details as text |
| **403 (Unauthorized API Key)** | indicates a [missing, unknown, or unauthorized API Key](/docs/proget/api/sca#authentication); the body will be empty |
| **500 (Server Error)** | indicates an unexpected error; the body will contain the message and stack trace, and this will also be logged |