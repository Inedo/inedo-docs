---
title: "Create Group"
order: 1
---

*Create Group* is available as both a `pgutil` command and an HTTP Request. It will create a new Group in ProGet, and return a [SecurityGroup](/docs/proget/api/security#securitygroup-object) object describing the new Group.

:::(Info) (🚀 Quick Example: Creating a new Group with pgutil)
This example creates the group `Developers`:

```bash
pgutil security groups create --name=Developers
```
:::

## Command Specification (CLI)
The `security groups create` command is used to create a new Group.

The `--name` option is always required.

**Creating a Group** requires the Group name (e.g. `Developers`):

```bash
pgutil security groups create --name=Developers
```

## HTTP Request Specification
To create a Group, simply `POST` to the URL with an [appropriate API Key](/docs/proget/api/sca#authentication) and a [SecurityGroup](/docs/proget/api/security#securitygroup-object) object as the request body.

```plaintext
POST /api/security/groups/add
```

## HTTP Response Specification

A successful (`200`) response body will contain a [SecurityGroup](/docs/proget/api/security#securitygroup-object) object. For example, to creating a new Group `Developers`, the request would return this:

```json
POST /api/security/groups/add

{
  "name":"Developers"
}
```

| Response | Details |
| --- | --- |
| **200 (Success)** | the group is created and the body will contain a [SecurityGroup](/docs/proget/api/security#securitygroup-object) object |
| **400 (Invalid Input)** | indicates invalid or missing properties on the [SecurityGroup](/docs/proget/api/security#securitygroup-object)  object; the body will provide some details as text |
| **403 (Unauthorized API Key)** | indicates a [missing, unknown, or unauthorized API Key](/docs/proget/api/sca#authentication); the body will be empty |
| **500 (Server Error)** | indicates an unexpected error; the body will contain the message and stack trace, and this will also be logged |