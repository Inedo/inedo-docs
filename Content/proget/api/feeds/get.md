---
title: "Get Feed"
order: 2
---

*Get Feed* is available as both a `pgutil` command and an HTTP Request, and will return a single [ProgetFeed](/docs/proget/api/feeds#feed-object) object or a list of properties for the specified feed.

:::(Info) (🚀 Quick Example: Getting a feed with pgutil)
This example returns properties of the feed `myNugetFeed`:

```bash
pgutil feeds properties list --feed=myNugetFeed
```
:::

## Command Specification (CLI)
The `feeds properties list` command is used to return properties of a single feed.

The `--feed` option is always required. 

**Getting a feed** requires the feed name (e.g. `myNugetFeed`):

```bash
pgutil feeds properties list --feed=myNugetFeed
```

This will return properties of the specified feed:

```plaintext
alternateNames=*not set*
feedType=nuget
active=true
dropPath=*not set*
endpointUrl=https://proget.corp.local/nuget/myNugetFeed/v3/index.json
connectors=nuget.org
canPublish=true
vulnerabilitiesEnabled=true
```

## HTTP Request Specification
To return a specified feed, simply `GET` to the URL with the name of the `feed` and an [appropriate API Key](/docs/proget/api/feeds#authentication).

```plaintext
GET /api/management/feeds/get/«feed-name»
```

## HTTP Response Specification
A [ProgetFeed](/docs/proget/api/feeds#feed-object) object (see [ProGetFeed.cs](https://github.com/Inedo/pgutil/blob/thousand/Inedo.ProGet/ProGetFeed.cs)) will be returned on a successful `200` response. A `403` response indicates a [missing, unknown, or unauthorized API Key](/docs/proget/api/security#authentication).