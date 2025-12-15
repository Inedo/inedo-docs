---
title: "Get Feed Storage"
order: 6
---

*Get Feed Storage* is available as both a `pgutil` command and an HTTP Request, and will return a [FeedStorageConfiguration](/docs/proget/api/feeds#storage-object) object, or properties for the specified feed's storage.

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
To return a specified feed's storage properties, simply `GET` to the URL with an [appropriate API Key](/docs/proget/api/feeds#authentication) and a [ProgetFeed](/docs/proget/api/feeds#feed-object) object (see [ProGetFeed.cs](https://github.com/Inedo/pgutil/blob/thousand/Inedo.ProGet/ProGetFeed.cs)) as the request body.

```plaintext
GET /api/storage?feed=«feed-name»
```

## HTTP Response Specification
A [FeedStorageConfiguration](/docs/proget/api/feeds#storage-object) object (see [FeedStorageConfiguration.cs](https://github.com/Inedo/pgutil/blob/thousand/Inedo.ProGet/FeedStorageConfiguration.cs)) will be returned on a successful `200` response. A `403` response indicates a [missing, unknown, or unauthorized API Key](/docs/proget/api/security#authentication).