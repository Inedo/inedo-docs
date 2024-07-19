---
title: "Update Feed"
order: 4
---

*Update Feed* is available as both a `pgutil` command and an HTTP Request, and will update a specified feed using the [Feed](/docs/proget/reference-api/feeds/proget-api-feeds#feed-object) object properties defined in the request body. This endpoint supports partial updating by only updating the properties that are supplied in the request. 

:::(Info) (🚀 Quick Example: Updating a feed's drop path with pgutil)
This example adds a drop path `c:\bulk-import` to a feed `myNugetFeed`
```
pgutil feeds properties set --feed=myNugetFeed --property=dropPath --value=c:\bulk-import
```
:::

## Command Specification (CLI)
The `feeds properties set` command is used to update properties of a single feed.

The `--property` and `--value` options are always required. The `--feed` option is required if there is no default feed configured. The following properties can be set with this command:

| Property | Description | 
| --- | --- | 
| `alternateNames` | Provide multiple endpoint URLs for a feed, useful when renaming feeds to keep backwards compatibility with old names. | 
| `dropPath`  | Disk path accessible to ProGet's service where packages may be added to the feed | 
| `active` | Indicates whether the feed is active (`true`) or disabled (`false`) | 
| `connectors`  | Connectors used by the feed. Add multiple connectors by separating them with a comma (e.g. `nuget.org,pypyi.org`) | 
| `vulnerabilitiesEnabled` | Indicates whether vulnerability detection is enabled (`true`) or disabled (`false`) | 

**Updating a feed's drop path** requires the feed name (e.g. `myNugetFeed`), the property to be updated (e.g. `dropPath`), and the value to update this with (e.g. `c:\bulk-import`):
```
pgutil feeds properties set --feed=myNugetFeed --property=dropPath --value=c:\bulk-import
```

**Adding multiple connectors to a feed** requires the feed name (e.g. `myFeed`), the property to be updated (e.g. `connectors`), and the name of the connectors (e.g. `registry.npmjs.org` and `nuget.org`):
```
pgutil feeds properties set --feed=myFeed --property=connectors --value=registry.npmjs.org,nuget.org
```

**Disabling a feed** requires the feed name (e.g. `myPypiFeed`), the property to be updated (e.g. `active`), and the value to update this with (e.g. `false`):
```
pgutil feeds properties set --feed=myPypiFeed --property=active --value=false
```

### Updating Retention Rules

The `feeds retention` commands are used to update a feed's retention rules. 

There are four commands available (`create`, `list`, `update`, and `delete`), and each command has options that correspond to fields in the web UI. The `--feed` option is required in all commands if there is no default feed configured. The `--rule` option is required for the `update` and `delete` commands, and corresponds to the rule number, identified using the `list` command. (see [example](#list))

The following options are used with the `create`, `update`, and`delete` commands:

| Option | Description | 
| --- | --- | 
| --deleteCached | Consider only cached packages or all packages (either `true` or `false`) |
| --deletePackageIds | Comma separated list of packages subject for deletion |
| --deletePrereleaseVersions | Consider only prerelease versions or all versions (either `true` or `false`) |
| --deleteVersions | Comma separated list of versions subject for deletion |
| --keepConsumedWithinDays | Package used in builds must not have been used within this many days to qualify for deletion |
| --keepIfActivelyConsumed | Keeps packages that are actively consumed (either `true` or `false`)
| --keepPackageIds | Comma separated list of packages exempt from deletion |
| --keepPackageUsageRemovedDays | Keep packages with usage data removed only after this many days |
| --keepUsedWithinDays | Requested/downloaded packages must not have been used within this many days to qualify for deletion |
| --keepVersions | Comma separated list of versions exempt from deletion |
| --keepVersionsCount | Always keep this number of the most recent package versions |
| --sizeExclusive | Specifies if the sizeTrigger value is an exclusive boundary (either `true` or `false`) |
| --sizeTrigger | Minimum size (in kb) of feed required to trigger retention run |
| --triggerDownloadCount | Trigger download count |

**Creating a retention rule for a feed** requires the feed name (e.g. `myNugetFeed`) and the retention rule to be created (e.g.  `--deletePrereleaseVersions` as `true`):

:::(info)
One or more options can be selected. Creating a new retention rule will set property values to those specified in the command. All other properties will be set to "not set" with the exception of  `deleteCached`, `deletePrereleaseVersions`, `keepIfActivelyConsumed` and `sizeExclusive` which will be set as `false` by default.
:::

```
pgutil feeds retention create --feed=myNugetFeed --deletePrereleaseVersions=true
```

**Listing a feed's retention rules** requires the feed name (e.g. `myPypiFeed`):

```
pgutil feeds retention list --feed=myPypiFeed
```

<p id="list">Example output of listing retention rules:</p>

```
[1]
 deleteCached=true
 deletePackageIds=*not set*
 deletePrereleaseVersions=false
 deleteVersions=*not set*
 keepConsumedWithinDays=*not set*
 keepIfActivelyConsumed=false
 keepPackageIds=*not set*
 keepPackageUsageRemovedDays=*not set*
 keepUsedWithinDays=*not set*
 keepVersionsCount=*not set*
 sizeExclusive=false
 sizeTrigger=*not set*
 triggerDownloadCount=*not set*
[2]
 deleteCached=false
 deletePackageIds=*not set*
 deletePrereleaseVersions=true
 deleteVersions=*not set*
 keepConsumedWithinDays=*not set*
 keepIfActivelyConsumed=false
 keepPackageIds=*not set*
 keepPackageUsageRemovedDays=*not set*
 keepUsedWithinDays=*not set*
 keepVersionsCount=*not set*
 sizeExclusive=false
 sizeTrigger=*not set*
 triggerDownloadCount=*not set*
```

**Updating retention rules for a feed** requires the feed name (e.g. `myNugetFeed`) and the existing retention rule properties to be updated (e.g.  `deleteCached` as `true` and `keepVersionsCount` as `5`):

```
pgutil feeds retention update --feed=myNugetFeed --deleteCached=true --keepVersionsCount=5
```

:::(info)
One or more options can be selected. This command will update the value of any existing set properties, or create a value if it was previously "not set". The command will only update the specified properties, while retaining the values of any other existing properties.
:::

Example update (Before):
```
 deleteCached=false
 deletePackageIds=*not set*
 deletePrereleaseVersions=false
 deleteVersions=*not set*
 keepConsumedWithinDays=*not set*
 keepIfActivelyConsumed=false
 keepPackageIds=*not set*
 keepPackageUsageRemovedDays=*not set*
 keepUsedWithinDays=*not set*
 keepVersionsCount=*not set*
 sizeExclusive=false
 sizeTrigger=*not set*
 triggerDownloadCount=*not set*
 ```
Example update (After):
```
 deleteCached=true
 deletePackageIds=*not set*
 deletePrereleaseVersions=false
 deleteVersions=*not set*
 keepConsumedWithinDays=*not set*
 keepIfActivelyConsumed=false
 keepPackageIds=*not set*
 keepPackageUsageRemovedDays=*not set*
 keepUsedWithinDays=*not set*
 keepVersionsCount=5
 sizeExclusive=false
 sizeTrigger=*not set*
 triggerDownloadCount=*not set*
 ```


**Deleting a retention rule for a feed** requires the feed name (e.g. `myNpmFeed`) and the retention rules number (e.g. `4`) from the `list` command:
```
pgutil feeds retention delete --feed=myNpmFeed --rule=4
```

## HTTP Request Specification
To update a feed, simply `POST` to the URL with the `feed` name, an [appropriate API Key](/docs/proget/reference-api/feeds/proget-api-feeds#authentication) and a [Feed](/docs/proget/reference-api/feeds/proget-api-feeds#feed-object) object as the request body.

:::(info)
When updating, any properties omitted will keep their existing values. Updating a property with an array value will overwrite the existing value. For example, if a feed has `connectors: ["A", "B"]`, updating with `connectors: ["C"]` will remove connectors "A" and "B" from the feed, keeping only "C". 

To append values, perform a [Get Feed](/docs/proget/reference-api/feeds/proget-api-feeds/proget-api-feeds-get) first, then append the existing values returned in the "Update Feed" request. For example, GET the values of connectors "A" and "B", and add them along with "C", supplying `connectors: ["A", "B", "C"]`in the update request.
:::

```
POST /api/management/feeds/update/«feed-name»
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
  "connectors": ["nuget.org", "proget.connector.local"],
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