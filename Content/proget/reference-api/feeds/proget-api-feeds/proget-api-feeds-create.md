---
title: "Create Feed"
order: 1
---

*Create Feed* is available as both a `pgutil` command and an HTTP Request, and will create a feed, with the properties defined by a [ProgetFeed](/docs/proget/reference-api/feeds/proget-api-feeds#feed-object) object in the body of the request.

:::(Info) (🚀 Quick Example: Creating a feed with pgutil)
This example will create a `NuGet` feed `myNugetFeed`
```
pgutil feeds create --name=myNugetFeed --type=NuGet
```
:::

## Command Specification (CLI)
The `feeds create` command is used to create a feed.

The `--name` and `--type` options are always required. 

**Creating a feed** requires the feed name (e.g. `MyNugetFeed`) and type (e.g. `NuGet`):
```
pgutil feeds create --name=myNugetFeed --type=NuGet
```

## HTTP Request Specification
To create a feed, simply `POST` to the URL with an [appropriate API Key](/docs/proget/reference-api/feeds/proget-api-feeds#authentication) and a [ProgetFeed](/docs/proget/reference-api/feeds/proget-api-feeds#feed-object) object as the request body.

```
POST/PUT /api/management/feeds/create
```

## HTTP Response Specification
A successful (`200`) response body will contain a single [ProgetFeed](/docs/proget/reference-api/feeds/proget-api-feeds#feed-object) object. For example, creating a new `nuget` feed, with the name `myNugetFeed` returns:

```
POST /api/management/feeds/create

{
  "name": "myNugetFeed",
  "alternateNames": [],
  "feedType": "nuget",
  "active": true,
  "cacheConnectors": true,
  "symbolServerEnabled": false,
  "stripSymbols": false,
  "stripSource": false,
  "endpointUrl": "https://proget.corp.local/nuget/myNugetFeed/v3/index.json",
  "connectors": [],
  "retentionRules": [],
  "variables": {},
  "canPublish": true,
  "packageStatisticsEnabled": false,
  "restrictPackageStatistics": false,
  "deploymentRecordsEnabled": true,
  "usageRecordsEnabled": true,
  "vulnerabilitiesEnabled": false,
  "licensesEnabled": false,
  "useWithProjects": true
}
```

| Response | Details |
|---|---|
| **200 (Success)** | a `feed` will be created, the body will contain a [ProgetFeed](/docs/proget/reference-api/feeds/proget-api-feeds#feed-object) object |
| **400 (Invalid Input)** | indicates invalid or missing properties in the request; the body will provide some details as text |
|  **403 (Unauthorized API Key)** | indicates a [missing, unknown, or unauthorized API Key](/docs/proget/reference-api/feeds/proget-api-feeds#authentication); the body will be empty |