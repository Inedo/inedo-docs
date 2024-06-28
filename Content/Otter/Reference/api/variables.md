---
title: "Variables Management API"
order: 5
---

The Variables Management API offers a simple mechanism for viewing, creating, updating, and deleting [configuration variables](/docs/otter/scripting-in-otter/otter-configuration-variables).

:::(Warning)
This API endpoint should be used instead of the [Native API Methods](/docs/otter/reference/api#native-api-reference) when possible, as they are much easier to use and will likely not change.
:::

For security and simplicity, these endpoints require that an [API Key](/docs/otter/configuring-for-your-team/otter-administration-security-api-keys) is created first.

## Data Specification

This endpoint sends and receives a as [JSON](http://json.org/) objects.

### Variable Value Strings

If a variable value string starts with a `` ` ``, `@(`, `%(`, then the value will be evaluated as a `literal_expression` (see [formal grammar](/docs/executionengine/reference/otterscript-formal-grammar)), which means you'll need to treat the value as a proper [string literal](/docs/executionengine/otterscript/strings-and-literals), and escape `$` and other characters if you don't want them expanded into variables at runtime... or cause an error when they can't expand. {#variable-value-string .info}

### Variables Object

This represents a set of configuration variables associated with a single scope, such as global, a server, or an environment. It is simply an object with variable names and values as properties. The value can be either a string, or an object with two properties:

- `value` - a *string* value of the variable's value itself
- `sensitive` - a *boolean* to indicate a sensitive variable that's obscured from casual viewing in the UI

#### Basic Variables
```
{
    "installation-path": "/var/hdars/service",
    "dameon-name": "hdars-service"
}
```

#### Other Types
```
{
    "license-key": { "value": "5208153R-YXYUYK-S1CUAC-NA3SM2V3-A8QW", "sensitive": true },
    "listen-ports": "@(8088,8089,8090)",
    "license-key-file": "`license:$license-key`norganization:kramerica"
}
```

#### Empty Variables
```
{}
```

### Scoped Variable Object 

This represents a single variable/value that is associated with zero or more scopes, such as both a global, a server, or a multi-scoped server+environment variable.

Property      | Format
--------------|----------
`name`        | A *string* value of the variable name
`value`       | A *string* value of the variable value
`sensitive`   | A *boolean* to indicate a sensitive variable that's obscured from casual viewing in the UI
`server`      | A *string* value of the name of the server the variable is associated with, or null if it's not associated
`role`        | A *string* value of the name of the role the variable is associated with, or null if it's not associated
`environment` | A *string* value of the name of the environment the variable is associated with, or null if it's not associated

## Endpoint Specification

### Entity Configuration Variables

These endpoints get and set one or all configuration variables for a specific entity, which is identified by an `entity-type` (either `server`, `role`, or `environment`) and name.

#### Get All Configuration Variables on Entity

```
GET /api/variables/«entity-type»/«entity-name»?key=«api-key»
```

Returns a status of 200 and a body with the [variables object](#variables-object) for the specified entity.

This may also return a status of 400 (invalid entity type), 403 (invalid key), 404 (entity not found) as well as an error message as the body.

#### Set All Configuration Variables on Entity

```
POST or PUT /api/variables/«entity-type»/«entity-name»?key=«api-key»
```

Updates *all* configuration variables for the specified entity with the variables/values specified as a [variables object](#variables-object) in the request body; note this will also delete variables not specified in the object.

On success, a status of 200 and empty body is returned. Otherwise, a status of 400 (invalid entity type or variables object), 403 (invalid key), 404 (entity not found) is returned with an error message as the body.

#### Get Single Configuration Variable on Entity

```
GET /api/variables/«entity-type»/«entity-name»/«variable-name»?key=«api-key»
```

Returns the value of the specified configuration variable on the specified entity in the body, as well as a status of 200.

This may also return a status of 400 (invalid entity type), 403 (invalid key), 404 (entity or variable not found) as well as an error message as the body.

#### Set Single Configuration Variable on Entity
```
POST or PUT /api/variables/«entity-type»/«entity-name»/«variable-name»?key=«api-key»
```

Sets the specified configuration variable, either by creating a new variable or updating an existing one, on the specified entity with the value specified in the request body; note you cannot set the sensitive indicator with this endpoint, only set a value. When creating a configuration variable, it will not be considered sensitive; when updating, it will keep the same sensitive indicator.

On success, a status of 200 and empty body is returned. Otherwise, a status of 400 (invalid entity type), 403 (invalid key), 404 (entity not found) is returned with an error message as the body.

#### Delete Single Configuration Variable on Entity
```
DELETE /api/variables/«entity-type»/«entity-name»/«variable-name»?key=«api-key»
```

Deletes the specified configuration variable if it exists on the specified entity, returning a status of 200 and empty body. Otherwise, a status of 400 (invalid entity type) or 403 (invalid key) is returned with an error message as the body.

### Global Configuration Variables

These endpoints get and set one or all global (unscoped) configuration variables.

#### Get All Global Configuration Variables
```
GET /api/variables/global?key=«api-key»
```

Returns a status of 200 and a body with the [variables object](#variables-object) for all global variables.

This may also return a status of 403 (invalid key) as well as an error message as the body.

#### Set All Global Configuration Variables
```
POST or PUT /api/variables/global?key=«api-key»
```

Updates *all* global configuration variables with the variables/values specified as a [variables object](#variables-object) in the request body; note this will also delete variables not specified in the object.

On success, a status of 200 and empty body is returned. Otherwise, a status of 403 (invalid key) is returned with an error message as the body.

#### Get Single Global Configuration Variable
```
GET /api/variables/global/«variable-name»?key=«api-key»
```

Returns the value of the specified global configuration variable in the body, as well as a status of 200.

This may also return a status of 403 (invalid key) or 404 (variable not found)  as well as an error message as the body.

#### Set Single Global Configuration Variable
```
POST or PUT /api/variables/global/«variable-name»?key=«api-key»
```

Sets the specified global configuration variable, either by creating a new variable or updating an existing one, with the value specified in the request body; note you cannot set the sensitive indicator with this endpoint, only set a value. When creating a configuration variable, it will not be considered sensitive; when updating, it will keep the same sensitive indicator.

On success, a status of 200 and empty body is returned. Otherwise, a status 403 (invalid key) is returned with an error message as the body.

#### Delete Single Global Configuration Variable
```
DELETE /api/variables/global/«variable-name»?key=«api-key»
```

Deletes the specified global configuration variable if it exists, returning a status of 200 and empty body. Otherwise, a status of 403 (invalid key) is returned with an error message as the body.

### All Configuration Variables

These endpoints get and set one or all configuration variables using [scoped variables objects](#scoped-variable-object), which is primarily useful in dealing with multi-scoped variables. Otherwise, it's a bit more complicated to use than the other endpoints, and might lead towards accidentally deleting variables or creating unwanted multi-scoped variables.

#### Get All Configuration Variables
```
GET /api/variables/scoped/all?key=«api-key»
```

Returns a status of 200 and a body with an array of [scoped variable objects](#scoped-variable-object) for all configuration variables in the system.

This may also return a status of 403 (invalid key) as well as an error message as the body.

#### Set All Configuration Variables
```
POST or PUT /api/variables/scoped/all?key=«api-key»
```

Sets *all* configuration variables in the system using the array of [scoped variable objects](#scoped-variable-object) in the request body; note this will also delete variables not specified in the object.

On success, a status of 200 and empty body is returned. Otherwise, a status of 403 (invalid key) is returned with an error message as the body.

#### Set Single Configuration Variable
```
POST or PUT /api/variables/scoped/single?key=«api-key»
```

Sets the configuration variable, either by creating a new variable or updating an existing one with the same name and scoping, using the scoped variable object in the body.

On success, a status of 200 and empty body is returned. Otherwise, a status 400 (invalid object) or 403 (invalid key) is returned with an error message as the body.

#### Delete Single Configuration Variable
```
DELETE /api/variables/scoped/single?key=«api-key»
```

Deletes the configuration variable if it exists with a name and scope matching the scoped variable object in the body, returning a status of 200 and empty body. Otherwise, a status of 400 (invalid object) or 403 (invalid key) is returned with an error message as the body.