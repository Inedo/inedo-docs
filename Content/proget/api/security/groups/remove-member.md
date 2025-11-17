---
title: "Remove Group Member"
order: 5
---

*Remove Group Member* is available as a `pgutil` command, and will remove a specified group member from a Group in ProGet.

:::(Info) (🚀 Quick Example: Removing a group member from a Group with pgutil)
This example will remove the group member `"John Smith"` from the Group `Developers`:

```bash
pgutil security groups members delete --name=Developers --member="John Smith"
```
:::

## Command Specification (CLI)
The `security groups members delete` command is used to remove a group member from a Group in ProGet.

The `--name` and `--member` options are always required. 

**Removing a group member from a group** requires the Group name (e.g. `Developers`) and the Group member name (e.g. `"John Smith"`):

```bash
pgutil security groups members delete --name=Developers --member="John Smith"
```

## HTTP Requests

This command does not have an HTTP endpoint. To remove members from a group, `POST` to the [Create Group](/docs/proget/api/security/groups/create) URL `/api/security/groups/add` with an [appropriate API Key](/docs/proget/api/sca#authentication) and a [SecurityGroup](/docs/proget/api/security#securitygroup-object) object as the request, containing the group members excluding the members you wish to add. For example, to removing David Jones from the group `Developers` that contains `John Smith` and `David Jones`:

```json
POST /api/security/groups/add
{
    "name": "Developers",
    "user": ["John Smith"]
}
```

:::(info)(Create Group Endpoint)
The [Create Group](/docs/proget/api/security/groups/create) endpoint normally creates a new group. If the group already exists, the request will overwrite the existing member list. To remove members, include all current members in the request with the exception of those you wish to remove.
:::