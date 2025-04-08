---
title: "Reject Build"
order: 8
---

The *Reject Build* is an endpoint in [BuildMaster's Release & Build Deployment API](/docs/buildmaster/reference/api/release-and-build) that will reject a build, with the parameters defined in the body of the request.

:::(Info) (🚀 Quick Example: Rejecting a build with Curl)
This example rejects a build with a `buildNumber` of `2`, `releaseNumber` of `1.0.1` and the `applicationName` `TaxCalculator`, authenticating with the API key `abc12345`:

````
curl -X POST -H "X-ApiKey: abc12345" -H "Content-Type: application/json" -d "{\"buildNumber\": \"2\", \"releaseNumber\": \"1.0.1\", \"applicationName\": \"TaxCalculator\"}" "https://proget.corp.local/api/releases/builds/reject"
````
:::

## Request Specification
```
POST/DELETE /api/releases/builds/reject
```
To reject a build, simply `POST` or `DELETE` to the URL with an [appropriate API Key](/docs/buildmaster/reference/api/release-and-build#authentication) and a request body, containing the following parameters:

| Input | Parameter Specification |
| --- | --- |
| Build | Required. Either a key named `buildId` with an integer value, or a key named `buildNumber` with any value. |
| Release | Required if `buildNumber` is specified, otherwise must not be set. Either a key named `releaseId` with an integer value, or a key named `releaseNumber` with any value. |
| Application | Required if `releaseNumber` is specified, otherwise must not be set. Either a key named `applicationId` with an integer value or a key named `applicationName` with any value. |

**Rejecting a build** requires parameters defined in the request body.

```json
DELETE /api/releases/builds/reject

{
  "buildNumber": "2",
  "releaseNumber": "1.0.1",
  "applicationName": "TaxCalculator"
}
```

## Response Specification

| Response | Details |
|---|---|
| **200 (Success)** | the specified build was rejected |
| **400 (Invalid Input)** | indicates invalid or missing properties in the request; the body will provide some details as text |
| **403 (Unauthorized API Key)** | indicates a [missing, unknown, or unauthorized API Key](/docs/proget/api/licenses#authentication); the body will be empty |