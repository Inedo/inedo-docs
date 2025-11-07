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

:::(Internal) (TODO)
***NEED TO CONFIRM IF POSSIBLE VIA HTTP REQUEST***

## HTTP Request Specification
To list all members of a Group, simply `GET` to the URL with an [appropriate API Key](/docs/proget/api/sca#authentication).

```plaintext
GET /api/sca/...
```

## HTTP Response Specification

A successful (`200`) response body will contain an array of [BuildInfo Object](/docs/proget/api/sca#buildinfo-object) objects. For example, to listing release versions of a project named `myProject`, the request would return this:

```json
GET /api/sca/releases?project=myProject

[

]
```

| Response | Details |
| --- | --- |
| **200 (Success)** | body will contain an array of [BuildInfo](/docs/proget/api/sca#buildinfo-object) objects |
| **400 (Invalid Input)** | indicates invalid or missing properties |
| **403 (Unauthorized API Key)** | indicates a [missing, unknown, or unauthorized API Key](/docs/proget/api/sca#authentication); the body will be empty |
| **404 (Project Not Found)** | indicates that the specified project does not exist | 
| **500 (Server Error)** | indicates an unexpected error; the body will contain the message and stack trace, and this will also be logged |

:::

:::(Internal) (TODO)
**ADD "LIST ALL GROUPS AND GROUP MEMBERS SCRIPT***
:::