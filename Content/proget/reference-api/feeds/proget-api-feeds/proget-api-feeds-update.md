---
title: "Update Feed"
order: 4
---

*Update Feed* is available as both a `pgutil` command and an HTTP Request, and will update a specified feed using the [Feed](/docs/proget/reference-api/feeds/proget-api-feeds#feed-object) object properties defined in the request body. This endpoint supports partial updating by only updating the properties that are supplied in the request. 

:::(Info) (🚀 Quick Example: Updating a feed with Curl)
This example updates a nuget feed named `myNugetFeed`, authenticating with the API key `abc12345`, updating the storage path to `C:\ProgramData\ProGet\Packages\.nugetv2\F1`:

````
curl -X POST -H "Content-Type: application/json" -H "X-ApiKey: proget" -d "{\"packagesPath\": \"C:\\ProgramData\\ProGet\\Packages\\.nugetv2\\F1\"}" "http://54.168.224.223:8624/api/management/feeds/update/nuget-approved"
````
:::

## Command Specification (CLI)
:::(Info) (🚧 Coming Soon 🚧)
The `feeds update` command is coming soon. Similar to the HTTP Request, it will update a specified feed.
:::

## HTTP Request Specification
To update a feed, simply `POST` to the URL with the `feed` name, an [appropriate API Key](/docs/proget/reference-api/feeds/proget-api-feeds#authentication) and a [Feed](/docs/proget/reference-api/feeds/proget-api-feeds#feed-object) object as the request body.

:::(info)
When updating, any properties omitted will keep their existing values. Updating a property with an array value will overwrite the existing value. For example, if a feed has `connectors: ["A", "B"]`, updating with `connectors: ["C"]` will remove connectors "A" and "B" from the feed, keeping only "C". 

To append values, perform a [Get Feed](/docs/proget/reference-api/feeds/proget-api-feeds/proget-api-feeds-get) first, then append the existing values returned in the "Update Feed" request. For example, GET the values of connectors "A" and "B", and add them along with "C", supplying `connectors: ["A", "B", "C"]`in the update request.
:::

```
POST /api/management/feeds/update/«feed-name»
```

**Updating a NuGet feed's description** requires the `feed` name (e.g. `myNugetFeed`) and a [Feed](/docs/proget/reference-api/feeds/proget-api-feeds#feed-object) object as the body.

```
POST /api/management/feeds/update/myNugetFeed

{
  "description": "This feed serves as a proxy for Inedo SDK extensions."
}
```

**Updating a NuGet feed's retention rules** requires the `feed` name (e.g. `myNugetFeed`) and a [Feed](/docs/proget/reference-api/feeds/proget-api-feeds#feed-object) object as the body.

This example configures the `myNugetFeed` feed with the following retention rules:
 - delete cached connector packages that haven't been downloaded in the last month
 - delete all but the latest 10 `InedoLib`, `InedoIcons`, and`Inedo.LicenseCreator` package versions that haven't been downloaded or used within the past 3 months
 - delete all but the latest 5 pre-release packages that haven't been downloaded in the last month

```
POST /api/management/feeds/update/MyNugetFeed

{
  "retentionRules": [
      {
        "keepUsedWithinDays": 30,
        "deleteCached": true
      },
      {
        "keepVersionsCount": 10,
        "keepUsedWithinDays": 90,
        "deletePackageIds": [
          "InedoLib",
          "InedoIcons",
          "Inedo.LicenseCreator"
        ],
        "sizeExclusive": true,
        "triggerDownloadCount": 1
      },
      {
        "deletePrereleaseVersions": true,
        "keepVersionsCount": 5,
        "keepUsedWithinDays": 30
      }
    ]
}
```

## HTTP Response Specification
A successful (`200`) response body will contain an updated [Feed](/docs/proget/reference-api/feeds/proget-api-feeds#feed-object) object. For example, when updating a NuGet feed `myNugetFeed` with a new description, this returns:

```
{
  "name": "myNugetFeed",
  "alternateNames": [],
  "feedType": "nuget",
  "description": "This feed serves as a proxy for Inedo SDK extensions.",
  "active": true,
  "cacheConnectors": true,
  "allowUnknownLicenses": true,
  "allowedLicenses": [],
  "blockedLicenses": [],
  "symbolServerEnabled": false,
  "stripSymbols": false,
  "stripSource": false,
  "endpointUrl": "http://proget.corp.local/nuget/nuget/v3/index.json",
  "connectors": ["nuget.org"],
  "vulnerabilitySources": [],
  "retentionRules": [],
  "packageFilters": {},
  "packageAccessRules": {},
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
| **200 (Success)** | response body contains an updated [Feed](/docs/proget/reference-api/feeds/proget-api-feeds#feed-object) object|
| **400 (Invalid Input)** | indicates invalid or missing properties in the request; the body will provide some details as text |
| **403 (Unauthorized API Key)** | indicates a [missing, unknown, or unauthorized API Key](/docs/proget/reference-api/feeds/proget-api-feeds#authentication); the body will be empty |
| **404 (Feed Not Found)** | indicates that the specified `license` does not exist |


## Sample Usage Scripts

### Add a connector to a feed (Powershell)
This script will add a connector `nuget.org`, to a feed `nuget-unapproved`. If the specified connector already exists, no changes will be made.

Only connectors that already exist in the ProGet instance may be added. See the [Create Connector](/docs/proget/reference-api/feeds/proget-api-connectors/proget-api-connectors-create) endpoint to add a new connector. 

This script can be edited to work with other [Feed](/docs/proget/reference-api/feeds/proget-api-feeds#feed-object) object properties that are array types.

```powershell
$baseUrl = "https://proget.corp.local"
$apiKey = "a1b2c3d4e5"
$feedName = "nuget-unapproved"
$key = "connectors"
$newFeedValue = "nuget.org"

function Get-FeedInfo {
    param ([string]$feedName)
    $url = "$baseUrl/api/management/feeds/get/$($feedName)"
    $headers = @{"X-ApiKey" = $apiKey}
    $response = Invoke-RestMethod -Uri $url -Headers $headers -Method Get
    return $response
}

function Update-FeedInfo {
    param ([string]$feedName,[string]$newValue)
    $url = "$baseUrl/api/management/feeds/update/$($feedName)"
    $headers = @{"X-ApiKey" = $apiKey}
    $currentFeedInfo = Get-FeedInfo -feedName $feedName
    $keyToChange = $currentFeedInfo.$key

    if ($keyToChange -notcontains $newValue) {
        $keyToChange += $newValue
        $body = @{$key = $keyToChange}
        $response = Invoke-RestMethod -Uri $url -Headers $headers -Method Post -Body ($body | ConvertTo-Json) -ContentType "application/json"
        Write-Host "'$($newFeedValue)' added successfully to $($key)"
    } else {
        Write-Host "No changes needed. Value '$($newValue)' already exists in $($key)."
    }
}

Update-FeedInfo -feedName $feedName -newValue $newFeedValue
```

### Delete a connector from a feed (Powershell)

This script will delete a connector `nuget.org`, from a feed `nuget-unapproved`. If the specified connector does not exist, no changes will be made.

This script can be edited to work with other [Feed](/docs/proget/reference-api/feeds/proget-api-feeds#feed-object) object properties that are array types.

```powershell
$baseUrl = "https://proget.corp.local"
$feedName = "nuget-unapproved"
$apiKey = "a1b2c3d4e5"
$key = "connectors"
$valueToDelete = "nuget.org"

$feedUrl = "$baseUrl/api/management/feeds/get/$feedName"
$feedDetails = Invoke-RestMethod -Uri $feedUrl -Headers @{ "X-ApiKey" = $apiKey }

if ($feedDetails.$key -contains $valueToDelete) {
    $feedDetails.$key = $feedDetails.$key -ne $valueToDelete
    $updateData = @{$key = $feedDetails.$key}
    $updateUrl = "$baseUrl/api/management/feeds/update/$feedName"
    Invoke-RestMethod -Uri $updateUrl -Headers @{ "X-ApiKey" = $apiKey; "Content-Type" = "application/json" } -Method Post -Body ($updateData | ConvertTo-Json)

    Write-Host "Value '$valueToDelete' deleted from $key."
} else {
    Write-Host "Value '$valueToDelete' not found in $key. No changes made."
}
```