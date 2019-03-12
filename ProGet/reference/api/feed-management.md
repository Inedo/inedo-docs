---
title: Feed Management API
subtitle: Feed Management API
sequence: 200
keywords: proget,feeds
show-headings-in-nav: true
---

The Feed Management API Endpoints offer a simple mechanism for querying, creating, and updating [feeds], [connectors], [Vulnerability Sources], [License Rules], and related data. The primary use cases for these endpoints are to
 * provision and configure feeds with infrastructure automation tools such as Otter
 * allow external tools to query/monitor feed information and metadata
 * duplicate feed configuration with minimal effort
 * copy feed configuration across multiple ProGet instances
 * assist in receiving support by submitting configuration to engineers or the community

These API endpoints should be used instead of the [Native API Methods] when possible, as they are much easier to use and will likely not change. {.announcement}

For security and simplicity, these endpoints require that an [API Key] is created first.


### Data Specification {#data-specification}

This endpoint sends and receives entries as [JSON](http://json.org/) objects.


<tab-block>
<tab name="Feed">

Property               | Format
-----------------------|----------
`name`                 | A *string* of no more than fifty characters: numbers (0-9), upper- and lower-case letters (a-Z), dashes (-), and underscores (_); must start with a letter, and may not start or end with a dash or underscore. Required.
`feedType`             | A *string* value of `nuget`, `npm`, `universal`, ..., or `xxx`. Required.
`packageType`          | A *string* value of `chocolatey` or `powershell` (for `nuget` feeds) or `romp` (for `universal` feed types). See [Feed and Package Types](#feed-and-package-types). Optional.
`description`          | A *string* that is displayed in the ProGet UI under the feed namme.  Optional (default `null`).
`active`               | A *boolean* indicating whether the feed is active or disabled. Optional (default `true`).
`cacheConnectors`      | A *boolean* indicating whether metadata from packages should be cached (???). Optional (default `???`).
`dropPath`             | A *string* containing a disk path accessible to ProGet's service where packages may added to the feed. Optional (default `null`).
`packagesPath`         | A *string* containing an absolute or relative (to RootFeedPath?) path that will override where packages are stored. Optional (default `null`). 
`packageStore`         | A *string* containing serialized XML configuration for a custom package store like Amazon S3 or Azure Blob. See [Persisted XML](#persisted-xml). Optional (default `null`). 
`replicationMode`      | A *string* value of `none`, `listen`, `initiate`, or `listenAndInitiate`. Optional (default `none`).
`syncToken`            | A *string* containing the replication token used when replicating. Required when `replicationMode` is not `none`.
`syncUrl`              | A *string* containing URL of a replication endpoint to contact. Required when `replicationMode` is `listenAndInitiate` or `initiate`.
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

</tab>
<tab name="Connector">
TODO: convert this to a table of sorts...

 * name (100 chars). string. See [Names and IDs](#names-and-ids). Required.
 * url. string. Required.
 * feedType. *Same format as Feed.feedType*. Required.
 * userName. string. Optional.
 * password. string. Optional.
 * timeout. Int. Seconds? Required. 
 * filter. String array. Optional.
 * metadataCached. Bool. REquired (default ?)
 * metadataCacheMinutes. int. required (default ?).
 * metadataCacheCount. int. required (default ?).

</tab>
<tab name="Retention Rule (on Feed)">
Note that this is only used within a feed entity's retentionRules property, and cannot be defined outside of a feed.

TODO: convert this to a table of sorts...
 * deletePrereleaseVersions. boolean. Required.
 * deleteCached. boolean. Required.
 * keepVersionCount. Int. Optional (default null)
 * keepVersions string array. Optional. [[new in 5.2 with PG-1450]]
 * deleteVersions  string array. Optional. [[new in 5.2 with PG-1450]]
 * keepUsedWithinDays. Int. Optional (default null).
 * deletePackageIds. string array. Optional.
 * keepPackageIds. string array. Optional.
 * sizeTrigger. int. in KBytes. Optional.
 * sizeExclusive. bool. Required.
 * triggerDownloadCount. int. Optional.
 
</tab>

<tab name="License">

TODO: convert this to a table of sorts...
 * licenseId. SPDX identifier string of 50 chars. required.
 * title. string. required
 * urls. array of string. required.
 * allowed. bool. optional.
 * alllowedFeeds. string array. optional.
 * blockedFeeds. string array. optional.
 
</tab>
</tab-block>


### Feed and Package Types  {#feed-and-package-types}
In the ProGet UI, Chocolatey and PowerShell are represented as separate feed types, even though they just contain NuGet packages and use the same NuGet API. This is why, in the ProGet UI, you can change a feed's type between NuGet, Chocolatey, and PowerShell at any time, but not between other feed types.

In the Feed Management API, this is modeled with distinct feed and package types:
 * Chocolatey and PowerShell are not feed types, but instead special package types within a NuGet feed
 * Once a feed is created, the feed type cannot be changed, and specifying a different feed type on update will result in an error
 * Package types are optional, and supported ony on NuGet and Universal feed types

### Persisted XML Configuration {#persisted-xml}
Because package stores, package filters, and package access rules are implemented by extensible runtime components (i.e. you can create your own using the [Inedo SDK]), ProGet stores configuration for these components in "Persisted XML", which is a lightweight object serialization format that Inedo products use. Persisted XML is pretty simple format (as as far object serialization goes), but documenting it is beyond the scope of this API. 

The easiest way to see what an object's persisted XML will look like is to create it in the UI, then look at the XML in either the database or the API. As a reference, we've provided the persisted XML for three components that we maintain:

<tab-block>
<tab name="Amazon S3 Package Store">
TODO: Paste in XML
</tab>
<tab name="Azure Blob Package Store">
</tab>
<tab name="Whitesource Package Access Rule">
</tab>
</tab-block>

### Names and IDs {#names-and-ids}
Because one of the primary use cases of the Feed Management API is maintaining feed configuration across instances of ProGet, using or exposing the read-only, system-generated identifiers for entitites is not practical. As such, these IDs are not used by the API.

**Default Feed Disk Path.** When you don't specify a disk path for a feed to use, ProGet will use a path that's based on the feed's system-generated ID. This means that, just like in the UI, if were to delete a feed and then recreate it with the same name, the disk path will be different.

### Endpoint Specifications {#endpoints}
All of the feed management endpoints follow the same convention:

POST ::/api/management/::{style="opacity: 0.5"}<b>&laquo;entity-type&raquo;</b>/<b>&laquo;action-type&raquo;</b>/<b>&laquo;entity-name&raquo;</b>?key=&laquo;api-key&raquo; {.info}

- `entity-type` is one of `feeds`, `connectors`, or `licenses`
- `action-type` is one of `[list](#list)`, `[get](#get)`, `[create](#create)`, `[update](#update)`, or `[delete](#delete)`
- `entity-name` is the name of the entity being created, updated, or deleted; it is not valid on a `list` action type

#### List Entries {#list}

This returns a status of 200 (on success), or 403 (api key not authorized), and a body containing only an *array* of entity objects. 

<tab-block>
<tab name="List Feeds">

{.info}
```
POST /api/management/feeds/list?key=secure123
```

```
[
  {
    "name": "FilteredNuGet",
    "feedType": "nuget",
    "description": "License Filtered NuGet packages",         
    "active": true,
    "cacheConnectors": true,
    "replicationMode": "none",     
    "allowUnknownLicenses": false,
    "allowedLicenses": ["MIT","BSD"],
    "blockedLicenses": ["GPL3"],
    "symbolServerEnabled": true, 
    "stripSymbols": true,
    "stripSource": true,
    "connectors": ["PublicNuGet"],
    ...,
    "variables": 
    {
       "feedOwner": "atripp@initech.corp"
    }  
  },
  {
    "name": "ApprovedChoco",
    "feedType": "nuget",
    "packageType": "chocolatey",         
    ...
  },
  { ... }
]
```

</tab>
<tab name="List Connectors">

{.info}
```
POST /api/management/connectors/list?key=secure123
```

```
[
  {
    "name": "PublicNuGet",
    "url": "https://nuget", <-- fix this!
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

</tab>
<tab name="List Licenses">

This endpoint requires License_Manage if POST or PUT is specified.

{.info}
```
GET /api/management/licenses/list?key=secure123
```

```
[
  {
    "licenseId": "MIT", <--- make sure these are correct
    "title": "MIT License v1",
    "urls":  ["",""],
    "allowed": true,
    "allowedFeeds": [],
    "blockedFeeds": "InternalPackages"
  },
  {
    "licenseId": "GPL3",
    "title": "General Public License v3",
    "urls":  ["",""],
    "allowed": false,
    "allowedFeeds": "TestPackages",
    "blockedFeeds": []
  },
  { ... }
]
```

</tab>
</tab-block>

The above is an example, and truncates responses for readability with ellipses; the API key used requires the `FeedManagement_View` permission for GET requests, and License_Manage on the speific feed (or all feeds) if updating licenses.

Note that entities will be returned with all properties, and unset values will be null empty arrays, as appropriate.  


#### Get entity {#get}

This returns a status of 201 (on success), 403 (api key not authorized), or 404 (entity not found), and an body containing the entity object.

We opted not to provide an example, as the request body is simply a JSON object formatted like the [list](#list) examples. The API key used requires the `FeedManagement_View` permission.


#### Create entity {#create}

This returns a status of 201 (on success), 403 (api key not authorized), or 422 (invalid entity), and an body containing either the entity object, or a description of the 422 status.

If the entity references another entity (e.g. in the `connectors` property of a feed) and the referenced entity does not exist, an invalid entity (422) will be returned. {.announcement}

We opted not to provide an example, as the request body is simply a JSON object formatted like the [list](#list) examples. The API key used requires the `FeedManagement_Update` permission.

#### Update entity {#update}

This returns a status of 200 (on success), 403 (api key not authorized), 404 (entity not found), or 422 (invalid entity), and an body containing either the entity object, or a description of the 422 status.

If the entity references another entity (e.g. in the `connectors` property of a feed) and the referenced entity does not exist, an invalid entity (422) will be returned. {.announcement}

If there are missing properties on the entity, only the specified properties will be updated. Setting a null or empty array value will cause the corresponding values to be removed, if allowed. {.announcement}


<tab-block>
<tab name="Update Feed">

{.info}
```
POST /api/management/feeds/update/MyNuGets?key=secure123

{
  "name": "InternalNuGet" 
}
```

</tab>
<tab name="Associate Connectors with Feed">

{.info}
```
POST /api/management/feeds/update/ExternalNuGet?key=secure123

{
  "connectors": ["PublicNuGet","PrivateInfragistics"]
}
```

Note that, in order to add a connector to a feed, will need to specify all connectors that the feed should use, in the order they should be used. This may require a list query, first.

</tab>
</tab-block>

#### Delete entity {#delete}

This returns a status of 200 (on success), 403 (api key not authorized), or 404 (entity not found), and an empty body.