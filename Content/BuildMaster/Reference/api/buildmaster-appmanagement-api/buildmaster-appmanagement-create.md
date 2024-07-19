---
title: "Create Application Endpoint"
order: 6
---

The *Create Application* is an endpoint in [BuildMaster's Application Management API](/docs/buildmaster/reference/api/buildmaster-appmanagement-api) that will create a blank application and return an [ApplicationInfo](/docs/buildmaster/reference/api/buildmaster-appmanagement-api#application-info) object describing the created application.

:::(Info) (🚀 Quick Example: Creating an Application with Curl)
This example creates an application `newApp`, authenticating with the API key `abc12345`:

```
curl -X POST --header "X-ApiKey: abc12345" https://proget.corp.local/api/applications/create?name=myNewApp
```
:::

## Request Specification
To create a blank application, simply `POST` to the URL with an [appropriate API Key](/docs/buildmaster/reference/api/buildmaster-appmanagement-api#authentication) and a [ApplicationInfo](/docs/buildmaster/reference/api/buildmaster-appmanagement-api#application-info) object as the request body.

```
POST /api/applications/create
```

Querystring parameters may be used for [ApplicationInfo](/docs/buildmaster/reference/api/buildmaster-appmanagement-api#application-info) object properties instead of the Body

```
POST /api/applications/create?«ApplicationInfo-properties»
```

**Creating an application** with an `ApplicationInfo` body requires the name (e.g. `myNewApp`):

```json
POST /api/applications/create
{
    "name": "myNewApp"
}
```

**Creating an application** with querystring parameters requires the name (e.g. `myNewApp`):

```
POST /api/applications/create?name=myNewApp
```

##  Response Specification
A successful (`200`) response body will contain an [ApplicationInfo](/docs/buildmaster/reference/api/buildmaster-appmanagement-api#application-info) object. For example, to creating a blank application named `myNewApp`, the request would return this:

```json
POST /api/applications/create

{
    "id":1,
    "name":"myNewApp",
    "active":true,
    "buildNumberScheme":"Sequential",
    "releaseUsage":"Required",
    "allowIssues":false
}
```

| Response | Details |
| --- | --- |
| **200 (Success)** | creates an application, the body will contain a [ApplicationInfo Object](/docs/buildmaster/reference/api/buildmaster-appmanagement-api#application-info)
| **400 (Invalid Input)** | indicates invalid or missing properties on the [ApplicationInfo Object](/docs/buildmaster/reference/api/buildmaster-appmanagement-api#application-info); the body will provide some details as text
|  **403 (Unauthorized API Key)** | indicates a [missing, unknown, or unauthorized API Key](/docs/buildmaster/reference/api/buildmaster-appmanagement-api#authentication); the body will be empty
| **500 (Server Error)** | indicates an unexpected error; the body will contain the message and stack trace, and this will also be logged
