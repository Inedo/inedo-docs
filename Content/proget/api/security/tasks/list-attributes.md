---
title: "List Task Attributes"
order: 4
---

*List Task Attributes* is available as both a `pgutil` command and an HTTP Request, and will return an array of [SecurityTaskAttribute](/docs/proget/api/security#securitytaskattribute-object) objects describing all existing task attributes.

:::(Info) (🚀 Quick Example: Listing task attributes with pgutil)
This example lists all existing task attributes:

```bash
pgutil security attributes list
```
:::

## Command Specification (CLI)
The `security attributes list` command is used to list all task attributes.

**Listing all task attributes** requires no additional options:

```bash
pgutil security attributes list
```

Example output:

```plaintext
 - Feeds_ViewFeed*
 - Feeds_DownloadPackage*
 - Feeds_DeletePackage*
 - Feeds_AddPackage*
 - Feeds_PullPackage*
 - Admin_ManageConnectors*
...
```

## HTTP Request Specification
To list all task attributes, simply `GET` to the URL with an [appropriate API Key](/docs/proget/api/sca#authentication).

```plaintext
GET /api/security/attributes/list
```

## HTTP Response Specification

A successful (`200`) response body will contain an array of [SecurityTaskAttribute](/docs/proget/api/security#securitytaskattribute-object) objects. For example, to listing all task attributes, the request would return this:

```json
GET /api/security/attributes/list

[
  {
    "name": "Feeds_ViewFeed",
    "feedScoped": true
  },
  {
    "name": "Feeds_DownloadPackage",
    "feedScoped": true
  },
  {
    "name": "Feeds_DeletePackage",
    "feedScoped": true
  },
  {...}
]
```

| Response | Details |
| --- | --- |
| **200 (Success)** | body will contain an array of [SecurityTaskAttribute](/docs/proget/api/security#securitytaskattribute-object) objects |
| **403 (Unauthorized API Key)** | indicates a [missing, unknown, or unauthorized API Key](/docs/proget/api/sca#authentication); the body will be empty |
| **500 (Server Error)** | indicates an unexpected error; the body will contain the message and stack trace, and this will also be logged |