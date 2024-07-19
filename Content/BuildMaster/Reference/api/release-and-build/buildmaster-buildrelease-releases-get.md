---
title: "Get Releases"
order: 2
---

The *Get Releases* is an endpoint in [BuildMaster's Release & Build Deployment API](/docs/buildmaster/reference/api/release-and-build) that will return an array of [ReleaseInfo](/docs/buildmaster/reference/api/release-and-build#releaseinfo-object) objects describing releases in the instance, based on the specified optional filter parameters.

:::(Info) (🚀 Quick Example: Getting all active releases with Curl)
This example lists all active releases, authenticating with the API key `abc12345`:

````
curl -X POST -H "X-ApiKey: abc12345" -H "Content-Type: application/json" -d "{\"status\": \"active\"}" "https://proget.corp.local/api/releases"
````
:::

## Request Specification
To get a list of releases, simply `GET` or `POST` to the URL with an [appropriate API Key](/docs/buildmaster/reference/api/release-and-build#authentication) and a request body, applying the following optional filter parameters:

| Filter | Parameter Specification |
| --- | --- |
| Application | Either a key named `applicationId` with an integer value, or a key named `applicationName` with any value. |
| Release | Either a key named `releaseId` with an integer value, a key named `releaseNumber` with any value, or a key named `releaseName` with any value. |
| Pipeline | A key named `pipelineName` with any value. |
| Status | A key named `status` with a value of `active`, `canceled`, or `deployed`. |

```
GET/POST /api/releases
```

**Getting releases** permits specified filters as the request body:

```json
GET /api/releases

{
  "status": "active"
}
```

## Response Specification
A successful (`200`) response body will contain an array of [ReleaseInfo](/docs/buildmaster/reference/api/release-and-build#releaseinfo-object) objects. For example, getting all active releases returns:

```json
[
  {
    "id": 4,
    "number": "1.0.1",
    "name": "1.0.1",
    "sequence": 2,
    "status": "active",
    "createdBy": "MikeJones",
    "createdOn": "2024-03-04T09:03:43.9200000Z",
    "applicationId": 2,
    "applicationName": "ProfitCalculator",
    "pipelineName": "Release",
    "furthestBuildId": 6,
    "furthestBuildNumber": "2",
    "latestBuildId": 6,
    "latestBuildNumber": "2",
    "deployables": [],
    "furthestPackageId": 6,
    "furthestPackageNumber": "2",
    "latestPackageId": 6,
    "latestPackageNumber": "2",
    "releaseName": "1.0.1",
    "releaseNumber": "1.0.1"
  },
  {
    "id": 1,
    "number": "1",
    "name": "1",
    "sequence": 1,
    "status": "active",
    "createdBy": "JohnSmith",
    "createdOn": "2024-03-04T08:42:38.8170000Z",
    "applicationId": 1,
    "applicationName": "TaxCalculator",
    "deployables": [],
    "releaseName": "1",
    "releaseNumber": "1"
  },
  { ... }
]
```

| Response | Details |
|---|---|
| **200 (Success)** | an array of [ReleaseInfo](/docs/buildmaster/reference/api/release-and-build#releaseinfo-object) objects will be returned |
| **400 (Invalid Input)** | indicates invalid or missing properties in the request; the body will provide some details as text |
| **403 (Unauthorized API Key)** | indicates a [missing, unknown, or unauthorized API Key](/docs/proget/reference-api/proget-api-licenses#authentication); the body will be empty |