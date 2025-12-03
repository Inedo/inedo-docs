---
title: "List Task Attributes"
order: 4
---

*List Task Attributes* is available as both a `pgutil` command and an HTTP Request, and will a list describing all existing Task attributes.

:::(Info) (🚀 Quick Example: Listing Task attributes with pgutil)
This example lists all existing Task attributes:

```bash
pgutil security attributes list
```
:::

## Command Specification (CLI)
The `security attributes list` command is used to list all Task attributes.

**Listing all Task attributes** requires no additional options:

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
To list all Task attributes, simply `GET` to the following URL with an [appropriate API Key](/docs/proget/api/sca#authentication):

```plaintext
GET /api/security/attributes/list
```

## HTTP Response Specification

An array of `SecurityTaskAttribute` (see [SecurityTaskAttribute.cs](https://github.com/Inedo/pgutil/blob/thousand/Inedo.ProGet/SecurityTaskAttribute.cs)) objects will be returned on a successful `200` response. A `403` response indicates a [missing, unknown, or unauthorized API Key](/docs/proget/api/security#authentication).