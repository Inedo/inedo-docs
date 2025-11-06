---
title: "List Groups"
order: 3
---

*List Groups* is available as both a `pgutil` command and an HTTP Request, and will return an array of [SecurityGroup](/docs/proget/api/security#securitygroup-object) objects listing the groups in ProGet and number of members in each.

:::(Info) (🚀 Quick Example: Listing groups with pgutil)
This example lists all existing groups:

```bash
pgutil security groups list
```
:::

## Command Specification (CLI)
The `security groups list` command is used to list all group in ProGet and return the number of members in each.

**Listing all existing groups** requires no additional options:

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
To list all groups in ProGet, simply `GET` to the URL with an [appropriate API Key](/docs/proget/api/sca#authentication).

```plaintext
GET /api/security/groups/list
```

## HTTP Response Specification

A successful (`200`) response body will contain an array of [SecurityGroup](/docs/proget/api/security#securitygroup-object) objects. For example, to listing all existing groups, the request would return this:

```json
GET /api/security/groups/list

[
  {
   "name":"Administrators",
  },
  {
   "name":"Lead Developers",
  }
  {
   "name":"Developers",
  },
  {
   "name":"SREs",
  }
  {...}
]
```

| Response | Details |
| --- | --- |
| **200 (Success)** | body will contain an array of [SecurityGroup](/docs/proget/api/security#securitygroup-object) objects |
| **403 (Unauthorized API Key)** | indicates a [missing, unknown, or unauthorized API Key](/docs/proget/api/sca#authentication); the body will be empty |
| **500 (Server Error)** | indicates an unexpected error; the body will contain the message and stack trace, and this will also be logged |