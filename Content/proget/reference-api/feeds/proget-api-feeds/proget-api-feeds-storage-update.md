---
title: "Update Feed Storage"
order: 7
---

---
title: "List Feeds"
order: 3
---

*List Feeds* is available as both a `pgutil` command and an HTTP Request, and will return an array of [ProgetFeed](/docs/proget/reference-api/feeds/proget-api-feeds#feed-object) objects, describing all existing feeds.

:::(Info) (🚀 Quick Example: Listing feeds with pgutil)
This example lists all existing feeds
```
pgutil feeds list
```
:::

## Command Specification (CLI)
The `feeds list` command is used to list all existing feeds.

**Listing all feeds** does not require any additional options:
```
pgutil feeds list
```
This will return a list of feeds:

```
approved-nuget (nuget)
approved-pypi (pypi)
assets (asset)
public-debian (debian)
public-gems (rubygems)
unapproved-nuget (nuget)
unapproved-pypi (pypi)
```

## HTTP Request Specification
To list all existing feeds, simply `GET` to the URL with an [appropriate API Key](/docs/proget/reference-api/feeds/proget-api-feeds#authentication).

```
GET /api/management/feeds/list
```

## HTTP Response Specification
A successful (`200`) response body will contain an array of [ProgetFeed](/docs/proget/reference-api/feeds/proget-api-feeds#feed-object) objects. For example:

```
GET /api/management/feeds/list

[
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
    },
    { ... } // remaining feeds
]
```

| Response | Details |
|---|---|
| **200 (Success)** | will delete the specified `feed` |
| **400 (Invalid Input)** | indicates invalid or missing properties in the request; the body will provide some details as text |
| **403 (Unauthorized API Key)** | indicates a [missing, unknown, or unauthorized API Key](/docs/proget/reference-api/feeds/proget-api-feeds#authentication); the body will be empty |
| **404 (Feed Not Found)** | indicates that the specified `feed` does not exist |