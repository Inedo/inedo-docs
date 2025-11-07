---
title: "List Tasks"
order: 3
---

*List Tasks* is available as both a `pgutil` command and an HTTP Request, and will return an array of [SecurityTask](/docs/proget/api/security#securitytask-object) objects describing all existing Tasks.

:::(Info) (🚀 Quick Example: Listing Tasks with pgutil)
This example lists all existing Task attributes:

```bash
pgutil security tasks list
```
:::

## Command Specification (CLI)
The `security tasks list` command is used to list all existing Tasks.

**Listing all Tasks** requires no additional options:

```bash
pgutil security tasks list
```

Example output:

```plaintext
Task:
Description: Allows access to manage feed settings, delete packages, and overwrite packages.
Attributes:
 * Feeds_ViewFeed
 * Feeds_DownloadPackage
 * Feeds_DeletePackage
 * Feeds_AddPackage
 * Feeds_PullPackage
 * Admin_ManageConnectors
 * Admin_ManageFeed
 * Feeds_OverwritePackage
 * Feeds_AcceptPackagePromotions
 * Feeds_UnlistPackage

Task:
Description: Allows access to create, edit, and resolve issues on projects and releases
Attributes:
 * Projects_UploadSbom
 * Projects_View
 * Projects_Manage
 * Projects_ResolveIssue

Task:
Description: Allows access to promote packages to a specified feed of the same feed type. Users granted this task should also be granted at least the View & Download Packages task for the source feed.
Attributes:
 * Feeds_ViewFeed
 * Feeds_DownloadPackage
 * Feeds_AddPackage
 * Feeds_AcceptPackagePromotions

...
```

## HTTP Request Specification
To list all Tasks, simply `GET` to the URL with an [appropriate API Key](/docs/proget/api/sca#authentication).

```plaintext
GET /api/security/tasks/list
```

## HTTP Response Specification

A successful (`200`) response body will contain an array of [SecurityTask](/docs/proget/api/security#securitytask-object) objects. For example, to listing all Tasks, the request would return this:

```json
GET /api/security/attributes/list

[
  {
    "name": "Manage Feed",
    "description": "Allows access to manage feed settings, delete packages, and overwrite packages.",
    "feedScoped": true,
    "attributes": [
      "Feeds_ViewFeed",
      "Feeds_DownloadPackage",
      "Feeds_DeletePackage",
      "Feeds_AddPackage",
      "Feeds_PullPackage",
      "Admin_ManageConnectors",
      "Admin_ManageFeed",
      "Feeds_OverwritePackage",
      "Feeds_AcceptPackagePromotions",
      "Feeds_UnlistPackage"
    ]
  },
  {
    "name": "Manage Projects",
    "description": "Allows access to create, edit, and resolve issues on projects and releases",
    "feedScoped": false,
    "attributes": [
      "Projects_UploadSbom",
      "Projects_View",
      "Projects_Manage",
      "Projects_ResolveIssue"
    ]
  },
  {
    "name": "Promote Packages",
    "description": "Allows access to promote packages to a specified feed of the same feed type. Users granted this task should also be granted at least the View & Download Packages task for the source feed.",
    "feedScoped": true,
    "attributes": [
      "Feeds_ViewFeed",
      "Feeds_DownloadPackage",
      "Feeds_AddPackage",
      "Feeds_AcceptPackagePromotions"
    ]
  },
  {...}
]
```

| Response | Details |
| --- | --- |
| **200 (Success)** | body will contain an array of [SecurityTaskAttribute](/docs/proget/api/security#securitytaskattribute-object) objects |
| **403 (Unauthorized API Key)** | indicates a [missing, unknown, or unauthorized API Key](/docs/proget/api/sca#authentication); the body will be empty |
| **500 (Server Error)** | indicates an unexpected error; the body will contain the message and stack trace, and this will also be logged |