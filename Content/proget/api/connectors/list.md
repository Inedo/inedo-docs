---
title: "List Connectors"
order: 3
---

*List Connectors* is available as both a `pgutil` command and an HTTP Request, and will list all existing [connectors](/docs/proget/feeds/connector-overview).

:::(Info) (🚀 Quick Example: Listing connectors with pgutil)
This example lists all existing connectors:

```bash
pgutil connectors list
```
:::

## Command Specification (CLI)
The `connectors list` command is used to list all existing [connectors](/docs/proget/feeds/connector-overview).

:::(info)(📄 Note: pgutil Sources)
Source options must also be specified unless you have the "Default" source configured. See [Working with Sources](/docs/proget/api/pgutil#sources) to learn more.
:::

**Listing all connectors** does not require any additional options:

```bash
pgutil connectors list
```
This will return a list of connectors, their type, and their URL:

```plaintext
myNugetConnector (nuget) https://proget.connector.local
nuget.org (nuget) https://api.nuget.org/v3/index.json
pypi.org (pypi) https://pypi.org/
registry.npmjs.org (npm) https://registry.npmjs.org
rubygems.org (rubygems) https://api.rubygems.org/
```

## HTTP Request Specification
To list all existing connectors, simply `GET` to the URL with an [appropriate API Key](/docs/proget/api/connectors#authentication).

```plaintext
GET /api/management/connectors/list
```

## HTTP Response Specification
An array of `Connector` (see [ProGetConnector.cs](https://github.com/Inedo/pgutil/blob/thousand/Inedo.ProGet/ProGetConnector.cs)) objects will be returned on a successful `200` response. A `403` response indicates a [missing, unknown, or unauthorized API Key](/docs/proget/api/connectors#authentication).

## Sample Usage Scripts

### List all connectors (Powershell)
This script will print a list of all existing connectors, their feed types and URLs. 

```powershell
$apiUrl = "https://proget.corp.local/api/management/connectors/list"
$apiKey = "abc12345"

$headers = @{"X-ApiKey" = $apiKey}

$response = Invoke-RestMethod -Uri $apiUrl -Method Get -Headers $headers

foreach ($connector in $response) {
    $connectorName = $connector.name
    $connectorType = $connector.feedType
    $connectorUrl = $connector.url
    $formattedConnector = "{0} ({1}) {2}" -f $connectorName, $connectorType, $connector.url
    Write-Host $formattedConnector
}
```

#### Example Output:

```powershell
nuget.org (nuget) https://api.nuget.org/v3/index.json
registry.npmjs.org (npm) https://registry.npmjs.org
...
...
```
