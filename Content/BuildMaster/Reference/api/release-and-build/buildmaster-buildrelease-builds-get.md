---
title: "Get Builds"
order: 7
---

The *Get Builds* is an endpoint in [BuildMaster's Release & Build Deployment API](/docs/buildmaster/reference/api/release-and-build) that will return an array of [BuildInfo](/docs/buildmaster/reference/api/release-and-build#buildinfo-object) objects describing builds in the instance, based on the specified optional filter parameters.

:::(Info) (🚀 Quick Example: Getting all active builds with Curl)
This example lists all active builds, authenticating with the API key `abc12345`:

````
curl -X POST -H "X-ApiKey: abc12345" -H "Content-Type: application/json" -d "{\"status\": \"active\"}" "https://buildmaster.corp.local/api/releases/builds"
````
:::

## Request Specification
```
GET/POST /api/releases/builds
```
To get a list of builds, simply `GET` or `POST` to the URL with an [appropriate API Key](/docs/buildmaster/reference/api/release-and-build#authentication) and a request body, applying the following optional filter parameters:

| Filter | Parameter Specification |
| --- | --- |
| Application | Either a key named `applicationId` with an integer value, or a key named `applicationName` with any value. |
| Release | Either a key named `releaseId` with an integer value, a key named `releaseNumber` with any value, or a key named `releaseName` with any value. |
| Build | Either a key named `buildId` with an integer value, or a key named `buildNumber` with any value. |
| Pipeline | A key named `pipelineName` with any value. |
| Stage | A key named `furthestStage` with any value. |
| Status | A key named `status` with a value of `active`, `rejected`, or `deployed`. |

**Getting builds** permits specified filters as the request body:

```json
GET /api/releases/builds

{
  "status": "active"
}
```

## Response Specification
A successful (`200`) response body will contain an array of [BuildInfo](/docs/buildmaster/reference/api/release-and-build#buildinfo-object) objects. For example, getting all active build returns:

```json
[
    {
        "id": 5,
        "number": "1",
        "status": "active",
        "createdBy": "MarkSmith",
        "createdOn": "2024-03-04T09:04:06.4930000Z",
        "applicationId": 2,
        "applicationName": "ProfitCalculator",
        "pipelineName": "Release",
        "furthestStage": "Build",
        "releaseId": 4,
        "releaseNumber": "1.0.1",
        "releaseName": "1.0.1",
        "buildNumber": "1"
    },
    {
        "id": 1,
        "number": "1",
        "status": "active",
        "createdBy": "JohnSmith",
        "createdOn": "2024-03-04T08:17:08.1000000Z",
        "applicationId": 1,
        "applicationName": "TaxCalculator",
        "releaseId": 3,
        "releaseNumber": "2.0.1",
        "releaseName": "2.0.1",
        "buildNumber": "1"
    },
    { ... }
]
```

| Response | Details |
|---|---|
| **200 (Success)** | an array of [BuildInfo](/docs/buildmaster/reference/api/release-and-build#buildinfo-object) objects will be returned |
| **400 (Invalid Input)** | indicates invalid or missing properties in the request; the body will provide some details as text |
| **403 (Unauthorized API Key)** | indicates a [missing, unknown, or unauthorized API Key](/docs/buildmaster/reference/api/release-and-build#authorization); the body will be empty |