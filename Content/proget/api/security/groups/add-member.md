---
title: "Add Group Member"
order: 4
---

*Add Group Member* is available as both a `pgutil` command and an HTTP Request, and will assign an existing user to an existing group.

:::(Info) (🚀 Quick Example: Adding a user to a group with pgutil)
This example adds the user `"John Smith"` to the group `Developers`:

```bash
pgutil security groups members add --member="John Smith" --name=Developers
```
:::

## Command Specification (CLI)
The `security groups members add` command is used to add an existing user to an existing group.

The `--member` and `--name` options are always required.

**Adding a user to a group** requires the user name (e.g. `"John Smith"`) and group name (e.g. `Developers`):

```bash
pgutil security groups members add --member="John Smith" --name=Developers
```

:::(Internal) (TODO)
***TRY TESTING ADDING WHEN ALREADY ADDED***
:::

## HTTP Request Specification
To add a user to a group, simply `POST` to the URL with an [appropriate API Key](/docs/proget/api/sca#authentication) and a [SecurityGroup](/docs/proget/api/security#securitygroup-object) object as the request body.

```plaintext
POST /api/security/groups/add
```

## HTTP Response Specification

A successful (`200`) response body will contain a [SecurityGroup](/docs/proget/api/security#securitygroup-object) object. For example, to creating a new group `Developers`, the request would return this:

```json
POST /api/security/groups/add

{
  "name":"Developers"
}
```

| Response | Details |
| --- | --- |
| **200 (Success)** | body will contain a [SecurityGroup](/docs/proget/api/security#securitygroup-object) object |
| **400 (Invalid Input)** | indicates invalid or missing properties on the [SecurityGroup](/docs/proget/api/security#securitygroup-object)  object; the body will provide some details as text |
| **403 (Unauthorized API Key)** | indicates a [missing, unknown, or unauthorized API Key](/docs/proget/api/sca#authentication); the body will be empty |
| **500 (Server Error)** | indicates an unexpected error; the body will contain the message and stack trace, and this will also be logged |


:::(Internal) (TODO)
***BULK ADD***
:::