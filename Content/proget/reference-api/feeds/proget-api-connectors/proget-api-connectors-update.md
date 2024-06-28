---
title: "Update Connector"
order: 4
---

*Update Connector* is available as both a `pgutil` command and an HTTP Request, and will update a specified connector using the [Connector](/docs/proget/reference-api/feeds/proget-api-connectors#connector-object) object properties defined in the request body. This endpoint supports partial updating by only updating the properties that are supplied in the request. 

:::(Info) (🚀 Quick Example: Updating a connector with Curl)
This example updates a nuget connector named `myNugetConnector`, authenticating with the API key `abc12345`, adding `http://proget.new-connector.local\` as a URL:

````
curl -X POST -H "Content-Type: application/json" -H "X-ApiKey: abc12345" -d "{\"url\": \"http://proget.new-connector.local\"}" "http://proget.corp.local/api/management/connectors/update/myNugetConnector"
````
:::

## Command Specification (CLI)
:::(Info) (🚧 Coming Soon 🚧)
The `connectors update` command is coming soon. Similar to the HTTP Request, it will update a specified connector.
:::

## HTTP Request Specification
To update a connector, simply `POST` to the URL with the `connector` name, an [appropriate API Key](/docs/proget/reference-api/feeds/proget-api-connectors#authentication) and a [Connector](/docs/proget/reference-api/feeds/proget-api-connectors#connector-object) object as the request body.

:::(info)
When updating, any properties omitted will keep their existing values. Updating a property with an array value will overwrite the existing value. For example, if a connector has `filters: ["A", "B"]`, updating with `filters: ["C"]` will remove filters "A" and "B" from the connector, keeping only "C". 

To append values, perform a [Get Connector](/docs/proget/reference-api/feeds/proget-api-connectors/proget-api-connectors-get) first, then append the existing values returned in the "Update Connector" request. For example, GET the values of filters "A" and "B", and add them along with "C", supplying `filters: ["A", "B", "C"]`in the update request.
:::

```
POST /api/management/connectors/update/«connector-name»
```

**Updating a NuGet connector's description** requires the `connector` name (e.g. `myNugetConnector`) and a [Connector](/docs/proget/reference-api/feeds/proget-api-connectors#connector-object) object as the body.

```
POST /api/management/connectors/update/myNugetConnector

{
  "url": "http://proget.new-connector.local",
}
```

## HTTP Response Specification
A successful (`200`) response body will contain an updated [Connector](/docs/proget/reference-api/feeds/proget-api-connectors#connector-object) object. For example, when updating a NuGet connector `myNugetConnector` with a new URL (`http://proget.new-connector.local`), this returns:

```
{
  "name": "MyNugetConnector",
  "url": "http://proget.new-connector.local",
  "feedType": "nuget",
  "timeout": 10,
  "metadataCacheEnabled": false,
  "metadataCacheMinutes": null,
  "metadataCacheCount": null,
  "filters": []
}
```

| Response | Details |
|---|---|
| **200 (Success)** | response body contains an updated [Connector](/docs/proget/reference-api/feeds/proget-api-connectors#connector-object) object|
| **400 (Invalid Input)** | indicates invalid or missing properties in the request; the body will provide some details as text |
| **403 (Unauthorized API Key)** | indicates a [missing, unknown, or unauthorized API Key](/docs/proget/reference-api/feeds/proget-api-connectors#authentication); the body will be empty |
| **404 (Connector Not Found)** | indicates that the specified `connector` does not exist |

## Sample Usage Scripts

### Add a filter to a connector (Powershell)
This script will add a filter `!beta`, to a connector `nuget.org`. If the specified filter already exists, no changes will be made.

Note the `!` pre-fix indicates that it will be added with a "Block" behavior.

This script can be edited to work with other [Connector](/docs/proget/reference-api/feeds/proget-api-connectors#connector-object) object properties that are array types.

```powershell
$baseUrl = "https://proget.corp.local"
$apiKey = "a1b2c3d4e5"
$connectorName = "nuget.org"
$key = "filters"
$newFeedValue = "!beta"

function Get-FeedInfo {
    param ([string]$connectorName)
    $url = "$baseUrl/api/management/connectors/get/$($connectorName)"
    $headers = @{"X-ApiKey" = $apiKey}
    $response = Invoke-RestMethod -Uri $url -Headers $headers -Method Get
    return $response
}

function Update-FeedInfo {
    param ([string]$connectorName, [string]$newValue)
    $url = "$baseUrl/api/management/connectors/update/$($connectorName)"
    $headers = @{"X-ApiKey" = $apiKey}
    $currentFeedInfo = Get-FeedInfo -connectorName $connectorName
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

Update-FeedInfo -connectorName $connectorName -newValue $newFeedValue
```

### Delete a filter from a connector (Powershell)

This script will delete a filter `alpha`, from a feed `nuget-unapproved`. If the specified filter does not exist, no changes will be made.

This script can be edited to work with other [Connector](/docs/proget/reference-api/feeds/proget-api-connectors#connector-object) object properties that are array types.

```powershell
$baseUrl = "https://proget.corp.local"
$connectorName = "nuget.org"
$apiKey = "a1b2c3d4e5"
$key = "filters"
$valueToDelete = "alpha"

$feedUrl = "$baseUrl/api/management/connectors/get/$connectorName"
$feedDetails = Invoke-RestMethod -Uri $feedUrl -Headers @{ "X-ApiKey" = $apiKey }

if ($feedDetails.$key -contains $valueToDelete) {
    $feedDetails.$key = $feedDetails.$key -ne $valueToDelete
    $updateData = @{$key = $feedDetails.$key}
    $updateUrl = "$baseUrl/api/management/connectors/update/$connectorName"
    Invoke-RestMethod -Uri $updateUrl -Headers @{ "X-ApiKey" = $apiKey; "Content-Type" = "application/json" } -Method Post -Body ($updateData | ConvertTo-Json)

    Write-Host "Value '$valueToDelete' deleted from $key."
} else {
    Write-Host "Value '$valueToDelete' not found in $key. No changes made."
}
```