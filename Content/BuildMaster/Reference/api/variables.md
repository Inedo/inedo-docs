---
title: "Variables Management"
order: 4
---

## API Usage

All of these endpoints require that an API key with Variables Management access be passed with each request. The examples use a query string for simplicity, but you could also use a header, a form value, or a JSON property. See _Using API Keys_ for more information.

## Data Specification

This endpoint sends and receives as [JSON](http://json.org/) objects.

### Variable Value Strings

If a variable value string starts with a `` ` ``, `@(`, `%(`, then the value will be evaluated as a `literal_expression` (see [formal grammar](/docs/executionengine/reference/otterscript-formal-grammar)), which means you'll need to treat the value as a proper [string literal](/docs/executionengine/otterscript/strings-and-literals), and escape `$` and other characters if you don't want them expanded into variables at runtime, otherwise this will cause an error when they can't expand.

### Variables Object

This represents a set of configuration variables associated with a single scope, such as global, a server, or an environment. It is simply an object with variable names and values as properties. The value can be either a string, or an object with two properties:

*   `value` - a _string_ value of the variable's value itself
*   `sensitive` - a _boolean_ to indicate a sensitive variable that's obscured from casual viewing in the UI
*   `evaluate` - a _boolean_ to indicate whether variables should be expanded when reading the value (available in BuildMaster 2022.14+)


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
    "license-key-file": "\`license:$license-key\`norganization:kramerica"
}
```

#### Empty Variables
```
{}
```

### Scoped Variable Object

This represents a single variable/value associated with zero or more scopes, such as a global, a server, or a multi-scoped server+ environment variable.


| Property | Format |
| --- | --- |
| `name` | A _string_ value of the variable name |
| `value` | A _string_ value of the variable value |
| `sensitive` | A _boolean_ to indicate a sensitive variable that's obscured from casual viewing in the UI |
| `evaluate` | A _boolean_ to indicate whether variables should be expanded when reading the value (available in BuildMaster 2022.14+) |
| `server` | A _string_ value of the name of the server that the variable is associated with, or null if it's not associated |
| `role` | A _string_ value of the name of the role that the variable is associated with, or null if it's not associated |
| `environment` | A _string_ value of the name of the environment that the variable is associated with, or null if it's not associated |
| `application` | A _string_ value of the name of the application that the variable is associated with, or null if it's not associated |
| `application-group` | A _string_ value of the name of the application-group that the variable is associated with, or null if it's not associated |



## Endpoint Specifications

### Entity Configuration Variables

These endpoints get and set one or all configuration variables for a specific entity, which is identified by a `entity-type` (either `server`, `role`, `application`, `application-group`, or `environment`) and name.

#### Get All Configuration Variables on Entity

GET /api/variables/«entity-type»/«entity-name»?key=«api-key»

Returns a status of 200 and a body with the [variables object](/docs/buildmaster/reference/api/variables#variables-object) for the specified entity.

This may also return a status of 400 (invalid entity type), 403 (invalid key), 404 (entity not found), as well as an error message as the body.

#### Set All Configuration Variables on Entity

POST or PUT /api/variables/«entity-type»/«entity-name»?key=«api-key»

Updates _all_ configuration variables for the specified entity with the variables/values specified as a [variables object](/docs/buildmaster/reference/api/variables#variables-object) in the request body; note that this will also delete variables not specified in the object.

On success, a status of 200 and empty body is returned. Otherwise, a status of 400 (invalid entity type or variables object), 403 (invalid key), 404 (entity not found) is returned with an error message as the body.

#### Get Single Configuration Variable on Entity

GET /api/variables/«entity-type»/«entity-name»/«variable-name»?key=«api-key»

Returns the value of the specified configuration variable on the specified entity in the body, as well as a status of 200.

This may also return a status of 400 (invalid entity type), 403 (invalid key), 404 (entity or variable not found), as well as an error message as the body.

#### Set Single Configuration Variable on Entity

POST or PUT /api/variables/«entity-type»/«entity-name»/«variable-name»?key=«api-key»

Sets the specified configuration variable, either by creating a new variable or updating an existing one, on the specified entity with the value specified in the request body; note that you cannot use this endpoint to set the sensitive indicator, only a value. When a configuration variable is created, it is not classified as sensitive; when it is updated, it retains the same sensitivity indicator.

On success, a status of 200 and empty body is returned. Otherwise, a status of 400 (invalid entity type), 403 (invalid key), 404 (entity not found) is returned with an error message as the body.

#### Delete Single Configuration Variable on Entity

DELETE /api/variables/«entity-type»/«entity-name»/«variable-name»?key=«api-key»

Deletes the specified configuration variable if it exists on the specified entity, returning a status of 200 and empty body. Otherwise, a status of 400 (invalid entity type) or 403 (invalid key) is returned with an error message as the body.

### Release and Build Configuration Variables

These endpoints get and set one or all configuration variables for a specific release or build. The operation and behavior are identical to the above _Entity Configuration Variables_ endpoints, except instead of `«entity-type»/«entity-name»` in the URL, the following are used for releases and builds, respectively:

*   `releases/«application-name»/«release-number»`
*   `builds/«application-name»/«release-number»/«build-number»`

For example, to _Delete a Single Configuration Variable_ on a release, the following pattern is used.

DELETE /api/variables/releases/«application-name»/«release-number»/«variable-name»?key=«api-key»

### Global Configuration Variables

These endpoints get and set one or all global (unscoped) configuration variables.

#### Get All Global Configuration Variables

GET /api/variables/**global**?key=«api-key»

Returns a status of 200 and a body with the [variables object](/docs/buildmaster/reference/api/variables#variables-object) for all global variables.

This may also return a status of 403 (invalid key) as well as an error message as the body.

#### Set All Global Configuration Variables

POST or PUT /api/variables/**global**?key=«api-key»

Updates _all_ global configuration variables with the variables/values specified as a [variables object](/docs/buildmaster/reference/api/variables#scoped-variable-object) in the request body; note this will also delete variables not specified in the object.

On success, a status of 200 and empty body is returned. Otherwise, a status of 403 (invalid key) is returned with an error message as the body.

#### Get Single Global Configuration Variables

GET /api/variables/**global**/«variable-name»?key=«api-key»

Returns the value of the specified global configuration variable in the body, as well as a status of 200.

This may also return a status of 403 (invalid key) or 404 (variable not found), as well as an error message as the body.

#### Set Single Global Configuration Variable

POST or PUT /api/variables/**global**/«variable-name»?key=«api-key»

Sets the specified global configuration variable, either by creating a new variable or updating an existing one, with the value specified in the request body; note that you cannot set the sensitivity indicator with this endpoint, only a value. When a configuration variable is created, it is not classified as sensitive; when it is updated, it keeps the same sensitivity indicator.

On success, a status of 200 and empty body is returned. Otherwise, a status 403 (invalid key) is returned with an error message as the body.

#### Delete Single Global Configuration Variables

DELETE /api/variables/**global**/«variable-name»?key=«api-key»

Deletes the specified global configuration variable if it exists, returning a status of 200 and empty body. Otherwise, a status of 403 (invalid key) is returned with an error message as the body.

### All Configuration Variables

These endpoints get and set one or all configuration variables using [scoped variables objects](/docs/buildmaster/reference/api/variables#scoped-variable-object), which is primarily useful in dealing with multi-scoped variables. Otherwise, it's a bit more complicated to use than the other endpoints, and might lead towards accidentally deleting variables or creating unwanted multi-scoped variables.

#### Get All Configuration Variables

GET /api/variables/**scoped/all**?key=«api-key»

Returns a status of 200 and a body with an array of [scoped variable objects](/docs/buildmaster/reference/api/variables#scoped-variable-object) for all configuration variables in the system.

This may also return a status of 403 (invalid key), as well as an error message as the body.

#### Set All Configuration Variables

POST or PUT /api/variables/**scoped/all**?key=«api-key»

Sets _all_ configuration variables in the system using the array of [scoped variable objects](/docs/buildmaster/reference/api/variables#scoped-variable-object) in the request body; note this will also delete variables not specified in the object.

On success, a status of 200 and empty body is returned. Otherwise, a status of 403 (invalid key) is returned with an error message as the body.

#### Set Single Configuration Variable

POST or PUT /api/variables/**scoped/single**?key=«api-key»

Sets the a configuration variable, either by creating a new variable or updating an existing one with the same name and scoping, using the scoped variable object in the body.

On success, a status of 200 and empty body is returned. Otherwise, a status 400 (invalid object) or 403 (invalid key) is returned with an error message as the body.

#### Delete Single Configuration Variable

DELETE /api/variables/**scoped/single**?key=«api-key»

Deletes the a configuration variable if it exists with a name and scope matching the scoped variable object in the body, returning a status of 200 and empty body. Otherwise, a status of 400 (invalid object) or 403 (invalid key) is returned with an error message as the body.