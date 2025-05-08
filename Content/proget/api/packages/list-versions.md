---
title: "List Package Versions"
order: 2
---

*List Package Versions* is is available as both a `pgutil` command and an HTTP Request and will return a JSON array of [PackageVersionInfo](/docs/proget/api/packages#package-version) objects describing the versions of packages in a feed, optionally filtered by group, name, and version.

:::(Info) (🚀 Quick Example: Listing package versions with pgutil)
This example will list versions of the package `myNugetPackage` in the feed `myNugetFeed`

```bash
pgutil packages versions --package=myNugetPackage --feed=myNugetFeed
```
:::

## Command Specification (CLI)
The `packages versions` command is used to list versions of the specified package in the feed. 

The `--package` option is always required, and the `--feed` option is required if there is no default feed configured.  The `--version` option is optional and will filter the package by version.

**Listing all NuGet packages** requires the package name (e.g. `myNugetPackage`):

```bash
pgutil packages versions --package=myNugetPackage --feed=myNugetFeed
```

**Listing a single NuGet package version** requires the name (e.g. `myNugetPackage`) and version (e.g. `1.0.0`):

```bash
pgutil packages versions --package=myNugetPackage --version=1.0.0 --feed=myNugetFeed
```
**Listing multiple versions of an NPM package** requires the scope (e.g. `myScope`) and name (e.g. `myNpmPackage`):

```bash
pgutil packages versions --package=@myScope/myNpmPackage --feed=myNpmFeed
```

**Listing a single NPM package version** requires the scope (e.g. `myScope`), name (e.g. `myNpmPackage`) and version (e.g. `2.0.0`):

```bash
pgutil packages versions --package=@myScope/myNpmPackage --version=2.0.0 --feed=myNpmFeed
```

## HTTP Request Specification
To list a package version, simply `GET` to the URL with a feed name, [package identifiers](/docs/proget/api/packages#using-multiple-parameters), and an [appropriate API Key](/docs/proget/api/packages#authentication).

```plaintext
GET /api/packages/«feed-name»/versions?«package-identifiers»
```

## HTTP Response Specification
A successful (`200`) response body will contain an array of [PackageVersionInfo](/docs/proget/api/packages#package-version) objects. For example, querying version `1.0.0` of `myNuGetPackage`, the request would return a single object:

```json
GET /api/packages/MyNuGetFeed/versions?name=myNugetPackage&version=1.0.0

[{
    "purl":"pkg:nuget/myNugetPackage@1.0.0",
    "group":null,
    "name":"MyNugetPackage",
    "version":"1.0.0",
    "totalDownloads":0,
    "downloads":0,
    "published":"2023-10-17T02:43:00.717Z",
    "publishedBy":"Anonymous",
    "size":2441966,
    "md5":"94d7f4cce0663c6a30340fe6fec831da",
    "sha1":"f418efd4238eb069cf38d1c86f4edc34444777dd",
    "sha256":"872fc189e638ab1056555b03aaa38f68bcb54286e221aa646eb1129babf63c77",
    "sha512":"99b252bc77d1c5f5f7b51fd4ea7d5653e9961d7b3061cf9207f8643a9c
    7cc9965eebc84d6467f2989bb4723b1a244915cc232a78f894e8b748ca882a7c89fb92"
 }]
```

You can also return multiple [PackageVersion](/docs/proget/api/packages#package-version) objects versions by just querying the package name:

```json
GET /api/packages/MyNuGetFeed/versions?name=myNugetPackage

[{
    "purl":"pkg:nuget/myNugetPackage@3.0.1",
    "group":null,
    "name":"myNugetPackage",
    "version":"3.0.1",
    "totalDownloads":0,
    "downloads":0,
    "published":"2023-10-17T02:43:00.717Z",
    "publishedBy":"Anonymous",
    "size":2441966,
    "md5":"94d7f4cce0663c6a30340fe6fec831da",
    "sha1":"f418efd4238eb069cf38d1c86f4edc34444777dd",
    "sha256":"872fc189e638ab1056555b03aaa38f68bcb54286e221aa646eb1129babf63c77",
    "sha512":"99b252bc77d1c5f5f7b51fd4ea7d5653e9961d7b3061cf9207f8643a9c
    7cc9965eebc84d6467f2989bb4723b1a244915cc232a78f894e8b748ca882a7c89fb92"
 },
 {
    "purl":"pkg:nuget/myNugetPackage@2.0.3",
    "group":null,
    "name":"myNugetPackage",
    "version":"2.0.3",
    "totalDownloads":0,
    "downloads":0,
    "published":"2023-10-17T02:43:07.993Z",
    "publishedBy":"Anonymous",
    "size":2442133,
    "md5":"df5d702d4c4e174965a2c1cdf405e69b",
    "sha1":"6a6fc524ede4d2ba3005f31b1c76191fd8cbad06",
    "sha256":"e4e8e86fd0a12df300e57c6cc2c1643b378cd91b254507eb092630d87257e218",
    "sha512":"3102da9daa37f47aa9ebb8757ca687a193a8cfd34b203c0f09978469502
    88c919885d1dd8986aa5e26ae92c820df8138cdd27421261fb90846caad68b98e100f"
 },
 ...
]
```

Note that, if an API call is made on a package that does not exist, an empty object is returned. In addition,  Package hash values may not be present for all packages because earlier versions of ProGet would typically only generate some of the hash types.

The response will always be an array, even if only one version is requested. For feed types that can have multiple files associated with a version, the array may contain more than one item (such as PyPI, Conda, Ruby, etc).

| Response | Details |
| --- | --- |
| **200 (Success)** | body will contain an array of [PackageVersionInfo](/docs/proget/api/packages#package-version) objects
|  **403 (Unauthorised API Key)** | indicates a [missing, unknown, or unauthorized API Key](/docs/proget/api/packages#authentication); the body will be empty
| **500 (Server Error)** | indicates an unexpected error; the body will contain the messsage and stack trace, and this will also be logged

## Sample Usage Scripts

### List all non-prerelease versions (Powershell)
This script will print out all stable (non prerelease) versions of the `GeneralUtils.NET` package in the `private-nuget` feed.

```powershell
$apiKey = "a1b2c3d4e5"
$feedName = "private-nuget"
$packageName = "GeneralUtils.NET"
$apiEndpoint = "https://proget.corp.local/api/packages/$feedName/versions"

$queryString = "name=$packageName"

$response = Invoke-RestMethod -Uri "${apiEndpoint}?${queryString}" -Headers @{ "X-Api-Key" = $apiKey }

$stableVersions = $response | Where-Object { $_.version -notmatch "[-.]\d*[A-Za-z]+" }
$stableVersions | ForEach-Object {
    Write-Host "Package: $($packageName), Version: $($_.version)"
}
```

Running this script will output something lke this:

```powershell
Package: GeneralUtils.NET, Version: 3.0.3
Package: GeneralUtils.NET, Version: 3.0.2
Package: GeneralUtils.NET, Version: 3.0.1
Package: GeneralUtils.NET, Version: 2.0.3
Package: GeneralUtils.NET, Version: 2.0.2
Package: GeneralUtils.NET, Version: 2.0.1
```

### List all versions (Python)
This script will print out all versions of the `GeneralUtils.NET` package in the `private-nuget` feed published by "John Smith" (username: `jsmith`).

```python
import requests
from datetime import datetime

api_key = "a1b2c3d4e5"
feed_name = "private-nuget"
package_name = "GeneralUtils.NET"
publisher = "jsmith"
base_url = "https://proget.corp.local.org"

api_url = f"{base_url}/api/packages/{feed_name}/versions?name={package_name}"
headers = {
    "Authorization": f"Bearer {api_key}"
}

response = requests.get(api_url, headers=headers)
response_data = response.json()

versions_published_by = [
    item for item in response_data if item["name"] == package_name and item["publishedBy"] == publisher
]

if not versions_published_by:
    print(f"No versions of {package_name} published by {publisher} found in the {feed_name} feed.")
else:
    for version_info in versions_published_by:
        formatted_date = datetime.strptime(version_info["published"], "%Y-%m-%dT%H:%M:%S.%fZ").strftime("%m/%d/%Y at %I:%M %p")
        print(f"----\nVersion: {version_info['version']}\nPublished: {formatted_date}")
```

Running this script will output something like this:

```python
Versions of GeneralUtils.NET published by jsmith found in the private-nuget feed:
----
Version: 13.0.3
Published: 10/19/2023 at 03:12 PM
----
Version: 13.0.2
Published: 10/19/2023 at 11:47 AM
----
Version: 13.0.1
Published: 10/19/2023 at 11:47 AM
----
Version: 12.0.3-beta2
Published: 10/19/2023 at 03:12 PM
----
```