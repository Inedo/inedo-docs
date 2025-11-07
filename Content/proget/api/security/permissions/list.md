---
title: "List Permissions"
order: 3
---

*List Permissions* is available as both a `pgutil` command and an HTTP Request, and will return an array of [SecurityPermission](/docs/proget/api/security#securitypermission-object) objects describing all configured permissions.

:::(Info) (🚀 Quick Example: Listing permissions with pgutil)
This example lists all existing permissions:

```bash
pgutil security permissions list
```
:::

## Command Specification (CLI)
The `security permissions list` command is used to list all existing permissions.

**Listing all permissions** requires no additional options:

```bash
pgutil security permissions list
```

Example output:

```yaml
1: allow View & Download Packages globally for user Anonymous
2: allow Administer globally for group Administrators
3: allow Administer globally for user Anonymous
...
```

## HTTP Request Specification
To list all permissions, simply `GET` to the URL with an [appropriate API Key](/docs/proget/api/sca#authentication).

```plaintext
GET /api/security/permissions/list
```

## HTTP Response Specification

A successful (`200`) response body will contain an array of [SecurityPermission](/docs/proget/api/security#securitypermission-object) objects. For example, to listing all permissions, the request would return this:

```json
GET /api/security/permissions/list

[
  {
    "id": 2,
    "group": "Administrators",
    "task": "Administer",
    "deny": false
  },
  {
    "id": 3,
    "user": "Anonymous",
    "task": "Administer",
    "deny": false
  },
  {
    "id": 1,
    "user": "Anonymous",
    "task": "View & Download Packages",
    "deny": false
  },
  {...}
]
```

| Response | Details |
| --- | --- |
| **200 (Success)** | body will contain an array of [SecurityPermission](/docs/proget/api/security#securitypermission-object) objects |
| **403 (Unauthorized API Key)** | indicates a [missing, unknown, or unauthorized API Key](/docs/proget/api/sca#authentication); the body will be empty |
| **500 (Server Error)** | indicates an unexpected error; the body will contain the message and stack trace, and this will also be logged |