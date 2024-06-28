---
title: "Get Deployments"
order: 9
---

The *Get Deployment* is an endpoint in [BuildMaster's Release & Build Deployment API](/docs/buildmaster/reference/api/release-and-build) that will return an array of [DeploymentInfo](/docs/buildmaster/reference/api/release-and-build#buildinfo-object) objects describing deployments in the instance, based on the specified optional filter parameters.

:::(Info) (🚀 Quick Example: Getting all successful deployments with Curl)
This example lists all successful deployments, authenticating with the API key `abc12345`:

````
curl -X POST -H "X-ApiKey: abc12345" -H "Content-Type: application/json" -d "{\"status\": \"succeeded\"}" "https://proget.corp.local/api/releases/builds/deployments"
````
:::

## Request Specification
```
GET or POST /api/releases/builds/deployments
```
To get a list of deployments, simply `GET` or `POST` to the URL with an [appropriate API Key](/docs/buildmaster/reference/api/release-and-build#authentication) and a request body, applying the following optional filter parameters:

| Filter | Parameter Specification |
| --- | --- |
| Application | Either a key named `applicationId` with an integer value, or a key named `applicationName` with any value. |
| Release | Either a key named `releaseId` with an integer value, a key named `releaseNumber` with any value, or a key named `releaseName` with any value. |
| Build | Either a key named `buildId` with an integer value, or a key named `buildNumber` with any value. |
| Deployment | A key named `deploymentId` with an integer value. |
| Pipeline | A key named `pipelineName` with any value. |
| Stage | A key named `pipelineStageName` with any value. |
| Environment | Either a key named `environmentId` with an integer value, or a key named `environmentName` with any value. |
| Status | A key named `status` with a value of `pending`, `executing`, `succeeded`, `warned`, or `failed`. |

**Getting deployments** permits specified filters as the request body:

```json
GET /api/releases/builds/deployments

{
  "status": "succeeded"
}
```

## Response Specification
A successful (`200`) response body will contain an array of [DeploymentInfo](/docs/buildmaster/reference/api/release-and-build#deploymentinfo-object) objects. For example, getting all successful deployments returns:

```json
[
    {
        "id": 21,
        "plan": "Deploy",
        "status": "succeeded",
        "started": "2024-03-04T09:31:20.5100000Z",
        "ended": "2024-03-04T09:31:20.6100000Z",
        "createdBy": "SYSTEM",
        "createdOn": "2024-03-04T09:31:15.4900000Z",
        "pipelineName": "Release",
        "pipelineStageName": "Integration",
        "applicationId": 2,
        "applicationName": "ProfitCalculator",
        "releaseId": 4,
        "releaseNumber": "1.0.1",
        "releaseName": "1.0.1",
        "buildId": 6,
        "buildNumber": "2",
        "packageId": 6,
        "packageNumber": "2"
    },
    {
        "id": 19,
        "plan": "Build",
        "status": "succeeded",
        "started": "2024-03-04T09:27:09.9000000Z",
        "ended": "2024-03-04T09:27:10.6570000Z",
        "createdBy": "SYSTEM",
        "createdOn": "2024-03-04T09:27:04.8930000Z",
        "pipelineName": "Release",
        "pipelineStageName": "Build",
        "applicationId": 2,
        "applicationName": "ProfitCalculator",
        "releaseId": 4,
        "releaseNumber": "1.0.1",
        "releaseName": "1.0.1",
        "buildId": 6,
        "buildNumber": "2",
        "packageId": 6,
        "packageNumber": "2"
    },
    { ... }
]
```

| Response | Details |
|---|---|
| **200 (Success)** | an array of [DeploymentInfo](/docs/buildmaster/reference/api/release-and-build#deploymentinfo-object) objects will be returned |
| **400 (Invalid Input)** | indicates invalid or missing properties in the request; the body will provide some details as text |
| **403 (Unauthorized API Key)** | indicates a [missing, unknown, or unauthorized API Key](/docs/proget/reference-api/proget-api-licenses#authentication); the body will be empty |