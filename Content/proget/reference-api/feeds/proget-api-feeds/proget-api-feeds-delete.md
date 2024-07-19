---
title: "Delete Feed"
order: 5
---

*Delete Feed* is available as both a `pgutil` command and an HTTP Request, and will delete a specified feed.

:::(Info) (🚀 Quick Example: Deleting a feed with pgutil)
This example deletes the feed `myNugetFeed`
```
pgutil feed delete --feed=myNugetFeed
```
:::

## Command Specification (CLI)
The `feed delete` command is used to delete a feed.

The `--feed` option is always required.

**Deleting a feed** requires the feed name (e.g. `myNugetFeed`):
```
pgutil feed delete --feed=myNugetFeed
```

## HTTP Request Specification
To delete a specified feed simply `DELETE` to the URL with the `feed` name and an [appropriate API Key](/docs/proget/reference-api/feeds/proget-api-feeds#authentication).

```
DELETE /api/management/feeds/delete/«feed-name»
```

## HTTP Response Specification

| Response | Details |
|---|---|
| **200 (Success)** | will delete the specified `feed` |
| **400 (Invalid Input)** | indicates invalid or missing properties in the request; the body will provide some details as text |
| **403 (Unauthorized API Key)** | indicates a [missing, unknown, or unauthorized API Key](/docs/proget/reference-api/feeds/proget-api-feeds#authentication); the body will be empty |
| **404 (Feed Not Found)** | indicates that the specified `feed` does not exist |