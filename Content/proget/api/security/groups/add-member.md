---
title: "Add Group Member"
order: 4
---

*Add Group Member* is available as a `pgutil` command and an HTTP Request, and will assign an existing User to an existing Group.

:::(Info) (🚀 Quick Example: Adding a User to a Group with pgutil)
This example adds the user `"jsmith"` to the group `Developers`:

```bash
pgutil security groups members add --member="jsmith" --name=Developers
```
:::

## Command Specification (CLI)
The `security groups members add` command is used to add an existing User to an existing Group.

The `--member` and `--name` options are always required.

**Adding a user to a group** requires the user name (e.g. `"jsmith"`) and group name (e.g. `Developers`):

```bash
pgutil security groups members add --member="jsmith" --name=Developers
```

Note source options must also be specified unless you have the "Default" source configured. See [Working with Sources](/docs/proget/api/pgutil#sources) to learn more.

## HTTP Request Specification

To add a User to an existing Group, `POST` to the following URL with an [appropriate API Key](/docs/proget/api/security#authentication) and a `SecurityGroup` (see [SecurityGroup.cs](https://github.com/Inedo/pgutil/blob/thousand/Inedo.ProGet/SecurityGroup.cs)) object as the request body.

```plaintext
POST /api/security/groups/update
```

To add a User, include their `username` in the `Users` array in the updated `SecurityGroup` object, along with all existing members. As this endpoint replaces the member list, the request body must contain the desired list of members.

## HTTP Response Specification

A `SecurityGroup` object will be returned on a successful `200`, indicating that the Group has been updated. A `403` response indicates a [missing, unknown, or unauthorized API Key](/docs/proget/api/security#authentication).