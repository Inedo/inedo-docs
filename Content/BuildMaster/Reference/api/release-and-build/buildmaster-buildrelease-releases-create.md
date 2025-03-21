---
title: "Create Release"
order: 1
---

The *Create Release* is an endpoint in [BuildMaster's Release & Build Deployment API](/docs/buildmaster/reference/api/release-and-build) that will create a release, with the parameters defined in the body of the request.

:::(Info) (🚀 Quick Example: Creating a release with Curl)
This example creates a release with a `releaseNumber` of `2.0.1` and the `applicationName` `TaxCalculator`, and the `pipelineName` `Release`, authenticating with the API key `abc12345`:

````
curl -X POST -H "X-ApiKey: abc12345" -H "Content-Type: application/json" -d "{\"releaseNumber\": \"2.0.1\", \"applicationName\": \"TaxCalculator\",  \"pipelineName\": \"Release\"}" "https://proget.corp.local/api/releases/create"
````
:::

## Request Specification
```
POST/PUT /api/releases/create
```
To create a release, simply `PUT` or `POST` to the URL with an [appropriate API Key](/docs/buildmaster/reference/api/release-and-build#authentication) and a request body, containing the following parameters:

| Input | Parameter Specification |
| --- | --- |
| Application | Required. Either a key named `applicationId` with an integer value, or a key named `applicationName` with any value. |
| Release Number | Required. A key named `releaseNumber` with any value. |
| Pipeline | Required. A key named `pipelineName` with any value. |
| Release Name | Optional. A key named `releaseName` with any value. |
| Variables | Optional. Any number of parameters with a key name consisting of a valid variable name prefixed with `$`, and with any value. |

**Creating a release** requires parameters defined in the request body.

```json
POST /api/releases/create

{
  "applicationName": "TaxCalculator",
  "releaseNumber": "2.0.1",
  "pipelineName": "Release",
}
```

## Response Specification
A successful (`200`) response body will contain a single [ReleaseInfo](/docs/buildmaster/reference/api/release-and-build#releaseinfo-object)  object. For example, creating a new release, with the `applicationName` `TaxCalculator` returns:

```json
{
  "id": 5,
  "number": "2.0.1",
  "name": "2.0.1",
  "sequence": 3,
  "status": "active",
  "createdBy": "API",
  "createdOn": "2024-03-05T05:06:27.6630000Z",
  "applicationId": 2,
  "applicationName": "TaxCalculator",
  "pipelineName": "Release",
  "deployables": [],
  "releaseName": "2.0.1",
  "releaseNumber": "2.0.1"
}
```

| Response | Details |
|---|---|
| **200 (Success)** | a `release` will be created, the body will contain a [ReleaseInfo](/docs/buildmaster/reference/api/release-and-build#releaseinfo-object) object |
| **400 (Invalid Input)** | indicates invalid or missing properties in the request; the body will provide some details as text |
| **403 (Unauthorized API Key)** | indicates a [missing, unknown, or unauthorized API Key](/docs/proget/api/licenses#authentication); the body will be empty |