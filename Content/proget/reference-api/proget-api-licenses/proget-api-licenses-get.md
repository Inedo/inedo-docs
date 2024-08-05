---
title: "Get License"
order: 2
---

*Get License* is available as both a `pgutil` command and an HTTP Request, and will return information or a single [License](/docs/proget/reference-api/proget-api-licenses#license-object) object for the specified license.

:::(Info) (🚀 Quick Example: Getting a license with pgutil)
This example returns information on a license with the ID `ABC-1.0`:

````
pgutil licenses info --code=ABC-1.0
````
:::

## Command Specification (CLI)
The `licenses info` command is used to get information on a license.

The `--code` options is always required.

**Getting a license** requires the license code (e.g. `MIT`):
```
pgutil licenses info --code=MIT

```
Example Output:

```
Code: MIT
Title: MIT License

Detection:
 SPDX: MIT
 Url:
  - go.microsoft.com/fwlink/?linkid=865381
  - go.microsoft.com/fwlink/?linkid=868514
  - licenses.nuget.org/MIT
  - opensource.org/license/mit/
  - raw.githubusercontent.com/Microsoft/dotnet/master/LICENSE
  - spdx.org/licenses/MIT.html
  - www.opensource.org/licenses/MIT

Files:
 - 0630520a7440edc1e05c3f318eba0df31920d5b4ff0848ed10d7922b34eed796
```

## HTTP Request Specification
To return a specified license, simply `GET` to the URL with the ID of the `license` and an [appropriate API Key](/docs/proget/reference-api/proget-api-licenses#authentication).

```
GET /api/management/licenses/get/«license-id»
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
