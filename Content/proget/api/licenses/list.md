---
title: "List Licenses"
order: 3
---

*List Licenses* is available as both a `pgutil` command and an HTTP Request, and will return a list of all existing licenses.

:::(Info) (🚀 Quick Example: Listing all licenses with pgutil)
This example will list all existing licences:

```bash
pgutil licenses list
```
:::

## Command Specification (CLI)
The `licenses list` command is used to list all existing licenses.

**Listing all licenses** does not require any options beyond the basic command:

```bash
pgutil licenses list
```

Example Output:

```plaintext
0BSD: BSD Zero Clause License

AAL: Attribution Assurance License

ABC: ABC License

Abstyles: Abstyles License

AdaCore-doc: AdaCore Doc License

Adobe-2006: Adobe Systems Incorporated Source Code License Agreement

Adobe-Display-PostScript: Adobe Display PostScript License

Adobe-Glyph: Adobe Glyph List License

Adobe-Utopia: Adobe Utopia Font License

...

```

### Listing License Files
The `licenses files list` command is used to list all existing license files.

**Listing all license files** does not require any options beyond the basic command:

```bash
pgutil licenses files list
```

Example Output:

```plaintext
0BSD 0a93c5768997c4ab6aea20386831cd5c63b8a2dd8484a36d99341aa2aafaa6b1
AAL 7608408f4ffdbc1ea582fcf9c2932489b87fd2bab912147612110ab75dfe65cd
Abstyles 2c7874987a8cb6de0690e4b779d2bc12afa94274fe295d6b147a9dcb30368de2
AdaCore-doc d74075bcffa19dbb1144512552a28b38fc8ba53085b2973c94ff8010c50eab5a
Adobe-2006 07aed3e8f1c4fb27b1240b5a0eb173b4fd8840367d5875fde130445f72b59f5c
Adobe-Display-PostScript 1d7589c8f1adbc5713e928a263cff5c942b00861c94ecaa8c0be0fcdbc1b5c56
Adobe-Glyph 33220eb7a88f19f3f0b2991a9208c137b122c45bdc0139567cc29f162acdae27
Adobe-Utopia 1e55fcd0120578420962bb39ec22ebdcb8e879c14c04f7c08892fbd33f4c906a
...
```

## HTTP Request Specification
To list all existing licenses, simply `GET` to the URL with an [appropriate API Key](/docs/proget/api/licenses#authentication).

```plaintext
GET /api/licenses/list
```

## HTTP Response Specification
A successful (`200`) response body will contain an array of [LicenseInfo.cs](https://github.com/Inedo/pgutil/blob/thousand/Inedo.ProGet/LicenseInfo.cs) objects. A `403` response indicates a [missing, unknown, or unauthorized API Key](/docs/proget/api/licenses#authentication).

## Sample Usage Scripts

### List all licenses (PowerShell)
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
