---
title: "Cancel Release"
order: 4
---

The *Cancel Release* is an endpoint in [BuildMaster's Release & Build Deployment API](/docs/buildmaster/reference/api/release-and-build) that will cancel a release, with the parameters defined in the body of the request.

:::(Info) (🚀 Quick Example: Canceling a release with Curl)
This example cancels a release with a `releaseNumber` of `1.0.1` and the `applicationName` `TaxCalculator`, authenticating with the API key `abc12345`:

````
curl -X POST -H "X-ApiKey: abc12345" -H "Content-Type: application/json" -d "{\"releaseNumber\": \"1.0.1\", \"applicationName\": \"TaxCalculator\"}" "https://proget.corp.local/api/releases/cancel"
````
:::

## Request Specification
```
POST/PUT /api/releases/cancel
```
To cancel a release, simply `POST` or `PUT` to the URL with an [appropriate API Key](/docs/buildmaster/reference/api/release-and-build#authentication) and a request body, containing the following parameters:

| Input | Parameter Specification |
| --- | --- |
| Release | Required. Either a key named `releaseId` with an integer value, or a key named `releaseNumber` with any value. |
| Application | Required if `releaseNumber` is specified, otherwise must not be set. Either a key named `applicationId` with an integer value, or a key named `applicationName` with any value. |
| Reason | Optional. A key named `reason` with any value. |

**Rejecting a build** requires parameters defined in the request body.

```json
POST /api/releases/cancel

{
  "releaseNumber": "1.0.1",
  "applicationName": "TaxCalculator"
}
```

## Response Specification

| Response | Details |
|---|---|
| **200 (Success)** | the specified release was cancelled |
| **400 (Invalid Input)** | indicates invalid or missing properties in the request; the body will provide some details as text |
| **403 (Unauthorized API Key)** | indicates a [missing, unknown, or unauthorized API Key](/docs/proget/reference-api/proget-api-licenses#authentication); the body will be empty |