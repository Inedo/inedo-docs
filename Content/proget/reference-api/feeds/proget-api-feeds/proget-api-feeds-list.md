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
The `feeds list` command set is used to list all existing feeds.

**Listing all feeds** does not require any additional options:
```
pgutil connectors list
```
This will return a list of feeds:

```
myNugetFeed
public-docker
public-nuget
public-npm
public-pypi
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
| **200 (Success)** | body will contain an array of [ProgetFeed](/docs/proget/reference-api/feeds/proget-api-feedsi#feed-object) objects |
|  **403 (Unauthorized API Key)** | indicates a [missing, unknown, or unauthorized API Key](/docs/proget/reference-api/feeds/proget-api-feeds#authentication); the body will be empty |

## Sample Usage Scripts

### List all feeds (Powershell)
This script will print a list of all existing feeds and their feed types. 

```powershell
$apiUrl = "https://proget.corp.local/api/management/feeds/list"
$apiKey = "abc12345"

$headers = @{"X-ApiKey" = $apiKey}

$response = Invoke-RestMethod -Uri $apiUrl -Method Get -Headers $headers

foreach ($feed in $response) {
    $feedName = $feed.name
    $feedType = $feed.feedType
    $formattedFeed = "{0} ({1})" -f $feedName, $feedType
    Write-Host $formattedFeed
}
```

#### Example Output:

```
internal-npm (npm)
nuget-approved (nuget)
nuget-unapproved (nuget)
public-files (asset)
public-npm (npm)
universal-feed (universal)
```
