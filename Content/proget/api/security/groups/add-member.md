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
