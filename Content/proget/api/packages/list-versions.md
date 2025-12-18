---
title: "List Package Versions"
order: 2
---

*List Package Versions* is is available as both a `pgutil` command and an HTTP Request and will return a list of objects describing the versions of packages in a feed, optionally filtered by group, name, and version.

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

Note source options must also be specified unless you have the "Default" source configured, and that a feed may be instead specified in the source. See [Working with Sources](/docs/proget/api/pgutil#sources) to learn more.

## HTTP Request Specification
To list a package version, simply `GET` to the URL with a feed name, [package identifiers](/docs/proget/api/packages#using-multiple-parameters), and an [appropriate API Key](/docs/proget/api/packages#authentication).

```plaintext
GET /api/packages/«feed-name»/versions?«package-identifiers»
```

You can also return multiple `PackageVersionInfo` (see [PackageVersionInfo.cs](https://github.com/Inedo/pgutil/blob/thousand/Inedo.ProGet/PackageVersion.cs)) objects by just querying the package name:

```json
GET /api/packages/MyNuGetFeed/versions?name=myNugetPackage
```

## HTTP Response Specification
A successful (`200`) response body will contain an array of 1 `PackageVersionInfo` object. A `403` response indicates a [missing, unknown, or unauthorized API Key](/docs/proget/api/security#authentication).

Note that, if an API call is made on a package that does not exist, an empty object is returned. In addition,  Package hash values may not be present for all packages because earlier versions of ProGet would typically only generate some of the hash types.

The response will always be an array, even if only one version is requested. For feed types that can have multiple files associated with a version, the array may contain more than one item (such as PyPI, Conda, Ruby, etc).

## Sample Usage Scripts

### List all non-prerelease versions (Powershell)
This script will print out all stable (non prerelease) versions of the `GeneralUtils.NET` package in the `private-nuget` feed.

```powershell
$apiKey = "a1b2c3d4e5"
$feedName = "private-nuget"
$packageName = "GeneralUtils.NET"
$apiEndpoint = "https://proget.corp.local/api/packages/$feedName/versions"

$queryString = "name=$packageName"

$response = Invoke-RestMethod -Uri "${apiEndpoint}?${queryString}" -Headers @{ "X-ApiKey" = $apiKey }

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
headers = {"X-ApiKey": api_key}

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