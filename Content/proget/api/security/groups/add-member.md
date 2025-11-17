---
title: "Add Group Member"
order: 4
---

*Add Group Member* is available as a `pgutil` command, and will assign an existing User to an existing Group.

:::(Info) (🚀 Quick Example: Adding a user to a group with pgutil)
This example adds the user `"John Smith"` to the group `Developers`:

```bash
pgutil security groups members add --member="John Smith" --name=Developers
```
:::

## Command Specification (CLI)
The `security groups members add` command is used to add an existing User to an existing Group.

The `--member` and `--name` options are always required.

**Adding a user to a group** requires the user name (e.g. `"John Smith"`) and group name (e.g. `Developers`):

```bash
pgutil security groups members add --member="John Smith" --name=Developers
```

## HTTP Requests

This command does not have an HTTP endpoint. To add new members to a group, `POST` to the [Create Group](/docs/proget/api/security/groups/create) URL `/api/security/groups/add` with an [appropriate API Key](/docs/proget/api/sca#authentication) and a [SecurityGroup](/docs/proget/api/security#securitygroup-object) object as the request, containing the current members together with new members you wish to add. For example, to adding Robert Green to the group `Developers` that already contains `John Smith` and `David Jones`:

```json
POST /api/security/groups/add
{
    "name": "Developers",
    "user": ["John Smith", "David Jones", "Robert Green"]
}
```

:::(info)(Create Group Endpoint)
The [Create Group](/docs/proget/api/security/groups/create) endpoint normally creates a new group. If the group already exists, the request will overwrite the existing member list. To add new members without removing existing ones, include all current members in the request along with the new members.
:::