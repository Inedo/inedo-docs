---
title: "Server Configuration Management API"
order: 5
---

The Server Configuration Management API offer a simple mechanism for querying the configuration status of [servers](/docs/otter/connecting-to-your-servers-with-otter/otter-servers-in-otter), [environments](/docs/otter/connecting-to-your-servers-with-otter/otter-modeling-infrastructure-environments), and [server roles](/docs/otter/connecting-to-your-servers-with-otter/otter-modeling-infrastructure-server-roles), as well as remediating drift.

:::(Warning)
This API endpoint should be used instead of the [Native API Methods](/docs/otter/reference/api#native-api-reference) when possible, as they are much easier to use and will likely not change.
:::

For security and simplicity, these endpoints require that an [API Key](/docs/otter/reference/api#native-api-reference) is created first.

### Data Specification

This endpoint sends and receives entries as [JSON](http://json.org/) objects.

#### Configuration Status Object

This represents the current configuration status for a server, server role, or environment.

Property               | Format
-----------------------|----------
`type`                 | A *string* value of `server`, `role`, or `environment`
`name`                 | A *string* value with the name of the server, role, or environment
`status`               | A *string* value of: <ul><li>`current`</li><li>`drifted`</li><li>`executing` - the server is currently involved in a collection or remediation execution</li><li>`pendingRemediation` - drifted, but with a remediation job scheduled</li><li>`unknown` - no configuration data collected</li><li>`disabled` - the server is marked inactive in the UI</li><li>`error` - an error occurred during collection or remediation</li></ul>
*`errorText`*          | When `status` is `error`, this will contain information about the nature of the error (agent failure, execution failure, etc); this property will be missing if the `status` is not `error`
`collectionDate`       | A *string* value with an ISO 8601 formatted date (yyyy-MM-ddThh:mm:ss) of the most recently collection date, or null if there has been no collection
*`latestCollectionId`* | When `type` is `server`, an *integer* identifying either the execution used for the current (when `status` is `executing`) or most recent collection run; this property will be null if no execution occurred, and will be missing if the `type` is not `server`
*`remediationDate`*    | When a remediation job is pending, a *string* value with an ISO 8601 formatted date (yyyy-MM-ddThh:mm:ss) of when the configuration drift will be remediated with a job; otherwise this property will be missing
*`remediationId`*      | An *integer* identifying the job used for remediating the configuration drift; otherwise this property will be missing

#### Server Configuration Item Object

This represents data about a single configuration item (such as a file or application) that was collected on a server.

Property            | Format
--------------------|----------
`type`              | A *string* containing the type returned by the configuration item (ex. File)
`key`               | A *string* uniquely identifying the configuration item among others of the same `type` (ex. C:\hdarfs.txt)
`status`            | A *string* value of `current`, `drifted`, or `unknown`
`collectionDate`    | A *string* value with an ISO 8601 formatted date (yyyy-MM-ddThh:mm:ss) of when this configuration was collected
`collectionId`      | An *integer* identifying the execution used for collecting the configuration
`collectionRole`    | An *string* value with the name of the role where the configuration is defined, or null if the configuration was defined on the server itself
*`remediationDate`* | When a remediation job is pending, a *string* value with an ISO 8601 formatted date (yyyy-MM-ddThh:mm:ss) of when the configuration drift will be remediated with a job; otherwise this property will be missing
*`remediationId`*   | An *integer* identifying the job used for remediating the configuration drift; otherwise this property will be missing

### Get Configuration Status
```
GET or POST /api/configuration/status/?key=«api-key»&«entity-type»=«entity-name»
```
Gets a [configuration status object](#configuration-status-object) for the specified `entity-type` (either `server`, `role`, or `environment`), or an array of all objects if no entity type is specified. A status of 200 (on success),  400 (invalid entity type), 403 (invalid key), 404 (server not found) is also returned.


#### Server Status
```
POST /api/configuration/status?key=secure123&server=mywebsv1
```

```
{
    "type": "server",
    "name": "mywebsv1",
    "status": "current",
    "collectionDate": "2016-04-13T03:28:11",
    "latestCollectionId": 10238
}
```
#### Role Status
```
POST /api/configuration/status?key=secure123&role=hdars
```

```
{
    "type": "role",
    "name": "hdars",
    "status": "pendingRemediation",
    "collectionDate": "2016-04-13T03:28:11",
    "remediationId": 233
}
```

#### All Statuses
```
POST /api/configuration/status?key=secure123
```

```
[{
    "type": "role",
    "name": "hdars",
    "status": "pendingRemediation",
    "collectionDate": "2016-04-13T03:28:11",
    "remediationId": 233
},
{
    "type": "role",
    "name": "sdmweb",
    "status": "current",
    "collectionDate": "2016-04-13T04:21:11"
},
{
    "type": "environment",
    "name": "production",
    "status": "current",
    "collectionDate": "2016-04-13T07:21:14"
},
{
    "type": "server",
    "name": "mywebsv1",
    "status": "current",
    "collectionDate": "2016-04-13T03:28:11",
    "latestCollectionId": 10238
}]
```
### Trigger Configuration Check

```
GET or POST /api/configuration/check?key=«api-key»&«entity-type»=«entity-name»
```

Triggers a *routine server configuration* check of a specific `entity-name` (`server` or `role`), or all servers/roles if not supplied; this will otherwise occur every sixty minutes (by default) or when a user clicks the [Check Configuration] button in the UI. A status of 200 (on success) or 403 (invalid key) and an empty body is returned.

**Future Enhancement?** - this endpoint could return one or more *collectionId* values that could be checked against the collectionId [configuration status object](#configuration-status-object), to make sure it is the same. This would simplify the current process of checking for recent configuration changes (get collectionId, trigger run, keep getting collectionId until it changes). Please [let us know](https://inedo.com/contact) if you think we should add this. 

### Trigger Remediation Job
```
GET or POST /api/configuration/remediate/«entity-type»/«entity-value»?key=«api-key»&job=«name»&start=«date»&simulate=«bool»
```
Creates a remediation job for the specified `entity-type` (either `server`, `role`, or `environment`), with an optional job name, start date (ISO 8601 formatted, `yyyy-MM-ddThh:mm:ss`), and simulation mode, returning a status of 200 (on success), 400 (incorrect parameters), 404 (entity not found), or 403 (invalid key), and a body containing only a *job token*. If the start date is not set, the job will be triggered to run.

#### Simple GET

```
GET /api/configuration/remediate/server/mywebsv1?key=secure123
```

```
Status: 200
Body: 471
```

#### POST with Parameters
```
POST /api/configuration/remediate/role/hdars?key=secure123&job=autoremediate&start=2016-04-21T23:00:00&simulate=false
```

```
Status: 200
Body: 1871
```

The returned token is *currently* the integer-based identifier used for jobs; however, please treat this as a string of arbitrary length, just in case the implementation detail changes to a GUID... or maybe even a [LongGuid](https://www.nuget.org/packages/Rubbishsoft.LongGuid/).

### Remediation Job Status Endpoint
```
GET or POST /api/configuration/remediate/status?key=«api-key»&token=«job-token»
```

Gets the status of a job that was created with the [trigger remediation](#trigger-remediation) endpoint, using the job token returned by that method. The API returns a status of 200 (on success), 400 (invalid parameters), or 403 (invalid key), and a body containing one of the following strings:

-  `pending` - indicates that the job dispatcher has not yet begun execution; this could mean the job is set to run in the future, or the Otter service is disabled
- `completed` - indicates that the job is complete (and did not end in a faulted state)
- `faulted` - indicates that the job is complete but ended in an Error state
- `running` - indicates that the job has not yet completed
- `disabled` - indicates that the job was disabled

### Get Server Configuration
```
GET or POST /api/configuration/server/«server-name»?key=«api-key»
```
Gets a JSON array of [server configuration objects](#server-configuration-item-object) for the specified server as the body. A status of 200 (on success), 403 (invalid key), 404 (server not found) is also returned.
```

[
  {
    "type": "File",
    "key": "C:\\hdirs.txt,
    "status": "current",
    "collectionDate": "2016-04-13T03:28:11",
    "collectionId": 1237,
    "collectionRole": null
  },
  {
    "type": "IIS App Pool",
    "key": "HDarsApp,
    "status": "drifted",
    "collectionDate": "2016-04-13T03:28:11",
    "collectionId": 1237,
    "collectionRole": "hdars",
    "remediationDate": "2016-04-14T04:00:00",
    "remediationId": 382
  },
  {
    "type": "IIS App Site",
    "key": "HDarsSite,
    "status": "drifted",
    "collectionDate": "2016-04-13T03:28:11",
    "collectionId": 1237,
    "collectionRole": "hdars",
    "remediationDate": "2016-04-14T04:00:00",
    "remediationId": 382
  }
]
```