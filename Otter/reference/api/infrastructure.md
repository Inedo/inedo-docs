---
title: Infrastructure Management API Endpoints
subtitle: Infrastructure Management
keywords: otter,api,servers,environments,roles
---

<style type="text/css">
    th {
        font-weight: normal;
        font-style: italic;
    }
</style>

The Infrastructure Management API Endpoints offer a simple mechanism for querying, creating, and updating [server](../../core-concepts/servers), [environment](../../modeling-infrastructure/environments), and [server role](../../modeling-infrastructure/server-roles) entries; they are intended to automate the set-up and management of an Otter instance.

These API endpoints should be used instead of the [Native API Methods](../api#native) when possible, as they are much easier to use and will likely not change. {.announcement}

For security and simplicity, these endpoints require that an [API Key](../../administration/security#api-keys) is created first.

### Data Specification {#data-specification}

This endpoint sends and receives entries as [JSON](http://json.org/) objects.

<tab-block>
<tab name="Server">

Property          | Format
------------------|----------
`name`            | A *string* of no more than fifty characters: numbers (0-9), upper- and lower-case letters (a-Z), dashes (-), and underscores (_); must start with a letter, and may not start or end with a dash or underscore. Required.
`roles`           | An *array of strings*, each consisting of a server role name. Optional.
`environments`    | An *array of strings*, each consisting of an environment name. Optional.
`drift`           | A *string* value of `none`, `reportOnly`, or `automaticallyRedmediate`. Required.
`serverType`      | A *string* value of `windows`, `ssh`, `local`, or `powershell`. Required.
`hostName`        | A *string* of the hostname of the server. Required for `windows` and `ssh`.
`port`            | An *integer* of the port to use of the server. Required for `windows` and `ssh`.
`encryptionType`  | A *string* value of `aes`, `ssl`, or `none`. Required for `windows`.
`encryptionKey`   | A *string* containing exactly 32 hexidecimal characters. Required when `encryptionType` is `aes`.
`requireSsl`      | A *boolean* indicating whether to only connect using SSL. Required when `encryptionType` is `ssl`.
`credentialsType` | A *string* containing the type of resource credential to use. Required for `ssh`.
`credentialsName` | A *string* containing the name of a resource credential to use. Required for `ssh`. Optional for `powershell`.
`tempPath`        | A *string* containing the name of the temporary path to use for files. Required for `ssh` and `powershell`.
`wsManUrl`        | A *string* containing the WSMan endpoint. Optional for `powershell`.
`active`          | A *boolean* indicating whether the server is active or disabled. Optional.
`variables`       | An *object* with property/values representing variable names and values. Optional. <ul><li>a variable name is a *string* of no more than fifty characters: numbers (0-9), upper- and lower-case letters (a-Z), dashes (-), spaces ( ), and underscores (_) and must start with a letter, and may not start or end with a dash, underscore, or space; a variable</li><li>a variable value is a *string* of any number of characters</li></ul>

</tab>
<tab name="Server Role">

Property    | Format
------------|----------
`name`      | *same format as **server.name***
`variables` | *same format as **server.variables***

</tab>
<tab name="Environment">

Property     | Format
-------------|----------
`name`       | *same format as **server.name***
`parentName` | A *string* containing the name of the parent environment, or null if there is no parent environment. Optional.
`variables`  | *same format as **server.variables***

</tab>
</tab-block>

### Endpoint Specifications {#endpoints}

All of the infrastructure management endpoints follow the same convention:

POST ::/api/infrastructure/::{style="opacity: 0.5"}<b>&laquo;entry-type&raquo;</b>/<b>&laquo;action-type&raquo;</b>/<b>&laquo;entry-name&raquo;</b>?key=&laquo;api-key&raquo; {.info}

{.docs}
- `entry-type` is one of `servers`, `roles`, or `environments`
- `action-type` is one of `[list](#list)`, `[create](#create)`, `[update](#update)`, or `[delete](#delete)`
- `entry-name` is the name of the entry being created, updated, or deleted; it is not valid on a `list` action type

#### List Entries {#list}

This returns a status of 200 (on success), or 403 (api key not authorized), and a body containing only an *array* of entry objects.

<tab-block>
<tab name="List Servers">


```
POST /api/infrastructure/servers/list?key=secure123
```

```
[
  {
    "name": "hdarsintsv1",
    "roles": ["web","hdars"],
    "environments": ["integration"],
    ...
    "variables": {
                   "disk-path": "/var/hdars/1000",
                   "app-name": "hdars"
                 },
    "active": true
  },
  {
    "name": "mdapxsv",
    "roles": [],
    "environments": ["test1","test2"],
    ...
    "variables": {}
  },
  { ... }
]
```

</tab>
<tab name="List Server Roles">


```
POST /api/infrastructure/roles/list?key=secure123
```

```
[
  {
    "name": "web",
    "variables": {
                   "websites-root": "c:\webroots\"
                 }
  },
  {
    "name": "hdars",
    "variables": {}
  },    
  { ... }
]
```

</tab>
<tab name="List Environments">


```
POST /api/infrastructure/environments/list?key=secure123
```

```
[
  {
    "name": "integration",
    "parent": null,
    "variables": {
                   "database-alias": "intagration"
                 }
  },
  {
    "name": "testing",
    "parent": null,
    "variables": {
                   "database-alias": "test"
                 }
  },
  {
    "name": "test1",
    "parent": "testing",
    "variables": {}
  },
  {
    "name": "test2",
    "parent": "testing",
    "variables": {}
  },
  { ... }
]
```

</tab>
</tab-block>

The above is an example, and truncates responses for readability with ellipses; the API key used requires the `Infrastructure_View` permission.

#### Create Entry {#create}

This returns a status of 201 (on success), 403 (api key not authorized), or 422 (invalid entry), and an body containing either the entry object, or a description of the 422 status.

If the entity references another entity (e.g. in the `roles` property of a server, or the `parent` property of environment) and the referenced entity does not exist, an invalid entry (422) will be returned. {.announcement}

We opted not to provide an example, as the request body is simply a JSON object formatted like the [list](#list) examples. The API key used requires the `Infrastructure_Manage` permission.

#### Update Entry {#update}

This returns a status of 200 (on success), 403 (api key not authorized), 404 (entry not found), or 422 (invalid entry), and an body containing either the entry object, or a description of the 422 status.

If the entity references another entity (e.g. in the `roles` property of a server, or the `parent` property of environment) and the referenced entity does not exist, an invalid entry (422) will be returned. {.announcement}

If there are missing properties on the entity, only the specified properties will be updated. {.announcement}

<tab-block>
<tab name="Update Server">


```
POST /api/infrastructure/servers/update/hdarsintsv1?key=secure123

{
  "roles": ["web","hdars","code-server"],
  "encryption": "none"
}
```

Note that, in this case, the `encryptionKey` property would be removed on update because the encryption type changed.

</tab>
<tab name="Rename Server Role">


```
POST /api/infrastructure/roles/update/hdars?key=secure123

{ "name": "new-hdars"}
```

</tab>
<tab name="Remove Parent Environment">


```
POST /api/infrastructure/environments/update/test1?key=secure123

{ "parent" : null }
```

</tab>
</tab-block>

#### Delete Entry {#delete}

This returns a status of 200 (on success), 403 (api key not authorized), or 404 (entry not found), and an empty body.
