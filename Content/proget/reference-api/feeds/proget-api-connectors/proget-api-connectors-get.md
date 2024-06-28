---
title: "Get Connector"
order: 2
---

*Get Connector* is available as both a `pgutil` command and an HTTP Request, and will return a single [ProgetConnector](/docs/proget/reference-api/feeds/proget-api-connectors#connector-object) object for the specified connector.

:::(Info) (🚀 Quick Example: Getting a connector with pgutil)
This example returns the connector `myNugetConnector`
```
pgutil connectors get --name=myNugetConnector
```
:::

## Command Specification (CLI)
The `connectors get` command set is used return a single connector.

The `--name` option is always required. 

**Getting a connector** requires the connector name (e.g. `MyNugetConnector`):
```
pgutil connectors get --name=myNugetConnector
```
This will return the specified connector, its type and its URL:

```
myNugetConnector (nuget) https://proget.connector.local
```

## HTTP Request Specification
To return a specified connector, simply `GET` to the URL with the name of the `connector` and an [appropriate API Key](/docs/proget/reference-api/feeds/proget-api-connectors#authentication).

```
GET /api/management/connectors/get/«connector-name»
```

## HTTP Response Specification
A successful (`200`) response body will contain a single [ProGetConnector](/docs/proget/reference-api/feeds/proget-api-connectors#connector-object) object. For example, to requesting a `nuget` connector with the name `myConnector`, the request would return:
```
GET /api/management/connectors/get/nuget.org

{
  "name": "myNugetConnector",
  "url": "https://proget.connector.local",
  "feedType": "nuget",
  "username": "jsmith",
  "password": "pass123",
  "timeout": 60,
  "filters": ["filter1", "filter2"],
  "metadataCacheEnabled": true,
  "metadataCacheMinutes": 30,
  "metadataCacheCount": 100
}
```

| Response | Details |
|---|---|
| **200 (Success)** | body will contain a [ProGetConnector](/docs/proget/reference-api/feeds/proget-api-connectors#connector-object) object |
| **400 (Invalid Input)** | indicates invalid or missing properties in the request; the body will provide some details as text |
|  **403 (Unauthorized API Key)** | indicates a [missing, unknown, or unauthorized API Key](/docs/proget/reference-api/feeds/proget-api-connectors#authentication); the body will be empty |
| **404 (Connector Not Found)** | indicates that the specified `connector` does not exist |