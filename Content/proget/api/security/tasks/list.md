---
title: "List Tasks"
order: 3
---

*List Tasks* is available as both a `pgutil` command and an HTTP Request, and will return an list describing all existing Tasks.

:::(Info) (🚀 Quick Example: Listing Tasks with pgutil)
This example lists all existing Task attributes:

```bash
pgutil security tasks list
```
:::

## Command Specification (CLI)
The `security tasks list` command is used to list all existing Tasks.

:::(info)(📄 Note: pgutil Sources)
Source options must also be specified unless you have the "Default" source configured. See [Working with Sources](/docs/proget/api/pgutil#sources) to learn more.
:::

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
To list all Tasks, simply `GET` to the following URL with an [appropriate API Key](/docs/proget/api/sca#authentication):

```plaintext
GET /api/security/tasks/list
```

## HTTP Response Specification

An array of `SecurityTask` (see [SecurityTask.cs](https://github.com/Inedo/pgutil/blob/thousand/Inedo.ProGet/SecurityTask.cs)) objects will be returned on a successful `200` response. A `403` response indicates a [missing, unknown, or unauthorized API Key](/docs/proget/api/security#authentication).