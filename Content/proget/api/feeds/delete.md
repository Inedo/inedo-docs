---
title: "Delete Feed"
order: 5
---

*Delete Feed* is available as both a `pgutil` command and an HTTP Request, and will delete a specified feed.

:::(Info) (🚀 Quick Example: Deleting a feed with pgutil)
This example deletes the feed `myNugetFeed`:

```bash
pgutil feeds delete --name=myNugetFeed
```
:::

## Command Specification (CLI)
The `feeds delete` command is used to delete a feed.

The `--name` option is always required.

:::(info)(📄 Note: pgutil Sources)
Source options must also be specified unless you have the "Default" source configured, and that a feed may be instead specified in the source. See [Working with Sources](/docs/proget/api/pgutil#sources) to learn more.
:::

**Deleting a feed** requires the feed name (e.g. `myNugetFeed`):

```bash
pgutil feeds delete --name=myNugetFeed
```

## HTTP Request Specification
To delete a specified feed simply `DELETE` to the following URL with the feed name and an [appropriate API Key](/docs/proget/api/feeds#authentication).

```plaintext
DELETE /api/management/feeds/delete/«feed-name»
```

## HTTP Response Specification
A successful `200` response indicates that the specified feed has been deleted. A `403` response indicates a [missing, unknown, or unauthorized API Key](/docs/proget/api/security#authentication).
