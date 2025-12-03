---
title: "Create Group"
order: 1
---

*Create Group* is available as both a `pgutil` command and an HTTP Request, and will create a new Group in ProGet.

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
To create or a Group, simply `POST` to the following URL with an [appropriate API Key](/docs/proget/api/security#authentication) and a `SecurityGroup` object (see [SecurityGroup.cs](https://github.com/Inedo/pgutil/blob/thousand/Inedo.ProGet/SecurityGroup.cs)) object as the request body.

```plaintext
POST /api/security/groups/add
```

## HTTP Response Specification

A `SecurityGroup` object will be returned on a successful `200` response. A `403` response indicates a [missing, unknown, or unauthorized API Key](/docs/proget/api/security#authentication).