---
title: "Remove Group Member"
order: 5
---

*Remove Group Member* is available as a `pgutil` command, and will remove a specified group member from a Group in ProGet.

:::(Info) (🚀 Quick Example: Removing a group member from a Group with pgutil)
This example will remove the group member `"John Smith"` from the Group `Developers`:

```bash
pgutil security groups members remove --name=Developers --member="John Smith"
```
:::

## Command Specification (CLI)
The `security groups members remove` command is used to remove a group member from a Group in ProGet.

The `--name` and `--member` options are always required. 

**Removing a group member from a group** requires the Group name (e.g. `Developers`) and the Group member name (e.g. `"John Smith"`):

```bash
pgutil security groups members remove --name=Developers --member="John Smith"
```