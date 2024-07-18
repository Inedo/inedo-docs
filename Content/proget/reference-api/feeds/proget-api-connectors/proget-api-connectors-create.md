---
title: "Create Connector"
order: 1
---

*Create Connector* is available as both a `pgutil` command and an HTTP Request, and will
 create a [connector](/docs/proget/feeds/connector-overview), with the properties defined by a [ProgetConnector](/docs/proget/reference-api/feeds/proget-api-connectors#connector-object) object in the body of the request.

:::(Info) (🚀 Quick Example: Creating a connector with pgutil)
This example will create a `NuGet` connector `myNugetConnector`, connecting to `https://proget.connector.local`
```
pgutil connectors create --name=myNugetConnector --type=NuGet --url=https://proget.connector.local
```
:::

## Command Specification (CLI)
The `connectors create` command is used to create a [connector](/docs/proget/feeds/connector-overview).

The `--name`, `--type` and `--url` options are always required. 

**Creating a connector** requires the connector name (e.g. `myNugetConnector`), type (e.g. `NuGet`) and the URL (e.g. `https://proget.connector.local`):
```
pgutil connectors create --name=myNugetConnector --type=NuGet --url=https://proget.connector.local
```

## HTTP Request Specification
To create a [connector](/docs/proget/feeds/connector-overview), simply `POST` to the URL with an [appropriate API Key](/docs/proget/reference-api/feeds/proget-api-connectors#authentication) and a [ProgetConnector](/docs/proget/reference-api/feeds/proget-api-connectors#connector-object) object as the request body.

```
POST/PUT /api/management/connectors/create
```

## HTTP Response Specification
A successful (`200`) response body will contain a single [Connector](/docs/proget/reference-api/feeds/proget-api-connectors#connector-object) object. For example, creating a new `nuget` connector, with the name `myConnector` returns:

```
POST /api/management/connectors/create

{
  "name": "myNugetConnector",
  "url": "https://proget.connector.local",
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
| **200 (Success)** | a `connector` will be created, the body will contain a [ProgetConnector](/docs/proget/reference-api/feeds/proget-api-connectors#connector-object) object |
| **400 (Invalid Input)** | indicates invalid or missing properties in the request; the body will provide some details as text |
|  **403 (Unauthorized API Key)** | indicates a [missing, unknown, or unauthorized API Key](/docs/proget/reference-api/feeds/proget-api-connectors#authentication); the body will be empty |