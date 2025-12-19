---
title: "Add Group Member"
order: 4
---

*Add Group Member* is available as a `pgutil` command, and will assign an existing User to an existing Group.

:::(Info) (🚀 Quick Example: Adding a user to a group with pgutil)
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