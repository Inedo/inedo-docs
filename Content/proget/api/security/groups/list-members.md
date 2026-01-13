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

Note source options must also be specified unless you have the "Default" source configured. See [Working with Sources](/docs/proget/api/pgutil#sources) to learn more.

## HTTP Request Specification
This command is not available as an HTTP endpoint, however you can use the [List Group Members](/docs/proget/api/security/groups/list-members) HTTP endpoint `GET /api/security/groups/list` to return a list of all groups and their assigned members.

Alternatively, to return a single group you can run the following PowerShell script:

```powershell
(Invoke-RestMethod -Headers @{"X-ApiKey"="abc12345"} "https://proget.corp.local/api/security/groups/list") |
    Where-Object { $_.name -ieq "«group-name" } |
    ConvertTo-Json -Depth 2
```

This will return a `SecurityGroup` (see [SecurityGroup.cs](https://github.com/Inedo/pgutil/blob/thousand/Inedo.ProGet/SecurityGroup.cs)) object of the Group specified.


