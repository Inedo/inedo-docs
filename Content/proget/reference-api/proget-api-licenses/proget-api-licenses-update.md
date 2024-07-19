---
title: "Update License"
order: 4
---

:::(internal) (⚠ Not Working)
Not working as of ProGet version 2023.22
:::

*Update License* is available as both a `pgutil` command and an HTTP Request, and will update a specified license using the [License](/docs/proget/reference-api/proget-api-licenses#license-object) object properties defined in the request body. This endpoint supports partial updating by only updating the properties that are supplied in the request. 

:::(Info) (🚀 Quick Example: Updating a license with Curl)
This example updates a nuget license with the id `XYZ-1.0`, authenticating with the API key `abc12345`, adding "npm-approved" and "pypi-approved" as allowed licenses:

````
curl -X POST -H "Content-Type: application/json" -H "X-ApiKey: abc12345" -d "{\"allowedFeeds\": [\"npm-approved\", \"pypi-approved\"]}" "https://proget.corp.local/api/management/licenses/update/XYZ-1.0"
````
:::

## Command Specification (CLI)
:::(Info) (🚧 Coming Soon 🚧)
The `license update` command is coming soon. Similar to the HTTP Request, it will update a specified license. 
:::

## HTTP Request Specification
To update a license, simply `POST` to the URL with the `license` id, an [appropriate API Key](/docs/proget/reference-api/proget-api-licenses#authentication) and a [License](/docs/proget/reference-api/proget-api-licenses#license-object) object as the request body.

:::(info) (📄 Note)
When updating, any properties omitted will keep their existing values. Updating a property with an array value will overwrite the existing value. For example, if a license has `allowedFeeds: ["A", "B"]`, updating with `allowedFeeds: ["C"]` will remove allowedFeeds "A" and "B" from the license, keeping only "C". 

To append values, perform a [Get License](/docs/proget/reference-api/proget-api-licenses/proget-api-licenses-get) first, then append the existing values returned in the "Update License" request. For example, GET the values of allowedFeeds "A" and "B", and add them along with "C", supplying `allowedFeeds: ["A", "B", "C"]`in the update request.
:::

```
POST /api/management/licenses/update/«license-id»
```

**Updating a license by adding allowed feeds** requires the `license` id (e.g. `ABC-1.0`) and a [License](/docs/proget/reference-api/proget-api-licenses#license-object) object as the body.

```
POST /api/management/licenses/update/ABC-1.0

{
  {
  "allowedFeeds": [
    "npm-approved",
    "nuget-approved",
    "pypi-approved"
    ]
  }
}
```
## HTTP Response Specification
A successful (`200`) response body will contain an updated [License](/docs/proget/reference-api/proget-api-licenses#license-object) object. For example, when updating a license `MIT`, adding an additional `allowedFeed`, this returns:

```
{
  "licenseId": "MIT",
  "title": "MIT License",
  "urls": [
    "https://spdx.org/licenses/MIT.html"
  ],
  "allowed": true,
  "allowedFeeds": [
    "npm-approved",
    "nuget-approved",
    "pypi-approved"
  ],
  "blockedFeeds": [
    "restricted-feed",
    "nuget-unapproved"
  ]
}
```

| Response | Details |
|---|---|
| **200 (Success)** | response body contains an updated [License](/docs/proget/reference-api/proget-api-licenses#license-object) object|
| **400 (Invalid Input)** | indicates invalid or missing properties in the request; the body will provide some details as text |
| **403 (Unauthorized API Key)** | indicates a [missing, unknown, or unauthorized API Key](/docs/proget/reference-api/proget-api-licenses#authentication); the body will be empty |
| **404 (License Not Found)** | indicates that the specified `license` does not exist |
