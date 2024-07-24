---
title: "Get Feed Storage"
order: 6
---

*Get Feed Storage* is available as both a `pgutil` command and an HTTP Request, and will return a [FeedStorageConfiguration](/docs/proget/reference-api/feeds/proget-api-feeds#storage-object) object, or properties for the specified feed's storage.

:::(Info) (🚀 Quick Example: Getting a feed's storage properties with pgutil)
This example returns storage properties of the feed `myNugetFeed`
```
pgutil feeds storage info --feed=myNugetFeed
```

## Command Specification (CLI)
The `feeds storage info` command is used to get storage properties of the specified feed.

The `--feed` option is always required. 

**Getting a feed's storage properties** requires the feed name (e.g. `myNugetFeed`):

```
pgutil feeds storage info --feed=myNugetFeed
```

Example output:

```
Type: disk
StoragePath: <default>
```

## HTTP Request Specification
To return a specified feed's storage properties, simply `GET` to the URL with the name of the `feed` and an [appropriate API Key](/docs/proget/reference-api/feeds/proget-api-feeds#authentication).

```
GET /api/storage?feed=«feed-name»
```

## HTTP Response Specification
A successful (`200`) response body will contain a single [FeedStorageConfiguration](/docs/proget/reference-api/feeds/proget-api-feeds#storage-object) object. For example, to requesting storage properties a feed with the name `myNugetFeed`, the request would return:

```
GET /api/storage?feed=myNugetFeed

{

}
```

| Response | Details |
|---|---|
| **200 (Success)** | body will contain a [FeedStorageConfiguration](/docs/proget/reference-api/feeds/proget-api-feeds#storage-object) object |
| **400 (Invalid Input)** | indicates invalid or missing properties in the request; the body will provide some details as text |
| **403 (Unauthorized API Key)** | indicates a [missing, unknown, or unauthorized API Key](/docs/proget/reference-api/feeds/proget-api-feeds#authentication); the body will be empty |
| **404 (Feed Not Found)** | indicates that the specified `feed` does not exist |