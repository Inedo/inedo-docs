---
title: "Add Permission"
order: 1
---

*Add Permission* is available as both a `pgutil` command and an HTTP Request. It will either grant or deny a Task/Permission to a User or Group, and return a [SecurityPermission](/docs/proget/api/security#securitypermission-object) object describing the Permission configured.

:::(Info) (🚀 Quick Example: Adding a Permission with pgutil)
This example grants the `Developers` Group Permission to `view and download packages` from Feeds in the `Approved Package Feeds` Feed group:

```bash
pgutil security permissions add --task="View & Download Packages" --group=developers --feed-group="Approved Package Feeds"
```
:::

## Command Specification (CLI)
The `security permissions add` command is used to grant or deny a Task/Permission to a User or Group.

The `--task` option together with either the `--user` or `--group` options are always required.

The `--feed` and `--feed-group` options are optional, and will scope the permission to either a Feed or Feed group. If these are omitted, the Permission is applied globally. 

The `--deny` option is optional, and is used to set a "deny" Permission instead of a grant.

**Granting global Permission to a User** requires the the Task name (e.g.`"View & Download Packages"`), and the User name (e.g. `"John Smith"`):

```bash
pgutil security permissions add --task="View & Download Packages" --user="John Smith"
```

**Granting feed level Permission to a Group** requires the the Task name (e.g.`"Promote Packages"`), the Group name (e.g. `Developers`) and the Feed name (e.g. `approved-nuget`):

```bash
pgutil security permissions add --task="Promote Packages" --group=Developers --feed=approved-nuget
```

**Denying feed level Permission to a Group** requires the the Task name (e.g.`"View & Download Packages"`), the Group name (e.g. `Developers`), the Feed name (e.g. `unapproved-nuget`) and the `--deny` option:

```bash
pgutil security permissions add --task="View & Download Packages" --group=Developers --feed=unapproved-nuget --deny
```

**Denying feed group level permission to a user** requires the the Task name (e.g.`"Manage Feed"`), the User name (e.g. `"David Jones`), the Feed group name (e.g. `production-packages`) and the `--deny` option:

```bash
pgutil security permissions add --task="Manage Feed" --name-"David Jones" --feed-group=production-packages
```

## HTTP Request Specification
To add a Permission, simply `POST` to the URL with an [appropriate API Key](/docs/proget/api/security#authentication) and a [SecurityPermission](/docs/proget/api/security#securitypermission-object) object as the request body.

```plaintext
POST /api/security/permissions/add
```

## HTTP Response Specification

A successful (`200`) response body will contain a [SecurityPermission](/docs/proget/api/security#securitypermission-object) object. For example, to granting the User `"John Smith"` permission to `view and download packages` from the Feed `approved-nuget`, the request would return this:

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
| **200 (Success)** | the Permission is added and the body will contain a [SecurityPermission](/docs/proget/api/security#securitypermission-object) object |
| **400 (Invalid Input)** | indicates invalid or missing properties on the [SecurityPermission](/docs/proget/api/security#securitypermission-object) object; the body will provide some details as text |
| **403 (Unauthorized API Key)** | indicates a [missing, unknown, or unauthorized API Key](/docs/proget/api/sca#authentication); the body will be empty |
| **500 (Server Error)** | indicates an unexpected error; the body will contain the message and stack trace, and this will also be logged |