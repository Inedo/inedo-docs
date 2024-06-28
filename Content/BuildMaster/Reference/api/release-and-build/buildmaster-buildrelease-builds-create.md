---
title: "Create Build"
order: 5
---

The *Create Build* is an endpoint in [BuildMaster's Release & Build Deployment API](/docs/buildmaster/reference/api/release-and-build) that will create a build, with the parameters defined in the body of the request.

:::(Info) (🚀 Quick Example: Creating a build with Curl)
This example creates a build with a `releaseNumber` of `1.0.1` and the `applicationName` `TaxCalculator`, authenticating with the API key `abc12345`:

````
curl -X POST -H "X-ApiKey: abc12345" -H "Content-Type: application/json" -d "{\"releaseNumber\": \"1.0.1\", \"applicationName\": \"TaxCalculator\"}" "https://proget.corp.local/api/releases/builds/create"
````
:::

## Request Specification
```
POST/PUT /api/releases/builds/create
```
To create a build, simply `PUT` or `POST` to the URL with an [appropriate API Key](/docs/buildmaster/reference/api/release-and-build#authentication) and a request body, containing the following parameters:

| Input | Parameter Specification |
| --- | --- |
| Release | Required. Either a key named `releaseId` with an integer value, or a key named `releaseNumber` with any value. |
| Application | Required if `releaseNumber` is specified, otherwise must not be set. Either a key named `applicationId` with an integer value, or a key named `applicationName` with any value. |
| Build Number | Optional. A key named `buildNumber` with any value; if not specified, the build number will be automatically generated. |
| Variables | Optional. Any number of parameters with a key name consisting of a valid variable name prefixed with `$`, and with any value. |

**Creating a build** requires parameters defined in the request body.

```json
POST /api/releases/builds/create

{
  "releaseNumber": "1.0.1",
  "applicationName": "TaxCalculator"
}
```

## Response Specification
A successful (`200`) response body will contain a single [BuildInfo](/docs/buildmaster/reference/api/release-and-build#buildinfo-object) object. For example, creating a new build, with a `releaseNumber` of `5` returns:

```json
{
    "id": 7,
    "number": "3",
    "status": "active",
    "createdBy": "API",
    "createdOn": "2024-03-05T02:46:56.0800000Z",
    "applicationId": 2,
    "applicationName": "TaxCalculator",
    "pipelineName": "Release",
    "releaseId": 4,
    "releaseNumber": "1.0.1",
    "releaseName": "1.0.1",
    "buildNumber": "3"
}
```

| Response | Details |
|---|---|
| **200 (Success)** | a `build` will be created, the body will contain a [BuildInfo](/docs/buildmaster/reference/api/release-and-build#buildinfo-object) object |
| **400 (Invalid Input)** | indicates invalid or missing properties in the request; the body will provide some details as text |
| **403 (Unauthorized API Key)** | indicates a [missing, unknown, or unauthorized API Key](/docs/proget/reference-api/proget-api-licenses#authentication); the body will be empty |