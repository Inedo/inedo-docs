---
title: "List Group Members"
order: 6
---

*List Group Members* is available as a `pgutil` command, and will return the list of group members assigned to a specified Group.

:::(Info) (🚀 Quick Example: Listing members in a Group with pgutil)
This example lists all group members assigned to the Group `Developers`

```bash
pgutil security groups members list --name=Developers
```
:::

## Command Specification (CLI)
The `security groups members list` command is used to list all group members assigned to a specified Group. 

The `--name` option is always required.

**Listing group members in a group** requires the Group name (e.g. `Developers`)

```bash
pgutil security groups members list --name=Developers
```

Example output:

```plaintext
John Smith
David Jones
James Brown
```