---
title: Feed Management API
subtitle: Feed Management API
sequence: 200
keywords: proget,feeds
show-headings-in-nav: true
---

The Feed Management API Endpoints offer a simple mechanism for querying, creating, and updating [feeds], [connectors], [Vulnerability Sources], [License Rules], and related data. The primary use cases for these endpoints are to
 - provision and configure feeds with infrastructure automation tools such as Otter
 - allow external tools to query/monitor feed information and metadata
 - duplicate feed configuration with minimal effort
 - copy feed configuration across multiple ProGet instances
 - assist in receiving support by submitting configuration to engineers or the community

These API endpoints should be used instead of the [Native API Methods] when possible, as they are much easier to use and will likely not change. {.announcement}

For security and simplicity, these endpoints require that a `[Feed Management API Key]` is created first. This key is passed into the API call within the body or the header of your request.
`{
   "API_key": "APIKEY"
 }`

### Feed Data Specification {#feed-data-specification data-title="Data Specification"}

All of these endpoints send and receive entries as [JSON](http://json.org/) objects.


Property               | Format
-----------------------|----------
`name`                 | A *string* of no more than fifty characters: numbers (0-9), upper- and lower-case letters (a-Z), dashes (-), and underscores (_); must start with a letter, and may not start or end with a dash or underscore. **required**.
`feedType`             | A *string* value of `nuget`, `npm`, `universal`, ..., or `xxx`. **required**.
`packageType`          | A *string* value of `chocolatey` or `powershell` (for `nuget` feeds) or `romp` (for `universal` feed types). See [Feed and Package Types](#feed-and-package-types). Optional.
`description`          | A *string* that is displayed in the ProGet UI under the feed namme.  Optional (default `null`).
`active`               | A *boolean* indicating whether the feed is active or disabled. Optional (default `true`).
`cacheConnectors`      | A *boolean* indicating whether metadata from packages should be cached (???). Optional (default `???`).
`dropPath`             | A *string* containing a disk path accessible to ProGet's service where packages may added to the feed. Optional (default `null`).
`packagesPath`         | A *string* containing an absolute or relative (to RootFeedPath?) path that will override where packages are stored. Optional (default `null`). 
`packageStore`         | A *string* containing serialized XML configuration for a custom package store like Amazon S3 or Azure Blob. See [Persisted XML](#persisted-xml). Optional (default `null`). 
`replicationMode`      | A *string* value of `none`, `listen`, `initiate`, or `listenAndInitiate`. Optional (default `none`).
`syncToken`            | A *string* containing the replication token used when replicating. **required** when `replicationMode` is not `none`.
`syncUrl`              | A *string* containing URL of a replication endpoint to contact. **required** when `replicationMode` is `listenAndInitiate` or `initiate`.
`syncDate`             | A *string* containing an ISOxxx formatted date that indicates the last time a replication occurred. You should only set this to force a new replication. Optional.
`allowUnknownLicenses` | A *boolean* indicating whether unknown licenses should be allowed or the global setting should be followed.Optional (default `null`).
`allowedLicenses`      | An *array of strings*, each consisting of an existing license ID, such as MIT or BSD. Optional.
`blockedLicenses`      | An *array of strings*, each consisting of an existing license ID, such as MIT or BSD. Optional.
`symbolServerEnabled`  | A *boolean* indicating whether symbol server is enabled.  This must be `null` unless the `feedType` is `nuget` and `packageType` is `null`. Optional (default `false` ??)
`stripSymbols`         | A *boolean* indicating whether symbols should be stripped from NuGet packages.  This must be `null` unless the `feedType` is `nuget` and `packageType` is `null`. Optional (default `false` ??).
`stripSource`          | A *boolean* indicating whether source should be stripped from NuGet packages.  This must be `null` unless the `feedType` is `nuget` and `packageType` is `null`. Optional (default `false` ??).
`connectors`           | An *array of strings*, each consisting of an existing and feedType-compatible connector name. Optional.
`vulnerabilitySources` | An *array of strings, each consisting of an existing vulnerability source name. Optional.
`retentionRules`       | An *array of objects*, each consisting of a valid retention rule object. Optional.
`packageFilters`       | An *array of strings*, each consisting of serialized XML configuration for a custom package filter. See [Persisted XML](#persisted-xml). Optional.
`packageAccessRules`   | An *array of strings*, each consisting of serialized XML configuration for a custom package access rule, like Whitesource. See [Persisted XML](#persisted-xml). Optional.
`variables`            | An *object* with property/values representing variable names and values. Optional. <ul><li>a variable name is a *string* of no more than fifty characters: numbers (0-9), upper- and lower-case letters (a-Z), dashes (-), spaces ( ), and underscores (_) and must start with a letter, and may not start or end with a dash, underscore, or space; a variable</li><li>a variable value is a *string* of any number of characters</li></ul>



### Connector Data Specification {#connector-data-specification data-title="Connector Data Specification"}
 
 Property              | Format
-----------------------|----------
 `name` | a *string*, (100 chars max). See [Names and IDs](#names-and-ids). **required**.
 `url`  | a *string*, referencing the url to the connector, **required**.
 `feedType` | a *string*, type of feed `npm`, `NuGet`, `Chocolatey`, etc. **required**.
 `userName`  | a *string*, optional.
 `password` | a *string*, optional.
 `timeout`  | an *integer*, optional. Number of seconds **required**. 
 `filter`   | an *array of strings*, optional.
 `metadataCached`  | a *boolean*, **required**.
 `metadataCacheMinutes`  | an *integer*, **required**.
 `metadataCacheCount`   | an *integer*, **required**.


### Retention Rule Data Specification {#retention-rule-data-specification data-title="Retention Rule Data Specification"}
Note that this is only used within a feed entity's retentionRules property, and cannot be defined outside of a feed.

 Property              | Format
-----------------------|----------
`feed_Id`   | an *integer* that identifies the feed, optional
`FeedType_Name`  | a *string*, type of feed `npm`, `NuGet`, `Chocolatey`, etc. **required**.
`DeletePrereleaseVersions_Indicator` | a *boolean*, **required**.
`DeleteCached_Indicator` | a *boolean*, **required**.
`KeepVersions_Count` | an *integer*, optional
`keepVersions` |  an *array of strings*, optional. [[new in 5.2 with PG-1450]]
`deleteVersions` |  an *array of strings*, optional. [[new in 5.2 with PG-1450]]
`KeepUsedWithin_Days` | an *integer*, optional.
`DeletePackageIds_Csv` |  an *array of strings*, optional.
`KeepPackageIds_Csv` |  an *array of strings*,  optional.
`sizeTrigger_KBytes`  | an *integer* in KBytes. optional.
`sizeExclusive_Indicator`  | a *boolean*, **required**.
`triggerDownloadCount` | an *integer*, optional.
 

### License Data Specification {#license-data-specification data-title="License Data Specification"}

 Property              | Format
-----------------------|----------
`licenseId` | a *string* SPDX identifier of 50 chars, **required**.
`title` |  a *string*, **required**
`urls`  | an *array of strings*, **required**.
`allowed`   | a *boolean* that will determine if license is allowed or blocked, optional.
`alllowedFeeds` | an *array of strings* that shows which feeds are allowed, optional.
`blockedFeeds` | an *array of strings* that shows which feeds are blocked, optional.
 

### Feed & Package Types {#feed-and-package-types data-title="Feed & Package Types"}

In the ProGet UI, Chocolatey and PowerShell are represented as separate feed types, even though they just contain NuGet packages and use the same NuGet API. This is why, in the ProGet UI, you can change a feed's type between NuGet, Chocolatey, and PowerShell at any time, but not between other feed types.

In the Feed Management API, this is modeled with distinct feed and package types:
 * Chocolatey and PowerShell are not feed types, but instead special package types within a NuGet feed
 * Once a feed is created, the feed type cannot be changed, and specifying a different feed type on update will result in an error
 * Package types are optional, and supported ony on NuGet and Universal feed types

### Persisted XML Configuration {#persisted-xml data-title="Persisted XML Configuration"}

Because package stores, package filters, and package access rules are implemented by extensible runtime components (i.e. you can create your own using the [Inedo SDK]), ProGet stores configuration for these components in "Persisted XML", which is a lightweight object serialization format that Inedo products use. Persisted XML is pretty simple format (as as far object serialization goes), but documenting it is beyond the scope of this API. 

The easiest way to see what an object's persisted XML will look like is to create it in the UI, then look at the XML in either the database or the API. As a reference, we've provided the persisted XML for three components that we maintain:


```
<Inedo.ProGet.Feeds.NuGet.NuGetFeedConfig Assembly="ProGetCoreEx">
  <Properties SymbolServerEnabled="False" StripSymbolFiles="False" StripSourceCodeInvert="False" UseLegacyVersioning="False" />
</Inedo.ProGet.Feeds.NuGet.NuGetFeedConfig>
```


### Names & IDs {#names-and-ids data-title="Names & IDs"}
Because one of the primary use cases of the Feed Management API is maintaining feed configuration across instances of ProGet, using or exposing the read-only, system-generated identifiers for entitites is not practical. As such, these IDs are not used by the API.

**Default Feed Disk Path.** When you don't specify a disk path for a feed to use, ProGet will use a path that's based on the feed's system-generated ID. This means that, just like in the UI, if were to delete a feed and then recreate it with the same name, the disk path will be different.

### Endpoint Specifications {#endpoints data-title="Endpoint Specifications"}
All of the feed management endpoints follow the same convention
GET/POST ::/api/management/::<b>&laquo;entity-type&raquo;</b>/<b>&laquo;action-type&raquo;</b>/<b>&laquo;entity-name&raquo;</b><b>&laquo;?key=secure123&raquo;</b> 

- `entity-type` is one of `feeds`, `connectors`, or `licenses`
- `action-type` is one of [list](#list), [get](#get), [create](#create), [update](#update), or [delete](#delete)
- `entity-name` is the name identifier of the entity being updated, or deleted; it is not valid on a `list` or `create` action type
- `key` all endpoints require an api key with _Feed Management_ permissions. This can be passed to the endpoint in the header or the body of your request.
- `Json` is required for any data sent in the _body_ of your request. (i.e. the Api Key)

#### List Entries {#list}

This returns a status of 200 (on success), or 403 (api key not authorized), and a body containing only an *array* of entity objects. 


##### Feed List
```
GET /api/management/feeds/list
{
    "API_key": "secure123"
}

```
or
```
GET /api/management/feeds/list?key=secure123
```

Sample Output: 
```
[
 {
    "name": "chocoTest2",
    "feedType": "npm",
    "description": "testing the update.....4",
    "active": true,
    "cacheConnectors": false,
    "dropPath": "newPath22",
    "packagesPath": "newPathOverride",
    "syncToken": "aGdoZ2Y=",
    "syncTargetUrl": "https://google.com",
    "allowUnknownLicenses": false,
    "allowedLicenses": [
        "0BSD"
    ],
    "blockedLicenses": [
        "AAL"
    ],
    "symbolServersEnabled": false,
    "stripSymbols": false,
    "stripSource": false,
    "connectors": [
        "testing"
    ],
    "vulnerabilitySources": [],
    "retentionRules": [
        {
            "Feed_Id": 21,
            "Feed_Name": "chocoTest2",
            "FeedType_Name": "npm",
            "Sequence_Number": 1,
            "DeletePrereleaseVersions_Indicator": "N",
            "KeepVersions_Count": 22,
            "KeepUsedWithin_Days": null,
            "DeletePackageIds_Csv": null,
            "KeepPackageIds_Csv": null,
            "DeleteCached_Indicator": "Y",
            "SizeTrigger_KBytes": 102400,
            "SizeExclusive_Indicator": "N",
            "TriggerDownload_Count": null
        }
    ],
    "packageFilters": [
        {
            "Feed_Id": 21,
            "Sequence_Number": 1,
            "PackageFilter_Name": "testPackageFilter2",
            "PackageFilter_Configuration": ""
        },
        {
            "Feed_Id": 21,
            "Sequence_Number": 2,
            "PackageFilter_Name": "testPackageFilter3",
            "PackageFilter_Configuration": ""
        }
    ],
    "packageAccessRules": [
        {
            "Feed_Id": 21,
            "Sequence_Number": 1,
            "PackageAccessRule_Name": "rule#1.2",
            "PackageAccessRule_Configuration": ""
        }
    ],
    "variables": {
        "test22": "test22",
        "test2": "ttttt4444444"
    }
}
 ,
  { ... }
]
```
##### Connector List
```
GET /api/management/connectors/list
{
    "API_key": "secure123"
}
```
or
```
GET /api/management/connectors/list?key=secure123
```
Sample Output:
```
[
  {
    "name": "PublicNuGet",
    "url": "https://www.nuget.org/api/v2",
    "feedType": "nuget",
    "timeout": 30,
    "metadataCached": true,
    "metadataCacheMinutes": 5,
    "metadataCacheCount": 20
  
  },
  {
    "name": "InedoDenPreRelease",
    "url": "https://proget.inedo.com/upack/Extensions",
    "feedType": "universal",
    "timeout": 30,
    "metadataCached": true,
    "metadataCacheMinutes": 5,
    "metadataCacheCount": 20
  
  },
  { ... }
]
```

##### License List
This endpoint requires License_Manage if POST or PUT is specified.{.information}


```
GET /api/management/licenses/list
{
    "API_key": "secure123"
}

```
or
```
GET /api/management/licenses/list?key=secure123
```
Sample Output:
```
[
  {
    "licenseId": "Abstyles",
    "title": "Abstyles License",
    "urls": [
        "fedoraproject.org/wiki/Licensing/Abstyles",
        "spdx.org/licenses/Abstyles.html"
    ],
    "allowed": true,
    "allowedFeeds": [],
    "blockedFeeds": "InternalPackages"
  },
  {
   "licenseId": "OLDAP-2.2.2",
    "title": "Open LDAP Public License 2.2.2",
    "urls": [
        "spdx.org/licenses/OLDAP-2.2.2.html",
        "www.openldap.org/devel/gitweb.cgi?p=openldap.git;a=blob;f=LICENSE;hb=df2cc1e21eb7c160695f5b7cffd6296c151ba188"
    ],
    "allowed": false,
    "allowedFeeds": "TestPackages",
    "blockedFeeds": []
  },
  { ... }
]
```



The above is an example, and truncates responses for readability with ellipses; the API key used requires the `FeedManagement_View` permission for GET requests, and License_Manage on the speific feed (or all feeds) if updating licenses.

Note that entities will be returned with all properties, and unset values will be null empty arrays, as appropriate.  


#### Get Entity {#get data-title="Get Entity"}

This returns a status of 201 (on success), 403 (api key not authorized), or 404 (entity not found), and an body containing the entity object.

We opted not to provide an example, as the request body is simply a JSON object formatted like the [list](#list) examples. The API key used requires the `FeedManagement_View` permission.


#### Create Entity {#create data-title="Create Entity"}

This returns a status of 201 (on success), 403 (api key not authorized), or 422 (invalid entity), and an body containing either the entity object, or a description of the 422 status.

If the entity references another entity (e.g. in the `connectors` property of a feed) and the referenced entity does not exist, an invalid entity (422) will be returned. {.announcement}

We opted not to provide an example, as the request body is simply a JSON object formatted like the [list](#list) examples. The API key used requires the `FeedManagement_Update` permission.

#### Update Entity {#update data-title="Update Entity"}

This returns a status of 200 (on success), 403 (api key not authorized), 404 (entity not found), or 422 (invalid entity), and an body containing either the entity object, or a description of the 422 status.

If the entity references another entity (e.g. in the `connectors` property of a feed) and the referenced entity does not exist, an invalid entity (422) will be returned. {.announcement}

If there are missing properties on the entity, only the specified properties will be updated. Setting a null or empty array value will cause the corresponding values to be removed, if allowed. {.announcement}

##### Update a Feed
```
POST /api/management/feeds/update/{FeedName}
{
    "API_key": "secure123"
    "name": "InternalNuGet" 
}
```
or
```
POST /api/management/feeds/update/{FeedName}?key=secure123
{
    "name": "InternalNuGet" 
}
```

##### Update a Feed with an array of strings
```
POST /api/management/feeds/update/{FeedName}
{
    "API_key": "secure123"
    "connectors": ["PublicNuGet","PrivateInfragistics"]
}
```

Note that, in order to add a connector to a feed, will need to specify all connectors that the feed should use, in the order they should be used. This may require a list query, first.


#### Delete Entity {#delete data-title="Delete Entity"}

This returns a status of 200 (on success), 403 (api key not authorized), or 404 (entity not found), and an empty body.


```
POST /api/management/feeds/delete/{FeedName}
{
    "API_key": "secure123"
}
```
or
```
POST /api/management/feeds/delete/{FeedName}?key=secure123
```
