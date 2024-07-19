---
title: "List Licenses"
order: 3
---

*List Licenses* is available as both a `pgutil` command and an HTTP Request, and will return an array of [License](/docs/proget/reference-api/proget-api-licenses#license-object) objects, describing all existing licenses.

:::(Info) (🚀 Quick Example: Listing all licenses with pgutil)
This example will list all existing licences:
```
pgutil licenses list
```
:::

## Command Specification (CLI)
The `licenses list` command is used to list all existing licenses.

**Listing all licenses** does not require any options beyond the basic command:
```
pgutil licenses list
```

## HTTP Request Specification
To list all existing licenses, simply `GET` to the URL with an [appropriate API Key](/docs/proget/reference-api/proget-api-licenses#authentication).

```
GET /api/management/licenses/list
```

## HTTP Response Specification
A successful (`200`) response body will contain an array of [License](/docs/proget/reference-api/proget-api-licenses#license-object) objects. For example:

```
GET /api/management/licenses/list

[
  {
    "licenseId": "0BSD",
    "title": "BSD Zero Clause License",
    "urls": [
      "landley.net/toybox/license.html",
      "spdx.org/licenses/0BSD.html"
    ],
    "allowed": true,
    "allowedLicenses": ["OpenLicenses"],
    "blockedLicenses": []
  },
  {
    "licenseId": "AAL",
    "title": "Attribution Assurance License",
    "urls": [
      "spdx.org/licenses/AAL.html",
      "www.opensource.org/licenses/attribution"
    ],
    "allowed": true,
    "allowedLicenses": [],
    "blockedLicenses": []
  },
  { ... } // other licenses
]
```

| Response | Details |
|---|---|
| **200 (Success)** | body will contain an array of [License](/docs/proget/reference-api/proget-api-licenses#license-object) objects |
|  **403 (Unauthorized API Key)** | indicates a [missing, unknown, or unauthorized API Key](/docs/proget/reference-api/proget-api-licenses#authentication); the body will be empty |

## Sample Usage Scripts

### List all licenses (Powershell)
This script will print a list of all existing licenses by their ID, name and URLS. 

```powershell
$apiUrl = "https://proget.corp.local/api/management/licenses/list"
$apiKey = "abc12345"

$headers = @{"X-ApiKey" = $apiKey}

$response = Invoke-RestMethod -Uri $apiUrl -Method Get -Headers $headers

foreach ($license in $response) {
    $licenseId = $license.licenseId
    $licenseTitle = $license.title
    $licenseUrls = $license.urls -join ', '  # Convert array to comma-separated string

    $formattedFeed = "{0}: {1} ({2})" -f $licenseId, $licenseTitle, $licenseUrls
    Write-Host $formattedFeed
}
```

#### Example Output:

```
Abstyles: Abstyles License (fedoraproject.org/wiki/Licensing/Abstyles, spdx.org/licenses/Abstyles.html)
Adobe-2006: Adobe Systems Incorporated Source Code License Agreement (fedoraproject.org/wiki/Licensing/AdobeLicense, spdx.org/licenses/Adobe-2006.html)
Adobe-Glyph: Adobe Glyph List License (fedoraproject.org/wiki/Licensing/MIT#AdobeGlyph, spdx.org/licenses/Adobe-Glyph.html)
AFL-1.1: Academic Free License v1.1 (opensource.linux-mirror.org/licenses/afl-1.1.txt, spdx.org/licenses/AFL-1.1.html)
...
...
```
