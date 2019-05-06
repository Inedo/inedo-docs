---
title: Feed Management API
subtitle: Feed Management API
sequence: 200
keywords: proget, feeds, feed-management-api, api
show-headings-in-nav: true
---

<style type="text/css">
.lighten { opacity: 0.5; }
</style>

The Feed Management API Endpoints offer a simple mechanism for querying, creating, and updating [feeds](/support/documentation/proget/core-concepts/feeds), [connectors](/support/documentation/proget/core-concepts/connectors), [vulnerability sources](/support/documentation/proget/compliance/vulnerabilities), [licenses & rules](/support/documentation/proget/compliance/license-scanning), and related data. The primary use cases for these endpoints are:

{.docs}
  - provision and configure feeds with infrastructure automation tools such as Otter
  - allow external tools to query/monitor feed information and metadata
  - duplicate feed configuration with minimal effort
  - copy feed configuration across multiple ProGet instances
  - assist in receiving support by submitting configuration to engineers or the community

:::{.attention .best-practice} 
This API is available starting in ProGet 5.2.
:::

#### Authentication

For security and simplicity, these endpoints require that a Feed Management [API Key](/support/documentation/proget/administration/security/api-keys) is created first. This key can be supplied to the API using any of the methods documented in the [API Key Usage](/support/documentation/proget/administration/security/api-keys#usage) section.

## Object Models {#object-models}

Each endpoint sends and receives entities as [JSON](http://json.org/) objects. The specific objects are defined as follows:

{.docs}
 - [Feed model](#feed-model)
   - [Retention model](#retention-rule-model)
   - [Replication model](#replication-model)
 - [Connector model](#connector-model)
 - [License model](#license-model)

{.attention .analogy } ***Bold\*** indicates a property is required for the [create](#create) endpoint. Unless otherwise indicated, omitting a property or supplying `null` as the value will keep the current setting.*

### Feed Data Model {#feed-model data-title="Feed Data Model"}

| Property              | Details  |
|-----------------------|---|
| **`name`\***            | *`string`* - the unique name of the feed; must not include characters that require URL escaping  |
| **`feedType`\***        | *`string`* - denotes the feed type; valid values are: `universal`, `nuget`, `chocolatey`, `npm`, `bower`, `maven`, `powershell`, `docker`, `rubygems`, `vsix`, `asset`, `romp`, `debian`, `pypi`, `helm`  |
| `description`           | *`string`* - a description displayed with the ProGet feed name in the UI; supplying `null` removes the description |
| `active`                | *`boolean`* - indicates whether the feed is active (`true`) or disabled (`false`) |
| `cacheConnectors`       | *`boolean`* - indicates whether connector caching is enabled for the feed  |
| `dropPath`              | *`string`* - disk path accessible to ProGet's service where packages may be added to the feed; supplying `null` disables the drop path   |
| `packagesPath`          | *`string`* - absolute disk path for package storage if a `packageStore` is not specified, supplying `null` will use a path relative to the value of the `Storage.PackagesRootPath` setting |
| `packageStore`          | *`string`* - serialized configuration for a custom package store such as Amazon S3 or Azure Blob; see [Persisted XML](#persisted-xml); supplying `null` will use ProGet-managed storage as specified by the `packagesPath`   |
| `allowUnknownLicenses`  | *`boolean`* - indicates whether packages with "unknown" licenses are allowed; supplying `null` will inherit the value of the `Feeds.AllowUnknownLicenseDownloads` setting  |
| `allowedLicenses`       | *`string[]`* - array of SPDX license identifiers (e.g. `MIT`, `LGPL-3.0`) known to ProGet *(visible on the Manage Licenses page)* allowed to be downloaded from the feed; supply an empty array to remove them all |
| `blockedLicenses`       | *`string[]`* - array of SPDX license identifiers (e.g. `MIT`, `LGPL-3.0`) known to ProGet *(visible on the Manage Licenses page)* blocked from being downloaded from the feed; supply an empty array to remove them all  |
| `symbolServerEnabled`   | *`boolean`* - indicates whether the NuGet symbol server is enabled; only applies to NuGet-like feed types |
| `stripSymbols`          | *`boolean`* - indicates whether symbol files (i.e. .pdb files) are removed from a NuGet-like package when downloaded |
| `stripSource`           | *`boolean`* - indicates whether source files (i.e. files under `src/` in the package) are removed from a NuGet-like package when downloaded |
| `connectors`            | *`string[]`* - array of connectors for the feed; supply an empty array to remove all feed connectors |
| `vulnerabilitySources`  | *`string[]`* - array of vulnerability sources for the feed; supply an empty array to remove all vulnerability sources  |
| `retentionRules`        | *`RetentionObject[]`* - array of [retention rule objects](#retention-rule-model) that define the retention rules for the feed; supply an empty array to remove all retention rules from the feed  |
| `packageFilters`        | *`string[]`* - array of serialized configurations that define the custom package filters; see [Persisted XML](#persisted-xml); supply an empty array to remove all package filters  |
| `packageAccessRules`    | *`string[]`* - array of serialized configurations that define the custom package access rules; see [Persisted XML](#persisted-xml); supply an empty array to remove all package access rules |
| `replication`           | *`ReplicationObject`* - a [replication object](#replication-model) that defines the replication configuration for the feed |
| `variables`             | *`object`* - an object whose property names represent variable names, and whose property values represent the variable value; supply an object with no properties to remove all variables. <br/> Naming rules: <ul><li>a variable name is a string of no more than fifty characters: numbers (0-9), upper- and lower-case letters (a-Z), dashes (-), spaces ( ), and underscores (_) and must start with a letter, and may not start or end with a dash, underscore, or space</li><li>a variable value is a string of any number of characters</li></ul> |


### Connector Data Model {#connector-model data-title="Connector Data Model"}
 
| Property | Details  |
|---------------------------|-----|
| **`name`\***             | *`string`* - the unique name of the connector; must not include characters that require URL escaping; max 100 characters | 
| **`feedType`\***        | *`string`* - denotes the feed type; valid values are: `universal`, `nuget`, `chocolatey`, `npm`, `maven`, `powershell`, `docker`, `rubygems`, `vsix`, `asset`, `romp`, `pypi`, `helm` | 
| `url`                    | *`string`* - the URL of the connector | 
| `username`               | *`string`* - the username used for authentication at the connector URL | 
| `password`               | *`string`* - the password used for authentication at the connector URL | 
| `timeout`                | *`integer`* - the timeout (in seconds) used when making a request to the connector URL | 
| `filters`                | *`string[]`* - array of connector filters; supply an empty array to remove all filters; supports [wildcards and negations](#wildcards) | 
| `metadataCacheEnabled` | *`boolean`* - indicates whether metadata caching is enabled on the connector | 
| `metadataCacheMinutes` | *`integer`* - the number of minutes a connector metadata request to a specific URL is cached by ProGet *(default: `30`)* | 
| `metadataCacheCount`   | *`integer`* - the number of URL-specific metadata requests cached by ProGet *(default: `100`)* | 

### Retention Rule Data Model {#retention-rule-model data-title="Retention Rule Data Model"}

*Note that this is only used within a feed entity's `retentionRules` property.*

| Property | Details |
|---|---|
| `deletePrereleaseVersions`| *`boolean`* - when `true`, the retention rule only applies to pre-release packages (e.g. they have a pre-release part in their semantic version, or are SNAPSHOT versions) |
| `deleteCached`            | *`boolean`* - when `true`, the retention rule only applies to cached connector packages |
| `keepVersionsCount`       | *`integer`* - when set to `n`, the retention rule always keeps the latest `n` versions of a matching package; this value is ignored for Docker feeds |
| `keepUsedWithinDays`      | *`integer`* - when set to `n`, the retention rule always keeps package versions if they have been downloaded within the past `n` days |
| `triggerDownloadCount`    | *`integer`* - when set to `n`, the retention rule always keeps versions that have been downloaded more than `n` times |
| `keepPackageIds`          | *`string[]`* - array of package names/identifiers that are kept regardless of other filters; supports [wildcards](#wildcards) but not negations, as any matching package is always kept |
| `deletePackageIds`        | *`string[]`* - array of package names/identifiers that are deleted if they match all other filters; supports [wildcards](#wildcards) but not negations (use `keepPackageIds` for exclusions)  |
| `keepVersions`            | *`string[]`* - array of package versions that are kept regardless of other filters; supports [wildcards](#wildcards) but not negations, as any matching package version is always kept |
| `deleteVersions`          | *`string[]`* - array of package versions that are deleted if they match all other filters; supports [wildcards](#wildcards) but not negations (use `keepVersions` for exclusions) |
| `sizeTriggerKb`           | *`integer`* - when set to `n`, indicates the minimum number of kilobytes of storage that must be used in order to run retention; used storage is calculated per-package or per-feed based on the value of `sizeExclusive` |
| `sizeExclusive`           | *`boolean`* - when `true` and `sizeTriggerKb` is set to non-null `n`, retention is run only on packages whose disk size is greater than `n` kilobytes; when `false` and `sizeTriggerKb` is set to non-null `n`, retention is run when the entire feed size is greater than `n` kilobytes; this value is ignored when `sizeTriggerKb` is `null`  |
 
### Replication Data Model {#replication-model data-title="Replication Data Model"}

*Note that this is only used within a feed entity's `replication` property.*

| Property | Details | 
|---|---|
| `clientMode`    | *`string`* - the client replication mode configuration, one of `disabled`, `pullonly`, `pushonly`, `mirror`; [more info](/support/documentation/proget/advanced/feed-replication#replication-client) |
| `serverMode`     | *`string`* - the server replication mode configuration, one of `disabled`, `readonly`, `writeonly`, `readwrite`; [more info](/support/documentation/proget/advanced/feed-replication#replication-server) |
| `clientToken`   | *`string`* - the [token](/support/documentation/proget/advanced/feed-replication#sync-tokens) used to authenticate with a replication server; this field is not returned by list or get endpoints; this field is required when `clientMode` is set to anything other than `disabled`  |
| `serverToken`    | *`string`* - the [token](/support/documentation/proget/advanced/feed-replication#sync-tokens) that must be specified by a replication client in order to connect; this field is not returned by list or get endpoints; this field is required when `serverMode` is set to anything other than `disabled`  |
| `sourceUrl`       | *`string`* - the URL of the replication server; this field is required when `clientMode` is set to anything other than `disabled` |

### License Data Model {#license-model data-title="License Data Model"}

| Property | Details | 
|---|---|
| **`licenseId`\***     | *`string`* - an SPDX license identifier |
| `title`         | *`string`* - a friendly name for the license  |
| `urls`          | *`string[]`* - array of URLs used to map a package license to SPDX identifer |
| `allowed`       | *`boolean`* - indicates whether a license is allowed (`true`) or blocked (`false`) at the global level |
| `allowedFeeds`  | *`string[]`* - array of feed names that contain a license rule set to allow the license  |
| `blockedFeeds`  | *`string[]`* - array of feed names that contain a license rule set to block the license |

#### Wildcards and Negations {#wildcards}

Certain fields noted above support wildcard and negation syntax. For example, the value `["Microsoft.*", "Castle.*", "!Rubbishsoft.*"]` has the following properties:

{.docs}
 - includes any packages that start with `Microsoft.` or `Castle.*`
 - excludes any packages that start with `Rubbishsoft.`

### Feed & Package Types {#feed-and-package-types data-title="Feed & Package Types"}

In the ProGet UI, Chocolatey and PowerShell are represented as separate feed types, even though they just contain NuGet packages and use the same NuGet API. This is why, in the ProGet UI, you can change a feed's type between NuGet, Chocolatey, and PowerShell at any time, but not between other feed types.

The API allows feed types to be changed only within the following feed type groups (anything else generates a `400` error):

{.docs}
 - `universal` and `romp`
 - `nuget`, `chocolatey`, and `powershell`

### Persisted XML Configuration {#persisted-xml data-title="Persisted XML Configuration"}

Because package stores, package filters, and package access rules are implemented by extensible runtime components (i.e. you can create your own using the [Inedo SDK](/support/documentation/inedosdk)), ProGet stores configuration for these components in "Persisted XML", which is a lightweight object serialization format that Inedo products use.

The easiest way to see what an object's persisted XML will look like is to create it in the UI, then look at the XML in either the database or the API [list](#list) or [get](#get) request. As a reference, we've provided the persisted XML for both supported package stores and a general example:

<tab-block>
<tab name="AWS">

```
<Inedo.ProGet.Extensions.AWS.PackageStores.S3FileSystem Assembly="AWS">
  <Properties AccessKey="{publicAccessKey}" SecretAccessKey="{privateAccessKey}" BucketName="{bucketName}" TargetPath="{pathInsideBucketDefaultIsRoot}" ReducedRedundancy="{False}" MakePublic="{False}" Encrypted="False" RegionEndpoint="{us-east-1}" CustomServiceUrl="{customServiceUrl}" />
</Inedo.ProGet.Extensions.AWS.PackageStores.S3FileSystem>
```

</tab>
<tab name="Azure Blob Storage">

```
<Inedo.ProGet.Extensions.Azure.PackageStores.AzureFileSystem Assembly="Azure">
  <Properties ConnectionString="{DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key}" ContainerName="{blobContainerName}" TargetPath="{pathToFilesDefaultIsRoot}" />
</Inedo.ProGet.Extensions.Azure.PackageStores.AzureFileSystem>
```

</tab>
<tab name="Custom Extension Format">

```
<{FullNamespace.ClassName} Assembly="AssemblyName">
  <Properties PropertyNameA="{property value}" PropertyNameB="1000" />
</{FullNamespace.ClassName}>
```

</tab>
</tab-block>

### Names & IDs {#names-and-ids data-title="Names & IDs"}

Because one of the primary use cases of the Feed Management API is maintaining feed configuration across instances of ProGet, using or exposing the read-only, system-generated identifiers for entities is not practical. As such, these IDs are not used by the API.

#### Default Feed Disk Path 

When you don't specify a disk path for a feed to use, ProGet will use a path that's based on the feed's system-generated ID. This means that, just like in the UI, if were to delete a feed and then recreate it with the same name, the disk path will be different.

## Endpoint Specifications {#endpoints data-title="Endpoint Specifications"}

All of the management endpoints follow the same convention:

{.info} GET/POST /api/management/**&laquo;entity-type&raquo;**/**&laquo;action-type&raquo;**/**[&laquo;entity-name&raquo;]**

- `entity-type` is one of `feeds`, `connectors`, or `licenses`
- `action-type` is one of [list](#list), [get](#get), [create](#create), [update](#update), or [delete](#delete)
- `entity-name` is the name identifier of the entity being updated, or deleted; it is not valid on a `list` or `create` action type
- all endpoints require an API key with _Feed Management_ permissions (and additionally _Manage Licenses_ permissions for `/licenses` endpoints). This key can be supplied to the endpoint via any of the documented [API Key Usage](/support/documentation/proget/administration/security/api-keys#usage) methods
- `indent` may suppllied in the query string for [get](#get) or [list](#list) endpoints to improve readability
- `application/json` content type is required for any data sent via `POST`/`PUT`/`PATCH`


### List Entities {#list}

Returns an array of objects representing the specified `entity-type` (feeds, connectors, or licenses).

##### URL Format:

{.info} GET/HEAD <span class="lighten">/api/management/</span>**&laquo;entity-type&raquo;**/list

##### Response Codes:

|Code|Description|
|---|---|
| `200` | Success | 
| `403` | Invalid/unauthorized API key, or impersonated user not granted `Feeds_Manage` privilege | 

##### Example Responses: {#list-example-responses}

<tab-block> 
<tab name="List Feeds">

{.info} GET/HEAD <span class="lighten">/api/management/</span>feeds/list

```
[
  {
    "name": "Default",
    "feedType": "nuget",
    "active": true,
    "cacheConnectors": true,
    "allowUnknownLicenses": true,
    "allowedLicenses": ["MIT", "BSD-2-Clause", "BSD-3-Clause", "Apache-2.0"],
    "blockedLicenses": ["RSGPL"],
    "symbolServerEnabled": true,
    "stripSymbols": true,
    "stripSource": true,
    "connectors": [
      "nuget.org"
    ],
    "vulnerabilitySources": [],
    "retentionRules": [
      {
        "deletePrereleaseVersions": false,
        "keepVersionsCount": 10,
        "keepUsedWithinDays": 90,
        "deletePackageIds": [
          "InedoLib",
          "InedoIcons",
          "Inedo.LicenseCreator"
        ],
        "keepPackageIds": null,
        "keepVersions": null,
        "deleteVersions": null,
        "deleteCached": false,
        "sizeTriggerKb": null,
        "sizeExclusive": true,
        "triggerDownloadCount": 1
      }
    ],
    "packageFilters": {},
    "packageAccessRules": {},
    "replication": {},
    "variables": {}
  },
  { ... }  
]
```
</tab>
<tab name="List Connectors">

{.info} GET/HEAD <span class="lighten">/api/management/</span>connectors/list

```
[
  {
    "name": "nuget.org",
    "url": "https://www.nuget.org/api/v2/",
    "feedType": "NuGet",
    "timeout": 10,
    "metadataCacheEnabled": true,
    "metadataCacheMinutes": 1440,
    "metadataCacheCount": 100,
    "filters": []
  },
  {
    "name": "chocolatey.org",
    "url": "http://chocolatey.org/api/v2/",
    "feedType": "NuGet",
    "timeout": 10,
    "metadataCacheEnabled": true,
    "metadataCacheMinutes": 30,
    "metadataCacheCount": 100,
    "filters": []
  },
  {
    "name": "Inedo Den",
    "url": "https://inedo.com/den/feed",
    "feedType": "ProGet",
    "timeout": 10,
    "metadataCacheEnabled": true,
    "metadataCacheMinutes": 30,
    "metadataCacheCount": 100,
    "filters": []
  }
]
```

</tab>

<tab name="List Licenses">

{.info} GET/HEAD <span class="lighten">/api/management/</span>licenses/list

```
[
  {
    "licenseId": "0BSD",
    "title": "BSD Zero Clause License",
    "urls": [
      "landley.net/toybox/license.html",
      "spdx.org/licenses/0BSD.html"
    ],
    "allowed": true,
    "allowedFeeds": ["OpenLicenses"],
    "blockedFeeds": []
  },
  {
    "licenseId": "AAL",
    "title": "Attribution Assurance License",
    "urls": [
      "spdx.org/licenses/AAL.html",
      "www.opensource.org/licenses/attribution"
    ],
    "allowed": true,
    "allowedFeeds": [],
    "blockedFeeds": []
  },
  { ... }
]
```

</tab>
</tab-block> 

### Get Entity {#get}

Returns a single data object with the name `entity-name` for the specified `entity-type`.

##### URL Format:

{.info} GET/POST /api/management/**&laquo;entity-type&raquo;**/get/**&laquo;entity-name&raquo;**

##### Response Codes:

|Code|Description|
|---|---|
| `200` | Success | 
| `403` | Invalid/unauthorized API key, or impersonated user not granted `Feeds_Manage` privilege | 
| `404` | Entity not found |

##### Example Responses: {#get-example-responses}

<tab-block> 
<tab name="Get Feed">

{.info} GET/HEAD <span class="lighten">/api/management/</span>feeds/get/Default

```
{
  "name": "Default",
  "feedType": "nuget",
  "active": true,
  "cacheConnectors": true,
  "allowUnknownLicenses": true,
  "allowedLicenses": ["MIT", "BSD-2-Clause", "BSD-3-Clause", "Apache-2.0"],
  "blockedLicenses": ["RSGPL"],
  "symbolServerEnabled": true,
  "stripSymbols": true,
  "stripSource": true,
  "connectors": [
    "nuget.org"
  ],
  "vulnerabilitySources": [],
  "retentionRules": [
    {
      "deletePrereleaseVersions": false,
      "keepVersionsCount": 10,
      "keepUsedWithinDays": 90,
      "deletePackageIds": [
        "InedoLib",
        "InedoIcons",
        "Inedo.LicenseCreator"
      ],
      "keepPackageIds": null,
      "keepVersions": null,
      "deleteVersions": null,
      "deleteCached": false,
      "sizeTriggerKb": null,
      "sizeExclusive": true,
      "triggerDownloadCount": 1
    }
  ],
  "packageFilters": {},
  "packageAccessRules": {},
  "replication": {},
  "variables": {}
}
```
</tab>
<tab name="Get Connector">

{.info} GET/HEAD <span class="lighten">/api/management/</span>connectors/get/Inedo%20Den

```
{
  "name": "Inedo Den",
  "url": "https://inedo.com/den/feed",
  "feedType": "ProGet",
  "timeout": 10,
  "metadataCacheEnabled": true,
  "metadataCacheMinutes": 30,
  "metadataCacheCount": 100,
  "filters": []
}
```

</tab>

<tab name="Get License">

{.info} GET/HEAD <span class="lighten">/api/management/</span>licenses/get/0BSD

```
{
  "licenseId": "0BSD",
  "title": "BSD Zero Clause License",
  "urls": [
    "landley.net/toybox/license.html",
    "spdx.org/licenses/0BSD.html"
  ],
  "allowed": true,
  "allowedFeeds": ["OpenLicenses"],
  "blockedFeeds": []
}
```

</tab>
</tab-block>

### Create Entity {#create}

Creates a specified `entity-type` with the properties defined in the body of the request. Refer to the [Object Models](#object-models) for the JSON format of the entities, and the caveats related to specific properties (i.e. supplying `null` values vs. empty arrays, required field combinations, etc.)

##### URL Format:

{.info} POST/PUT <span class="lighten">/api/management/</span>**&laquo;entity-type&raquo;**/create

##### Response Codes:

|Code|Description|
|---|---|
| `201` | Success (created); response body contains new object | 
| `400` | Validation error; the response body content will contain specific error messages e.g. "feed name is required", "invalid feed type" |
| `403` | Invalid/unauthorized API key, or impersonated user not granted `Feeds_Manage` privilege | 
| `422` | Invalid entity; occurs when a property of the entity references a missing entity; the response body content will contain specific error messages e.g. "connector 'NonExistent' does not exist"  |

##### Examples:

<tab-block>
<tab name="Create Simple Feed">

{.info} POST/PUT <span class="lighten">/api/management/</span>feeds/create

| Header | Value |
|---|---|
| `X-ApiKey` | `secure123` |
| `Content-Type` | `application/json` |

**Body:**

```
{
  "name": "InedoExtensions",
  "feedType": "universal",
  "active": true
}
```

-----

**PowerShell Example:**

```
Invoke-RestMethod -Method POST `
  -Uri http://{proget-server}/api/management/feeds/create `
  -ContentType "application/json" `
  -Headers @{"X-ApiKey" = "secure123"} `
  -Body (@{name="InedoExtensions";feedType="universal";active=$true} | ConvertTo-Json)
```

</tab>
</tab-block>


### Update Entity {#update}

Updates a specified `entity-type` with an existing name `entity-name` using the properties defined in the body of the request. This endpoint supports partial updating by only updating the properties that are supplied in the request. Refer to the [Object Models](#object-models) for the JSON format of the entities, and the caveats related to specific properties (i.e. supplying `null` values vs. empty arrays, required field combinations, etc.)

For most properties, omitting scalar values will keep the existing or default values. When updating a feed property with an array value, the array value supplied should be considered the "desired state" (regardless of `POST` or `PATCH`). For example, if a feed has connectors A and B, supplying `connectors: ["C"]` in the [feed object](#feed-model) will remove connectors A and B from the feed. To append values, perform a [get](#get) first, then append the existing values returned in the [update](#update) request.

##### URL Format:

{.info} POST/PATCH <span class="lighten">/api/management/</span>**&laquo;entity-type&raquo;**/update/**&laquo;entity-name&raquo;**

##### Response Codes:

|Code|Description|
|---|---|
| `201` | Success (updated); response body contains updated object | 
| `400` | Validation error; the response body content will contain specific error messages e.g. "feed name is required", "invalid feed type" |
| `403` | Invalid/unauthorized API key, or impersonated user not granted `Feeds_Manage` privilege | 
| `404` | Entity `entity-name` does not exist
| `422` | Invalid entity; occurs when a property of the entity references a missing entity; the response body content will contain specific error messages e.g. "connector 'NonExistent' does not exist"  |

##### Examples:

<tab-block>
<tab name="Add Feed Description">

{.info} POST/PATCH <span class="lighten">/api/management/</span>feeds/update/InedoExtensions

| Header | Value |
|---|---|
| `X-ApiKey` | `secure123` |
| `Content-Type` | `application/json` |

**Body:**

```
{
  "description": "This feed serves as a proxy for Inedo SDK extensions."
}
```

-----

**PowerShell Example:**

```
Invoke-RestMethod -Method POST `
  -Uri http://{proget-server}/api/management/feeds/update/InedoExtensions `
  -ContentType "application/json" `
  -Headers @{"X-ApiKey" = "secure123"} `
  -Body (@{description="This feed serves as a proxy for Inedo SDK extensions."} | ConvertTo-Json)
```

</tab>
<tab name="Update Connector URL">

{.info} POST/PATCH <span class="lighten">/api/management/</span>connectors/update/nuget.org

| Header | Value |
|---|---|
| `X-ApiKey` | `secure123` |
| `Content-Type` | `application/json` |

**Body:**

```
{
  "url": "https://nuget/org/api/v2"
}
```

-----

**PowerShell Example:**

```
Invoke-RestMethod -Method POST `
  -Uri http://{proget-server}/api/management/connectors/update/nuget.org`
  -ContentType "application/json" `
  -Headers @{"X-ApiKey" = "secure123"} `
  -Body (@{url="https://nuget/org/api/v2"} | ConvertTo-Json)
```

</tab>
</tab-block>


### Delete Entity {#delete}

Deletes a specified `entity-type` with an existing name `entity-name`.

##### URL Format:

{.info} POST/DELETE <span class="lighten">/api/management/</span>**&laquo;entity-type&raquo;**/delete/**&laquo;entity-name&raquo;**

##### Response Codes:

|Code|Description|
|---|---|
| `200` | Success (deleted) | 
| `403` | Invalid/unauthorized API key, or impersonated user not granted `Feeds_Manage` privilege | 
| `404` | Entity `entity-name` does not exist

##### Examples:

<tab-block>
<tab name="Delete Connector">

{.info} POST/PATCH <span class="lighten">/api/management/</span>connectors/delete/OldConnector

| Header | Value |
|---|---|
| `X-ApiKey` | `secure123` |

**Body:**

*No content*

-----

**PowerShell Example:**

```
Invoke-RestMethod -Method DELETE `
  -Uri http://{proget-server}/api/management/connectors/delete/OldConnector `
  -Headers @{"X-ApiKey" = "secure123"}
```

</tab>

</tab-block>
