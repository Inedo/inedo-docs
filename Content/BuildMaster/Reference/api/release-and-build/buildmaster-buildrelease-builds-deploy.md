---
title: "Deploy Build"
order: 6
---

The *Deploy Build* is an endpoint in [BuildMaster's Release & Build Deployment API](/docs/buildmaster/reference/api/release-and-build) that will deploy a build, with the parameters defined in the body of the request. This endpoint will return a [DeploymentInfo](/docs/buildmaster/reference/api/release-and-build#buildinfo-object) object.

:::(Info) (🚀 Quick Example: Creating a build with Curl)
This example deploys a build with a `buildNumber` of `2`, `releaseNumber` of `1.0.1` and the `applicationName` `TaxCalculator`, authenticating with the API key `abc12345`:

````
curl -X POST -H "X-ApiKey: abc12345" -H "Content-Type: application/json" -d "{\"buildNumber\": \"2\", \"releaseNumber\": \"1.0.1\", \"applicationName\": \"TaxCalculator\"}" "https://proget.corp.local/api/releases/builds/deploy"
````
:::

## Request Specification
```
POST/PUT /api/releases/builds/deploy
```
To deploy a build, simply `PUT` or `POST` to the URL with an [appropriate API Key](/docs/buildmaster/reference/api/release-and-build#authentication) and a request body, containing the following parameters:

| Input | Parameter Specification |
| --- | --- |
| Build | Required. Either a key named `buildId` with an integer value, or a key named `buildNumber` with any value. |
| Release | Required if `buildNumber` is specified, otherwise must not be set. Either a key named `releaseId` with an integer value, or a key named `releaseNumber` with any value. |
| Application | Required if `releaseNumber` is specified, otherwise must not be set. Either a key named `applicationId` with an integer value, or a key named `applicationName` with any value. |
| To Stage | Optional. A key named `toStage` with any value. If not supplied, the next stage in the pipeline will be used. |
| Force | Optional. A key named `force` with a value of `true` or `false`. |
| Start Time | Optional. A key named `startTime` with a timestamp of when the deployment should start. If not supplied, the deployment will start immediately. If the timestamp does not contain a date, it defaults to today. If a date is specified without a time, it defaults to midnight. If there is no time zone specified, it defaults to the server's local time zone. _Available in BuildMaster 6.0.2+_ |
| Variables | Optional. Any number of parameters with a key name consisting of a valid variable name prefixed with `$`, and with any value. |

**Deploying a build** requires parameters defined in the request body.

```json
POST /api/releases/builds/deploy

{
  "buildNumber": "2",
  "releaseNumber": "5",
  "applicationName": "TaxCalculator"
}
```

## Response Specification
A successful (`200`) response body will contain a single [DeploymentInfo](/docs/buildmaster/reference/api/release-and-build#buildinfo-object) object. For example, deploying a build, with the `applicationName` of `TaxCalculator` returns:

```json
{
    "id": 20,
    "plan": null,
    "status": "pending",
    "started": "2024-03-04T09:31:15.3470000Z",
    "ended": null,
    "createdBy": "API",
    "createdOn": "2024-03-04T09:31:15.3470000Z",
    "pipelineName": "Release",
    "pipelineStageName": "Integration",
    "applicationId": 2,
    "applicationName": "TaxCalculator",
    "releaseId": 4,
    "releaseNumber": "1.0.1",
    "releaseName": "1.0.1",
    "buildId": 6,
    "buildNumber": "2",
    "packageId": 6,
    "packageNumber": "2"
}
```

| Response | Details |
|---|---|
| **200 (Success)** | a `build` will be deployed, the body will contain a [DeploymentInfo](/docs/buildmaster/reference/api/release-and-build#deploymentinfo-object) object |
| **400 (Invalid Input)** | indicates invalid or missing properties in the request; the body will provide some details as text |
| **403 (Unauthorized API Key)** | indicates a [missing, unknown, or unauthorized API Key](/docs/proget/reference-api/proget-api-licenses#authentication); the body will be empty |