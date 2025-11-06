---
title: "List Users"
order: 5
---

*List Users* is available as both a `pgutil` command and an HTTP Request, and will return an array of [SecurityUser](/docs/proget/api/security#securityuser-object) objects describing the user accounts in ProGet.

:::(Info) (🚀 Quick Example: Listing user accounts with pgutil)
This example lists all existing users:

```bash
pgutil security users list
```
:::

## Command Specification (CLI)
The `security users list` command is used to list all user accounts in ProGet.

The `--search-term` option is optional, and can be used to filter users.

**Listing all existing users** requires no additional options:

```bash
pgutil security users list
```

Example output:

```plaintext
Administrator
John Smith
David Jones
John Barnes
Michael Bankson
```

**Listing users with a filter** requires requires a search term (e.g. `"John"`)

```bash
pgutil security users list --search-term="John"
```

Example output:

```plaintext
John Smith
John Barnes
```

## HTTP Request Specification
To list all user accounts in ProGet, simply `GET` to the URL with an [appropriate API Key](/docs/proget/api/sca#authentication).

```plaintext
GET /api/security/users/list
```

The list results can be filtered using the optional `search-term` query argument. 

```plaintext
GET /api/security/users/list?search-term=John
```

## HTTP Response Specification

A successful (`200`) response body will contain an array of [SecurityUser](/docs/proget/api/security#securityuser-object) objects. For example, to listing users filtered by the name `John`, the request would return this:

```json
GET /api/security/users/list?search-term=John

[
  {
   "name":"John Smith",
   "displayName":"jsmith",
   "email":"jsmith@kramerica.com",
   "groups":["Developers"]
  },
  {
   "name":"John Barnes",
   "displayName":"jbarnes",
   "email":"jbarnes@kramerica.com",
   "groups":["SREs"]
  }
  {...}
]
```

| Response | Details |
| --- | --- |
| **200 (Success)** | body will contain an array of [SecurityUser](/docs/proget/api/security#securityuser-object) objects |
| **403 (Unauthorized API Key)** | indicates a [missing, unknown, or unauthorized API Key](/docs/proget/api/sca#authentication); the body will be empty |
| **500 (Server Error)** | indicates an unexpected error; the body will contain the message and stack trace, and this will also be logged |