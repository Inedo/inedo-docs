---
title: "List Groups"
order: 3
---

*List Groups* is available as both a `pgutil` command and an HTTP Request, and will return a list of Groups in ProGet and number of group members in each.

:::(Info) (🚀 Quick Example: Listing Groups with pgutil)
This example lists all existing Groups:

```bash
pgutil security groups list
```
:::

## Command Specification (CLI)
The `security groups list` command is used to list all Groups in ProGet and return the number of group members in each.

:::(info)(📄 Note: pgutil Sources)
Source options must also be specified unless you have the "Default" source configured. See [Working with Sources](/docs/proget/api/pgutil#sources) to learn more.
:::

**Listing all existing Groups** requires no additional options:

```bash
pgutil security groups list
```

Example output:

```plaintext
Administrators (members: 2)
Developers (members: 20)
Lead Developers (members: 2)
SREs (members: 13)
```

## HTTP Request Specification
To list all Groups in ProGet, simply `GET` to the following URL with an [appropriate API Key](/docs/proget/api/security#authentication):

```plaintext
GET /api/security/groups/list
```

## HTTP Response Specification

An array of `SecurityGroup` (see [SecurityGroup.cs](https://github.com/Inedo/pgutil/blob/thousand/Inedo.ProGet/SecurityGroup.cs)) objects will be returned on a successful `200` response. A `403` response indicates a [missing, unknown, or unauthorized API Key](/docs/proget/api/security#authentication).