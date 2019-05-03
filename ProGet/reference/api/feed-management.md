---
title: Feed Management API
subtitle: Feed Management API
sequence: 200
keywords: proget, feeds, feed-management-api, api
show-headings-in-nav: true
---

The Feed Management API Endpoints offer a simple mechanism for querying, creating, and updating [feeds](/support/documentation/proget/core-concepts/feeds), [connectors](/support/documentation/proget/core-concepts/connectors), [Vulnerability Sources](/support/documentation/proget/compliance/vulnerabilities), [License Rules](/support/documentation/proget/compliance/license-scanning), and related data. The primary use cases for these endpoints are:

{.docs}
  - provision and configure feeds with infrastructure automation tools such as Otter
  - allow external tools to query/monitor feed information and metadata
  - duplicate feed configuration with minimal effort
  - copy feed configuration across multiple ProGet instances
  - assist in receiving support by submitting configuration to engineers or the community

:::{.attention .best-practice} 
This API is available starting in ProGet 5.2.
:::

For security and simplicity, these endpoints require that a Feed Management [API Key](/support/documentation/proget/administration/security/api-keys) is created first. This key can be supplied to the API using any of the methods documented in the [API Key Usage](/support/documentation/proget/administration/security/api-keys#usage) section.

### Feed Data Specification {#feed-data-specification data-title="Data Specification"}

All of these endpoints send and receive entries as [JSON](http://json.org/) objects.

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
| `retentionRules`        | *`RetentionObject[]`* - array of [retention rule objects](#retention-rule-data-specification) that define the retention rules for the feed; supply an empty array to remove all retention rules from the feed  |
| `packageFilters`        | *`string[]`* - array of serialized configurations that define the custom package filters; see [Persisted XML](#persisted-xml); supply an empty array to remove all package filters  |
| `packageAccessRules`    | *`string[]`* - array of serialized configurations that define the custom package access rules; see [Persisted XML](#persisted-xml); supply an empty array to remove all package access rules |
| `replication`           | *`ReplicationObject`* - a [replication object](#replication-data-specification) that defines the replication configuration for the feed |
| `variables`             | *`object`* - an object whose property names represent variable names, and whose property values represent the variable value; supply an object with no properties to remove all variables. <br/> Naming rules: <ul><li>a variable name is a string of no more than fifty characters: numbers (0-9), upper- and lower-case letters (a-Z), dashes (-), spaces ( ), and underscores (_) and must start with a letter, and may not start or end with a dash, underscore, or space</li><li>a variable value is a string of any number of characters</li></ul> |

 ***\*Bold** indicates a property is required. Unless otherwise indicated, omitting a property or supplying `null` as the value will keep the current setting.*


### Connector Data Specification {#connector-data-specification data-title="Connector Data Specification"}
 
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

***\*Bold** indicates a property is required. Unless otherwise indicated, omitting a property or supplying `null` as the value will keep the current setting.*

### Retention Rule Data Specification {#retention-rule-data-specification data-title="Retention Rule Data Specification"}

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
 

### License Data Specification {#license-data-specification data-title="License Data Specification"}

| Property | Details | 
|---|---|
| **`licenseId`\***     | *`string`* - an SPDX license identifier |
| `title`         | *`string`* - a friendly name for the license  |
| `urls`          | *`string[]`* - array of URLs used to map a package license to SPDX identifer |
| `allowed`       | *`boolean`* - indicates whether a license is allowed (`true`) or blocked (`false`) at the global level |
| `allowedFeeds`  | *`string[]`* - array of feed names that contain a license rule set to allow the license  |
| `blockedFeeds`  | *`string[]`* - array of feed names that contain a license rule set to block the license |
 
***\*Bold** indicates a property is required. Unless otherwise indicated, omitting a property or supplying `null` as the value will keep the current setting.*

#### Wildcards and Negations {#wildcards}

Certain fields noted above support wildcard and negation syntax. For example, the value `["Microsoft.*", "Castle.*", "!Rubbishsoft.*"]` has the following properties:

 - includes any packages that start with `Microsoft.` or `Castle.*`
 - excludes any packages that start with `Rubbishsoft.`

### Feed & Package Types {#feed-and-package-types data-title="Feed & Package Types"}

In the ProGet UI, Chocolatey and PowerShell are represented as separate feed types, even though they just contain NuGet packages and use the same NuGet API. This is why, in the ProGet UI, you can change a feed's type between NuGet, Chocolatey, and PowerShell at any time, but not between other feed types.

In the Feed Management API, this is modeled with distinct feed and package types:

{.docs}
 * Chocolatey and PowerShell are not feed types, but instead special package types within a NuGet feed
 * Once a feed is created, the feed type cannot be changed, and specifying a different feed type on update will result in an error
 * Package types are optional, and supported ony on NuGet and Universal feed types

### Persisted XML Configuration {#persisted-xml data-title="Persisted XML Configuration"}

Because package stores, package filters, and package access rules are implemented by extensible runtime components (i.e. you can create your own using the [Inedo SDK](/support/documentation/inedosdk)), ProGet stores configuration for these components in "Persisted XML", which is a lightweight object serialization format that Inedo products use. Persisted XML is pretty simple format (as as far object serialization goes), but documenting it is beyond the scope of this API. 

The easiest way to see what an object's persisted XML will look like is to create it in the UI, then look at the XML in either the database or the API. As a reference, we've provided the persisted XML for three components that we maintain:

{.upcoming} Examples coming soon...

### Names & IDs {#names-and-ids data-title="Names & IDs"}

Because one of the primary use cases of the Feed Management API is maintaining feed configuration across instances of ProGet, using or exposing the read-only, system-generated identifiers for entities is not practical. As such, these IDs are not used by the API.

#### Default Feed Disk Path 

When you don't specify a disk path for a feed to use, ProGet will use a path that's based on the feed's system-generated ID. This means that, just like in the UI, if were to delete a feed and then recreate it with the same name, the disk path will be different.

### Endpoint Specifications {#endpoints data-title="Endpoint Specifications"}

All of the feed management endpoints follow the same convention
GET/POST /api/management/<b>&laquo;entity-type&raquo;</b>/<b>&laquo;action-type&raquo;</b>/<b>&laquo;entity-name&raquo;</b><b>&laquo;?key=secure123&raquo;</b> 

- `entity-type` is one of `feeds`, `connectors`, or `licenses`
- `action-type` is one of [list](#list), [get](#get), [create](#create), [update](#update), or [delete](#delete)
- `entity-name` is the name identifier of the entity being updated, or deleted; it is not valid on a `list` or `create` action type
- `key` all endpoints require an api key with _Feed Management_ permissions. This can be passed to the endpoint in the header or the body of your request. For requests that include an entity-name you will need to add the api-key in the body.
- `JSON` is required for any data sent in the _body_ of your request.


{.upcoming} Endpoint examples coming soon...