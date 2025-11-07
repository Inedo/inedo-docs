---
title: "Create User"
order: 1
---

*Create User* is available as both a `pgutil` command and an HTTP Request. It will create a new User account in ProGet, and return a [SecurityUser](/docs/proget/api/security#securityuser-object) object describing the new User created.

:::(Info) (🚀 Quick Example: Creating a new User account with pgutil)
This example creates the User `"John Smith"` with accompanying `displayname`, `email` and `password`

```bash
pgutil security users create --username="John Smith" --displayname=jsmith --email=jsmith@kramerica.com  --password=password123
```
:::

## Command Specification (CLI)
The `security users create` command is used to create a new User.

The `--username`, `--displayname`, and `--email` options are always required.

If `--password` is not specified, the User will be prompted to manually input a password and retype it to confirm. 

**Creating a user** requires the the user name (e.g. `"John Smith"`), display name (e.g. `jsmith`), and the email address (e.g `john@kramerica.com`). You can also include a password (e.g. `abc12345`).

```bash
pgutil security users create --username="John Smith" --displayname=jsmith --email=john@kramerica.com  --password=password123
```

## HTTP Request Specification
To create a new User account, simply `POST` to the URL with an [appropriate API Key](/docs/proget/api/security#authentication) and a [SecurityUser](/docs/proget/api/security#securityuser-object) object as the request body.

```plaintext
POST /api/security/users/add
```

## HTTP Response Specification

A successful (`200`) response body will contain a [SecurityUser](/docs/proget/api/security#securityuser-object) object. For example, to creating a new User account with the user name `"John Smith"`, the request would return this:

```json
POST /api/security/users/add

{
    "name": "John Smith",
    "displayName": "jsmith",
    "mail": "jsmith@kramerica.com",
    "password": "password123",
}
```

Note: When returned, the JSON object will hide the `password` field on output.

| Response | Details |
| --- | --- |
| **200 (Success)** | the user is created and the body will contain a [SecurityUser](/docs/proget/api/security#securityuser-object) object (with `password` hidden) |
| **400 (Invalid Input)** | indicates invalid or missing properties on the [SecurityUser](/docs/proget/api/security#securityuser-object) object; the body will provide some details as text |
| **403 (Unauthorized API Key)** | indicates a [missing, unknown, or unauthorized API Key](/docs/proget/api/sca#authentication); the body will be empty |
| **500 (Server Error)** | indicates an unexpected error; the body will contain the message and stack trace, and this will also be logged |