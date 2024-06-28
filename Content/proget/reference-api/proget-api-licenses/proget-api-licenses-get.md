---
title: "Get License"
order: 2
---

*Get License* is available as both a `pgutil` command and an HTTP Request, and will return a single [License](/docs/proget/reference-api/proget-api-licenses#license-object) object for the specified license.

:::(Info) (🚀 Quick Example: Returning a license with Curl)
This example returns a license with the ID `XYZ-1.0`, authenticating with the API key `abc12345`:

````
curl -X GET --header "X-ApiKey: abc12345" "https://proget.corp.local/api/management/licenses/get/XYZ-1.0"
````
:::

## Command Specification (CLI)
:::(Info) (🚧 Coming Soon 🚧)
The `license get` command is coming soon. Similar to the HTTP Request, it will display a single license
:::

## HTTP Request Specification
To return a specified license, simply `GET` to the URL with the ID of the `license` and an [appropriate API Key](/docs/proget/reference-api/proget-api-licenses#authentication).

```
GET /api/management/licenses/get/«license-id»
```

**Getting a NuGet license** requires the `license` ID (e.g. `XYZ-1.0`)
```
GET /api/management/licenses/get/XYZ-1.0
```

## HTTP Response Specification
A successful (`200`) response body will contain a single [License](/docs/proget/reference-api/proget-api-licenses#license-object) object. For example, to requesting a license with the ID `0BSD`, the request would return:

```
GET /api/management/licenses/get/0BSD

{
  "licenseId": "0BSD",
  "title": "BSD Zero Clause License",
  "urls": [
    "landley.net/toybox/license.html",
    "spdx.org/licenses/0BSD.html"
  ],
  "allowed": true,
  "allowedlicenses": ["OpenLicenses"],
  "blockedlicenses": []
}
```

| Response | Details |
|---|---|
| **200 (Success)** | body will contain a [License](/docs/proget/reference-api/proget-api-licenses#license-object) object |
| **400 (Invalid Input)** | indicates invalid or missing properties in the request; the body will provide some details as text |
|  **403 (Unauthorized API Key)** | indicates a [missing, unknown, or unauthorized API Key](/docs/proget/reference-api/proget-api-licenses#authentication); the body will be empty |
| **404 (License Not Found)** | indicates that the specified `license` does not exist |
