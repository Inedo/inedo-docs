---
title: "Get Feed"
order: 2
---

*Get Feed* is available as both a `pgutil` command and an HTTP Request, and will return a single [ProgetFeed](/docs/proget/reference-api/feeds/proget-api-feeds#feed-object) object for the specified feed.

:::(Info) (🚀 Quick Example: Getting a feed with pgutil)
This example returns the feed `myNugetFeed`
```
pgutil feeds get --name=myNugetFeed
```
:::

## Command Specification (CLI)
The `feeds get` command set is used to return a single feed.

The `--name` option is always required. 

**Getting a feed** requires the feed name (e.g. `myNugetFeed`):
```
pgutil feeds get --name=myNugetFeed
```
This will return the specified feed:
```
myNugetFeed
```

## HTTP Request Specification
To return a specified feed, simply `GET` to the URL with the name of the `feed` and an [appropriate API Key](/docs/proget/reference-api/feeds/proget-api-feeds#authentication).

```
GET /api/management/feeds/get/«feed-name»
```

## HTTP Response Specification
A successful (`200`) response body will contain a single [ProgetFeed](/docs/proget/reference-api/feeds/proget-api-feeds#feed-object) object. For example, to requesting a feed with the name `myNugetFeed`, the request would return:
```
GET /api/management/feeds/get/myNugetFeed

{
   "name":"myNugetFeed",
   "alternateNames":[],
   "feedType":"nuget",
   "active":true,
   "cacheConnectors":true,
   "symbolServerEnabled":false,
   "stripSymbols":false,
   "stripSource":false,
   "endpointUrl":"https://proget.corp.local/nuget/public-nuget/v3/index.json",
   "connectors":["nuget.org"],
   "retentionRules":[],
   "variables":{},
   "canPublish":true,
   "packageStatisticsEnabled":false,
   "restrictPackageStatistics":false,
   "deploymentRecordsEnabled":true,
   "usageRecordsEnabled":true,
   "vulnerabilitiesEnabled":true,
   "licensesEnabled":true,
   "useWithProjects":true
}
```

| Response | Details |
|---|---|
| **200 (Success)** | body will contain a [ProgetFeed](/docs/proget/reference-api/feeds/proget-api-feeds#feed-object) object |
| **400 (Invalid Input)** | indicates invalid or missing properties in the request; the body will provide some details as text |
|  **403 (Unauthorized API Key)** | indicates a [missing, unknown, or unauthorized API Key](/docs/proget/reference-api/feeds/proget-api-feeds#authentication); the body will be empty |
| **404 (Feed Not Found)** | indicates that the specified `feed` does not exist |

