---
title: "Delete Feed"
order: 5
---

*Delete Feed* is available as both a `pgutil` command and an HTTP Request, and will delete a specified feed.

:::(Info) (🚀 Quick Example: Deleting a feed with pgutil)
This example deletes the feed `myNugetFeed`:

```bash
pgutil feed delete --feed=myNugetFeed
```
:::

## Command Specification (CLI)
The `feed delete` command is used to delete a feed.

The `--feed` option is always required.

**Deleting a feed** requires the feed name (e.g. `myNugetFeed`):

```bash
pgutil feed delete --feed=myNugetFeed
```

## HTTP Request Specification
To delete a specified feed simply `DELETE` to the following URL with the feed name and an [appropriate API Key](/docs/proget/api/feeds#authentication).

```plaintext
DELETE /api/management/feeds/delete/«feed-name»
```

## HTTP Response Specification
A successful `200` response indicates that the specified feed has been deleted. A `403` response indicates a [missing, unknown, or unauthorized API Key](/docs/proget/api/security#authentication).
