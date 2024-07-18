---
title: "Infrastructure Management"
order: 2
---

The Infrastructure Management API endpoints can query, create, and update servers, environments, and server roles; they are designed to automate the setup and management of a BuildMaster instance.

## API Key Usage
All of these endpoints require that an API key with Infrastructure Management access is passed into each request. The examples use a query string for simplicity, but you can also use a header, a form value, or a JSON property. For more information, see  [API Access and API Keys](/docs/buildmaster/reference/api/buildmaster-administration-security-api-keys) for more information.

### Data Specification

This endpoint sends and receives entries as [JSON](http://json.org/) objects.

#### Server


| Property | Format |
| --- | --- |
| `name` | A _string_ of no more than fifty characters: numbers (0–9), upper- and lower-case letters (a–z), dashes (-), and underscores (\_); must start with a letter, and may not start or end with a dash or underscore |
| `roles` | An _array of strings_, each consisting of a server role name |
| `environments` | An _array of strings_, each consisting of an environment name |
| `drift` | A _string_ value of `reportOnly`; this is always ignored by BuildMaster when importing or comparing, and is only used by Otter |
| `serverType` | A _string_ value of `windows`, `ssh`, `powershell`, or `local` |
| `agentType` | A _string_ value of `Inedo`, `Indeo Listener`, `SSH`, `PowerShell`, or `Local` |
| `agentVersion` | A _string_ value representing the version of an `Inedo` or `Inedo Listener` agent |
| `hostName` | A _string_ of the hostname of the server; this property is not present when the `type` is `local` |
| `port` | An _integer_ of the port to use of the server; this property is not present when the `type` is `local` |
| `encryptionType` | A _string_ value of `aes`, `ssl`, or `none`; this property is only present when the `type` is `windows` |
| `encryptionKey` | A _string_ containing exactly 32 hexidecimal characters; this property is only present when the `encryption` is `aes` |
| `requireSsl` | A _boolean_ indicating whether to only connect using SSL; this property is only present when the `encryption` is `ssl` |
| `credentialsName` | A _string_ containing the name of a resource credential to use; this property is only present when the `type` is `ssh` or `powershell` |
| `tempPath` | A _string_ containing the name of the temporary path to use for files; this property is only present when the `type` is `ssh` or `powershell` |
| `wsManUrl` | A _string_ containing the WSMan endpoint; this property is only present when the `type` is `powershell` |
| `active` | A _boolean_ indicating whether the server is active or disabled |
| `variables` | An _object_ with property/values representing variable names and values <ul> <li> A variable name is a _string_ of no more than fifty characters: numbers (0–9), upper- and lower-case letters (a–z), dashes (-), spaces ( ), and underscores (\_) and must start with a letter, and may not start or end with a dash, underscore, or space; a variable </li> <li> A variable value is a _string_ of any number of characters</li></ul> |

#### Server Role

| Property | Format |
| --- | --- |
| `name` | _same format as **server.name**_ |
| `variables` | _same format as **server.variables**_ |

#### Environment

| Property | Format |
| --- | --- |
| `name` | _same format as **server.name**_ |
| `parentName` | a _string_ containing the name of the parent environment, or null if there is no parent environment |
| `variables` | _same format as **server.variables**_ |
| `active` | A _boolean_ indicating whether the environment is active or disabled (as of BuildMaster 6.1.10) |


### Endpoint Specifications

All of the infrastructure management endpoints follow the same convention:

POST /api/infrastructure/**«entry-type»**/**«action-type»**/**«entry-name»**?key=«api-key»

*   `entry-type` is one of `servers`, `roles`, or `environments`
*   `action-type` is one of `[list](#list)`, `[create](#create)`, `[update](#update)`, or `[delete](#delete)`
*   `entry-name` is the name of the entry being created, updated, or deleted; it is not valid on a `list` action type

#### List Entries

This returns a status of 200 (on success), or 403 (api key not authorized), and a body containing only an _array_ of entry objects.

#### List Servers

`POST /api/infrastructure/servers/list?key=secure123`
```
[
  {
    "name": "hdarsintsv1",
    "roles": \["web","hdars"\],
    "environments": \["integration"\],
    ...
    "variables": { 
                   "disk-path": "/var/hdars/1000", 
                   "app-name": "hdars" 
                 },
    "active": true
  },
  {
    "name": "mdapxsv",
    "roles": \[\],
    "environments": \["test1","test2"\],
    ...
    "variables": {}
  },
  { ... }
]
```

#### List Server Roles

`POST /api/infrastructure/roles/list?key=secure123`
```
[
  {
    "name": "web",
    "variables": { 
                   "websites-root": "c:\\webroots\\"
                 }
  },
  {
    "name": "hdars",
    "variables": {}
  },    
  { ... }
]
```

#### List Environments

`POST /api/infrastructure/environments/list?key=secure123`
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

The above is an example, and truncates responses for readability with ellipses; the API key used requires the `Infrastructure_View` permission.

#### Create Entry

This returns a status of 201 (on success), 403 (api key not authorized), or 422 (invalid entry), and an body containing either the entry object, or a description of the 422 status.

If the entity references another entity (e.g., in the `roles` property of a server, or the `parent` property of environment) and the referenced entity does not exist, an invalid entry (422) will be returned.

We opted not to provide an example, as the request body is simply a JSON object formatted like the [list](/docs/buildmaster/reference/api/infrastructure#list) examples. The API key used requires the `Infrastructure_Manage` permission.

#### Update Entry

This returns a status of 200 (on success), 403 (api key not authorized), 404 (entry not found), or 422 (invalid entry), and an body containing either the entry object, or a description of the 422 status.

If the entity references another entity (e.g., in the `roles` property of a server, or the `parent` property of environment) and the referenced entity does not exist, an invalid entry (422) will be returned.

If there are missing properties on the entity, only the specified properties will be updated.

#### Update Server

`POST /api/infrastructure/servers/update/hdarsintsv1?key=secure123`
```
{
  "roles": \["web","hdars","code-server"\], 
  "encryption": "none"
}
```
Note that, in this case, the `encryptionKey` property would be removed on update because the encryption type changed.

#### Rename Server Role

`POST /api/infrastructure/roles/update/hdars?key=secure123`
```
{ "name": "new-hdars"}
```

#### Remove Parent Environment
`POST /api/infrastructure/environments/update/test1?key=secure123`

```
{ "parent" : null }
```

#### Delete Entry

This returns a status of 200 (on success), 403 (api key not authorized), or 404 (entry not found), and an empty body.