---
title: "Update Connector"
order: 4
---

*Update Connector* is available as both a `pgutil` command and an HTTP Request, and will update a specified [connector](/docs/proget/feeds/connector-overview). This endpoint supports partial updating by only updating the properties that are supplied in the request. 

:::(Info) (🚀 Quick Example: Enabling a connector's metadata caching with pgutil)
This example will enable metadata caching for the connector `nuget.org`:

```bash
pgutil connectors properties set --connector=nuget.org --property=metadataCacheEnabled --value=true
```
:::

## Command Specification (CLI)
The `connectors properties set` command is used to updates properties of a single [connector](/docs/proget/feeds/connector-overview).

The `--property` and `--value` options are always required. The `--feed` option is required if there is no default feed configured. The following properties can be set with this command:

| Property | Description | 
| --- | --- | 
| `timeout` | The timeout (in seconds) used when making a request to the connector URL | 
| `metadataCacheEnabled`  | Indicates whether metadata caching is enabled (`true`) or disabled (`false`) | 
| `metadataCacheCount` | The number of URL-specific metadata requests cached by ProGet | 
| `metadataCacheMinutes`  | The number of minutes a connector metadata request to a specific URL is cached by ProGet  | 

:::(info)(📄 Note: pgutil Sources)
Source options must also be specified unless you have the "Default" source configured. See [Working with Sources](/docs/proget/api/pgutil#sources) to learn more.
:::
                                      
**Enabling a connector's Metadata Caching** requires the connector name (e.g. `nuget.org`), the property to be updated (e.g. `metadataCacheEnabled`), and the value to update this with (e.g. `true`):

```bash
pgutil connectors properties set --connector=nuget.org --property=metadataCacheEnabled --value=true
```

**Updating a connector's timeout** requires the connector name (e.g. `registry.npmjs.org`), the property to be updated (e.g. `timeout`), and the timeout duration in seconds (e.g. `30`):

```bash
pgutil connectors properties set --connector=registry.npmjs.org --property=timeout --value=30
```

### Updating Filters
The `connectors filters` command is used to updates [filters](/docs/proget/feeds/connector-overview#connector-filters) of a single [connector](/docs/proget/feeds/connector-overview).

There are three commands available (`add`, `list`, and `remove`), and each command has options that correspond to fields in the web UI. 

The `--connector` option is required for all commands. The `--filter` option is required for the `add` and `remove` commands.

:::(info)
When creating filters, terms written as `name` will be allowed, but terms prefixed with an exclamation mark, like `!name` will be blocked
:::

**Adding a filter to block** requires the connector name (e.g. `nuget.org`) and the filter (e.g. `!Jquery`):

```bash
pgutil connectors filters add --connector=nuget.org --filter=!JQuery
```

**Adding a filter to allow** requires the connector name (e.g. `registry.npmjs.org`) and the filter (e.g. `Microsoft.*`):

```bash
pgutil connectors filters add --connector=registry.npmjs.org --filter=Microsoft.*
```

**Listing all filters** requires the connector name (e.g. `registry.npmjs.org`):

```bash
pgutil connectors filters list --connector=registry.npmjs.org
```

Example output:

```bash
Microsoft.*
!lodash
!event-stream
```

**Removing a filter** requires the connector name (e.g. `pypi.org`), and the filter to remove (e.g. `!simplejson`):

```bash
pgutil connectors filters remove --connector=pypi.org --filter=!simplejson
```

## HTTP Request Specification
To update a connector, simply `POST` to the following URL with the `connector` name, an [appropriate API Key](/docs/proget/api/connectors#authentication) and a `Connector` (see [ProGetConnector.cs](https://github.com/Inedo/pgutil/blob/thousand/Inedo.ProGet/ProGetConnector.cs)) object as the request body.

:::(info)
When updating, any properties omitted will keep their existing values. Updating a property with an array value will overwrite the existing value. For example, if a connector has `filters: ["A", "B"]`, updating with `filters: ["C"]` will remove filters "A" and "B" from the connector, keeping only "C". 

To append values, perform a [Get Connector](/docs/proget/api/connectors/get) first, then append the existing values returned in the "Update Connector" request. For example, GET the values of filters "A" and "B", and add them along with "C", supplying `filters: ["A", "B", "C"]`in the update request.
:::

```plaintext
POST /api/management/connectors/update/«connector-name»
```

## HTTP Response Specification
An updated `Connector` object will be returned on a successful `200` response. A `403` response indicates a [missing, unknown, or unauthorized API Key](/docs/proget/api/connectors#authentication).

## Sample Usage Scripts

### Add a filter to a connector (Powershell)
This script will add a filter `!beta`, to a connector `nuget.org`. If the specified filter already exists, no changes will be made.

Note the `!` pre-fix indicates that it will be added with a "Block" behavior.

This script can be edited to work with other [Connector](/docs/proget/api/connectors#connector-object) object properties that are array types.

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

This script can be edited to work with other [Connector](/docs/proget/api/connectors#connector-object) object properties that are array types.

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
