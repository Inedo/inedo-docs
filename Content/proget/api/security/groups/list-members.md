---
title: "List Group Members"
order: 6
---

*List Group Members* is available as a `pgutil` command, and will return the list of group members assigned to a specified group.

:::(Info) (🚀 Quick Example: Listing members in a group with pgutil)
This example lists all group members assigned to the group `Developers`

```bash
pgutil security groups members list --name=Developers
```
:::

## Command Specification (CLI)
The `security groups members list` command is used to list all group members assigned to a specified group. 

The `--name` option is always required.

**Listing group members in a group** requires the group name (e.g. `Developers`)

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
:::

## HTTP Request Specification
To list all builds of a project, simply `GET` to the URL with an [appropriate API Key](/docs/proget/api/sca#authentication).

```plaintext
GET /api/sca/releases?name=«projectName»
```

## HTTP Response Specification

A successful (`200`) response body will contain an array of [BuildInfo Object](/docs/proget/api/sca#buildinfo-object) objects. For example, to listing release versions of a project named `myProject`, the request would return this:

```json
GET /api/sca/releases?project=myProject

[
  {
    "version":"1.0.0",
    "active":true,
    "viewReleaseUrl":"https://proget.corp.local/projects/release?projectReleaseId=1",
    "viewIssuesUrl":"https://proget.corp.local/projects/release/issues?projectReleaseId=1"
  },
  {
    "version":"1.1.0",
    "active":true,
    "viewReleaseUrl":"https://proget.corp.local/projects/release?projectReleaseId=2",
    "viewIssuesUrl":"https://proget.corp.local/projects/release/issues?projectReleaseId=2"
  },
  {
    "version":"1.2.3",
    "active":true,
    "viewReleaseUrl":"https://proget.corp.local/projects/release?projectReleaseId=3",
    "viewIssuesUrl":"https://proget.corp.local/projects/release/issues?projectReleaseId=3"
  },
  {...}
]
```

| Response | Details |
| --- | --- |
| **200 (Success)** | body will contain an array of [BuildInfo](/docs/proget/api/sca#buildinfo-object) objects |
| **400 (Invalid Input)** | indicates invalid or missing properties |
| **403 (Unauthorized API Key)** | indicates a [missing, unknown, or unauthorized API Key](/docs/proget/api/sca#authentication); the body will be empty |
| **404 (Project Not Found)** | indicates that the specified project does not exist | 
| **500 (Server Error)** | indicates an unexpected error; the body will contain the message and stack trace, and this will also be logged |

:::(Internal) (TODO)
**ADD "LIST ALL GROUPS AND GROUP MEMBERS SCRIPT***
:::