---
title: "Get Feed Storage"
order: 6
---

*Get Feed Storage* is available as both a `pgutil` command and an HTTP Request, and will return properties for the specified feed's storage.

:::(Info) (🚀 Quick Example: Getting a feed's storage properties with pgutil)
This example returns storage properties of the feed `myNugetFeed`:

```bash
pgutil feeds storage info --feed=myNugetFeed
```
:::

## Command Specification (CLI)
The `feeds storage info` command is used to get storage properties of the specified feed.

The `--feed` option is always required. 

**Getting a feed's storage properties** requires the feed name (e.g. `myNugetFeed`):

```bash
pgutil feeds storage info --feed=myNugetFeed
```

Example output:

```plaintext
Type: disk
StoragePath: <default>
```

## HTTP Request Specification
To return a specified feed's storage properties, simply `GET` to the following URL with a feed name and an [appropriate API Key](/docs/proget/api/feeds#authentication).

```plaintext
GET /api/storage?feed=«feed-name»
```

## HTTP Response Specification
A `FeedStorageConfiguration` (see [FeedStorageConfiguration.cs](https://github.com/Inedo/pgutil/blob/thousand/Inedo.ProGet/FeedStorageConfiguration.cs)) object will be returned on a successful `200` response. A `403` response indicates a [missing, unknown, or unauthorized API Key](/docs/proget/api/security#authentication).
