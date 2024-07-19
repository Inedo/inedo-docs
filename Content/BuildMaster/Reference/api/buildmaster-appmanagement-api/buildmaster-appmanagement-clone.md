---
title: "Clone Application Endpoint"
order: 5
---

The *Clone Application* is an endpoint in [BuildMaster's Application Management API](/docs/buildmaster/reference/api/buildmaster-appmanagement-api) that can clone an existing application and return an [ApplicationInfo](/docs/buildmaster/reference/api/buildmaster-appmanagement-api#application-info) object describing the cloned application.

:::(Info) (🚀 Quick Example: Cloning an Application with Curl)
This example clones an existing application named `originalApp` and creates a clone `clonedApp`, authenticating with the API key `abc12345`:

```
curl -X POST --header "X-ApiKey: abc12345" https://proget.corp.local/api/applications/clone?name=clonedApp&from=originalApp
```
:::

## Request Specification
To clone an application, simply `POST` to the URL with an [appropriate API Key](/docs/buildmaster/reference/api/buildmaster-appmanagement-api#authentication) and a [CloneApplication](/docs/buildmaster/reference/api/buildmaster-appmanagement-api#clone-application) object as the request body.


```
POST /api/applications/clone
```

Querystring parameters may be used for [CloneApplication](/docs/buildmaster/reference/api/buildmaster-appmanagement-api#clone-application) object properties instead of the Body

```
POST /api/applications/clone?«CloneApplication-properties»
```

**Cloning an application** with an `CloneApplication` body requires the name of the new application (e.g. `clonedApp`) and the name of the existing application it will be cloned from (e.g. `originalApp`)

```json
POST /api/applications/clone
{
    "name": "clonedApp"
    "from": "originalApp"
}
```

**Cloning an application** with  querystring parameters requires the name of the new application (e.g. `clonedApp`) and the name of the existing application it will be cloned from (e.g. `originalApp`)
````
POST /api/applications/clone?name=clonedApp&from=originalApp
````

## Response Specification
A successful (`200`) response body will contain an [ApplicationInfo](/docs/buildmaster/reference/api/buildmaster-appmanagement-api#application-info) object. For example, to clone a application named `clonedApp` from an existing application named `originalApp`, the request would return this:

```json
POST /api/applications/clone

{
    "id":2,
    "name":"clonedApp",
    "active":true,
    "buildNumberScheme":"Sequential",
    "releaseUsage":"Required",
    "allowIssues":false
}
```

| Response | Details |
| --- | --- |
| **200 (Success)** | clones the application, body will contain a [ApplicationInfo Object](/docs/buildmaster/reference/api/buildmaster-appmanagement-api#application-info)
| **400 (Invalid Input)** | indicates invalid or missing properties on the [CloneApplication Object](/docs/buildmaster/reference/api/buildmaster-appmanagement-api#clone-application); the body will provide some details as text
|  **403 (Unauthorized API Key)** | indicates a [missing, unknown, or unauthorized API Key](/docs/buildmaster/reference/api/buildmaster-appmanagement-api#authentication); the body will be empty
| **500 (Server Error)** | indicates an unexpected error; the body will contain the message and stack trace, and this will also be logged