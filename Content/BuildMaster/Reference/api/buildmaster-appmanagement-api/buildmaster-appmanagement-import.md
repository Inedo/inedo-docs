---
title: "Import Application Endpoint"
order: 4
---

The *Import Application* is an endpoint in [BuildMaster's Application Management API](/docs/buildmaster/reference/api/buildmaster-appmanagement-api) that can create an application from an exported package (i.e., template, sample, or backup) and return an [ApplicationInfo](/docs/buildmaster/reference/api/buildmaster-appmanagement-api#application-info) object describing the imported application.

:::(Info) (🚀 Quick Example: Importing an Application with Curl)
This example creates an application `ImportedApp` by importing version `1.0.0` of an application `MyApp`, authenticating with the API key `abc12345`:

```
curl -X POST -H "Content-Type: application/json" -H "X-ApiKey: abc12345" -d "{\"name\": \"ImportedApp\",\"packageSource\": \"BackupResource\",\"packageId\": \"MyApp\",\"packageVersion\": \"1.0.0\",\"importHistory\": true,\"overwrite\": false,\"raft\": null}" https://buildmaster.corp.local/api/applications/import
```
:::

Importing and Exporting in BuildMaster can be quite complex, see [Migrating Application-by-Application](/docs/buildmaster/installation-maintenance/buildmaster-migrating-instance-to-new-server#additional-migration-notes) for more details on performing these. 

## Request
To import an application, simply `POST` to the URL with an [appropriate API Key](/docs/buildmaster/reference/api/buildmaster-appmanagement-api#authentication) and an  [ImportApplication](/docs/buildmaster/reference/api/buildmaster-appmanagement-api#import-application) object as the request body.
````
POST /api/applications/import
````
Querystring parameters may be used for [ImportApplication](/docs/buildmaster/reference/api/buildmaster-appmanagement-api#import-application) object properties instead of the Body
````
POST /api/applications/import?«ImportApplication-properties»
````

**Importing an application** with an `ImportApplication` body requires the URL and the body in the request:

```json
POST /api/applications/import
{
   "name": "ImportedApp",
   "packageSource": "BackupResource",
   "packageId": "MyApp",
   "packageVersion": "1.0.0",
   "importHistory": true,
   "overwrite": false,
   "raft": null
}
```

**Importing an application** with querystring parameters requires the name (e.g. importedApp) and other querystring parameters specified in the [ImportApplication](/docs/buildmaster/reference/api/buildmaster-appmanagement-api#import-application) object

```
POST /api/applications/import?name=ImportedApp&packageSource=BackupResource&packageId=MyApp&packageVersion=1.0.0&importHistory=true&overwrite=false&raft=null
```

## Response
A successful (`200`) response body will contain an [ApplicationInfo](/docs/buildmaster/reference/api/buildmaster-appmanagement-api#application-info) object. For example, to import a application named `ImportedApp`:

the request would return this:

```json
POST /api/applications/clone

{
    "id":3,
    "name":"ImportedApp",
    "active":true,
    "buildNumberScheme":"Sequential",
    "releaseUsage":"Required",
    "allowIssues":false
}
```

| Response | Details |
| --- | --- |
| **200 (Success)** | body will contain a [ApplicationInfo Object](/docs/buildmaster/reference/api/buildmaster-appmanagement-api#application-info)
| **400 (Invalid Input)** | indicates invalid or missing properties on the [ImportApplication Object](/docs/buildmaster/reference/api/buildmaster-appmanagement-api#import-application); the body will provide some details as text
|  **403 (Unauthorized API Key)** | indicates a [missing, unknown, or unathorized API Key](/docs/buildmaster/reference/api/buildmaster-appmanagement-api#authentication); the body will be empty
| **500 (Server Error)** | indicates an unexpected error; the body will contain the message and stack trace; this will also be logged