---
title: "Create User"
order: 1
---

*Create User* is available as both a `pgutil` command and an HTTP Request. It will create a new user account in ProGet, and return a [SecurityUser](/docs/proget/api/security#securityuser-object) object describing the new user created.

:::(Info) (🚀 Quick Example: Creating a new user account with pgutil)
This example creates the user `jsmith` with accompanying `username`, `email` and `password`

```bash
pgutil security users create --displayname=jsmith --username="John Smith" --email=john@smith.com  --password=abc12345
```
:::

## Command Specification (CLI)
The `security users create` command is used to create a new user.

The `--displayname`, `--username`, and `--email` options are always required.

If `--password` is not specified, the user will be prompted to manually input a password and retype it to confirm. 

**Creating a user** requires the display name (e.g. `jsmith`), the username (e.g. `"John Smith"`) and the email address (e.g `john@kramerica.com`). You can also include a password (e.g. `abc12345`).

```bash
pgutil security users create --displayname=jsmith --username="John Smith" --email=john@kramerica.com  --password=abc12345
```

## HTTP Request Specification
To create a new user account, simply `POST` to the URL with an [appropriate API Key](/docs/proget/api/security#authentication) and a [SecurityUser](/docs/proget/api/security#securityuser-object) object as the request body.

```plaintext
POST /api/security/users/add
```

## HTTP Response Specification

A successful (`200`) response body will contain a [SecurityUser](/docs/proget/api/security#securityuser-object) object. For example, to creating a new user account with the display name `jsmith`, the request would return this:

```json
POST /api/security/users/add

{
    "name": "John Smith",
    "displayName": "jsmith",
    "mail": "jsmith@kramerica.com",
    "password": "password123",
    "groups": [] // refer to SecurityGroups object 
}
```

| Response | Details |
| --- | --- |
| **200 (Success)** | body will contain a [SecurityUser](/docs/proget/api/security#securityuser-object) object |
| **400 (Invalid Input)** | indicates invalid or missing properties on the [SecurityUser](/docs/proget/api/security#securityuser-object) object; the body will provide some details as text |
| **403 (Unauthorized API Key)** | indicates a [missing, unknown, or unauthorized API Key](/docs/proget/api/sca#authentication); the body will be empty |
| **500 (Server Error)** | indicates an unexpected error; the body will contain the message and stack trace, and this will also be logged |