---
title: "Remove Group Member"
order: 5
---

*Remove Group Member* is available as a `pgutil` command and an HTTP Request, and will remove a specified group member from a Group in ProGet.

:::(Info) (🚀 Quick Example: Removing a group member from a Group with pgutil)
This example will remove the group member `"jsmith"` from the Group `Developers`:

```bash
pgutil security groups members remove --name=Developers --member="jsmith"
```
:::

## Command Specification (CLI)
The `security groups members remove` command is used to remove a group member from a Group in ProGet.

The `--name` and `--member` options are always required. 

**Removing a group member from a group** requires the Group name (e.g. `Developers`) and the Group member name (e.g. `"jsmith"`):

```bash
pgutil security groups members remove --name=Developers --member="jsmith"
```

Note source options must also be specified unless you have the "Default" source configured. See [Working with Sources](/docs/proget/api/pgutil#sources) to learn more.

## HTTP Request Specification

To remove a member from an existing Group, `POST` to the following URL with an [appropriate API Key](/docs/proget/api/security#authentication) and a `SecurityGroup` (see [SecurityGroup.cs](https://github.com/Inedo/pgutil/blob/thousand/Inedo.ProGet/SecurityGroup.cs)) object as the request body.

```plaintext
POST /api/security/groups/update
```

To remove a member, simply omit their username from the `Users` array in the updated `SecurityGroup` object. Any User not included in the list is removed from the Group. As this endpoint replaces the member list, the request body must contain the desired list of members.

## HTTP Response Specification

A `SecurityGroup` object will be returned on a successful `200`, indicating that the Group has been updated. A `403` response indicates a [missing, unknown, or unauthorized API Key](/docs/proget/api/security#authentication).