---
title: "Add Permission"
order: 1
---

*Add Permission* is available as both a `pgutil` command and an HTTP Request. It will either grant or deny a task/permission to a user or group, and return a [SecurityPermission](/docs/proget/api/security#securitypermission-object) object describing the permission configured.

:::(Info) (🚀 Quick Example: Adding a permission with pgutil)
This example grants the `Developers` group permission to `view and download packages` from feeds in the `Approved Package Feeds` feed group:

```bash
pgutil security permissions add --task="View & Download Packages" --group=developers --feed-group="Approved Package Feeds"
```
:::

## Command Specification (CLI)
The `security permissions add` command is used to grant or deny a task/permission to a user or group.

The `--task` option together with either the `--user` or `--group` options are always required.

The `--feed` and `--feed-group` options are optional, and will scope the permission to either a feed or feed group. If these are omitted, the permission is applied globally. 

The `--deny` option is optional, and is used to set a "deny" permission instead of a grant.

**Granting global permission to a user** requires the the task name (e.g.`"View & Download Packages"`), and the user name (e.g. `"John Smith"`):

```bash
pgutil security permissions add --task="View & Download Packages" --user="John Smith"
```

**Granting feed level permission to a group** requires the the task name (e.g.`"Promote Packages"`), the group name (e.g. `Developers`) and the feed name (e.g. `approved-nuget`):

```bash
pgutil security permissions add --task="Promote Packages" --group=Developers --feed=approved-nuget
```

**Denying feed level permission to a group** requires the the task name (e.g.`"View & Download Packages"`), the group name (e.g. `Developers`), the feed name (e.g. `unapproved-nuget`) and the `--deny` option:

```bash
pgutil security permissions add --task="View & Download Packages" --group=Developers --feed=unapproved-nuget --deny
```

**Denying feed group level permission to a user** requires the the task name (e.g.`"Manage Feed"`), the user name (e.g. `"David Jones`), the feed group name (e.g. `production-packages`) and the `--deny` option:

```bash
pgutil security permissions add --task="Manage Feed" --name-"David Jones" --feed-group=production-packages
```

## HTTP Request Specification
To add a permission, simply `POST` to the URL with an [appropriate API Key](/docs/proget/api/security#authentication) and a [SecurityPermission](/docs/proget/api/security#securitypermission-object) object as the request body.

```plaintext
POST /api/security/permissions/add
```

## HTTP Response Specification

A successful (`200`) response body will contain a [SecurityPermission](/docs/proget/api/security#securitypermission-object) object. For example, to granting the user `"John Smith"` permission to `view and download packages` from the feed `approved-nuget`, the request would return this:

```json
POST /api/security/permissions/add

{
  {
    "user": "John Smith",
    "task": "View & Download Packages",
    "feed": "approved-nuget",
    "deny": false
  },
}
```

| Response | Details |
| --- | --- |
| **200 (Success)** | body will contain a [SecurityPermission](/docs/proget/api/security#securitypermission-object) object |
| **400 (Invalid Input)** | indicates invalid or missing properties on the [SecurityPermission](/docs/proget/api/security#securitypermission-object) object; the body will provide some details as text |
| **403 (Unauthorized API Key)** | indicates a [missing, unknown, or unauthorized API Key](/docs/proget/api/sca#authentication); the body will be empty |
| **500 (Server Error)** | indicates an unexpected error; the body will contain the message and stack trace, and this will also be logged |