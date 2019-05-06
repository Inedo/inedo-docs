---
title: Connector Health API
subtitle: Connector Health API
sequence: 300
keywords: proget, feeds, api, connectors
show-headings-in-nav: true
---

The Connector Health API Endpoints offer a simple mechanism for querying the health status of the connectors in ProGet.

:::{.attention .best-practice} 
This API is available starting in ProGet 5.2.
:::

#### Authentication

For security and simplicity, these endpoints require that a Connector Health [API Key](/support/documentation/proget/administration/security/api-keys) is created first. This key can be supplied to the API using any of the methods documented in the [API Key Usage](/support/documentation/proget/administration/security/api-keys#usage) section.

## Object Models {#object-models}

Each endpoint responds with entities as [JSON](http://json.org/) objects:

### Connector Health Data Model {#connector-health-model data-title="Connector Health Data Model"}

| Property | Details |
|---|---|
| `name`        | *`string`* - the unique name of the connector |
| `url`         | *`string`* - the URL of the connector  |
| `state`       | *`string`* - the health state of the connector, one of: `healthy`, `error`, or `unknown` |
| `errorMessage` | *`string`* - a specific error message if `state` is `error`; otherwise `null`  |
| `lastChecked` | *`datetime`* - an ISO 8601 UTC date when the connector health was last checked, or `null` if `state` is `unknown`  |

## Endpoint Specifications {#endpoints data-title="Endpoint Specifications"}

There are two endpoints available for querying connector health:

### All Connectors {#all}

##### URL Format:

{.info} GET/HEAD /api/connector-health

##### Response Codes:

|Code|Description|
|---|---|
| `200` | Success; response body contains an array of connector health objects | 
| `403` | Invalid/unauthorized API key, or impersonated user not granted `Feeds_Manage` privilege |

##### Example Response:

```
[
    {
        "id": 1,
        "name": "npmjs.org",
        "url": "https://registry.npmjs.org",
        "state": "healthy",
        "lastChecked": "2019-03-29T17:41:59.907+00:00"
    },
    {
        "id": 2,
        "name": "chocolatey.org",
        "url": "https://www.chocolatey.org/api/v2",
        "state": "healthy",
        "lastChecked": "2019-03-29T17:41:59.903+00:00"
    },
    {
        "id": 3,
        "name": "nuget.org",
        "url": "https://www.nuget.org/api/v2",
        "state": "unhealthy",
        "erroMessage": "Connection timed out."
        "lastChecked": "2019-03-29T17:41:55.05+00:00"
    },
    {....}

]
```

##### PowerShell Example:

```
Invoke-RestMethod `
  -Uri http://{proget-server}/api/connector-health `
  -Headers @{"X-ApiKey" = "secure123"}
```

### Single Connector {#single}

{.info} GET/HEAD /api/connector-health/**&laquo;connector-id&raquo;**

##### Response Codes:

|Code|Description|
|---|---|
| `200` | Success; response body contains a connector health object | 
| `403` | Invalid/unauthorized API key, or impersonated user not granted `Feeds_Manage` privilege | 
| `404` | Specified `connector-id` was not found | 

##### Example Response:

```
{
    "id": 3,
    "name": "nuget.org",
    "url": "https://www.nuget.org/api/v2",
    "state": "unhealthy",
    "erroMessage": "Connection timed out."
    "lastChecked": "2019-03-29T17:41:55.05+00:00"
}
```

##### PowerShell Example:

```
Invoke-RestMethod `
  -Uri http://{proget-server}/api/connector-health/3 `
  -Headers @{"X-ApiKey" = "secure123"}
```