---
title: "List Packages"
order: 1
---

*List Packages* is available as both a `pgutil` command and an HTTP Request, and will return a JSON array of [PackageVersionInfo](/docs/proget/api/packages#package-version) objects describing the packages in a feed that match the specified query arguments.

:::(Info) (🚀 Quick Example: Listing packages with pgutil)
This example will list the packages in the feed `myNugetFeed`

```bash
pgutil packages list --feed=myNugetFeed
```
:::

## Command Specification (CLI)
The `packages list` command is used to list the packages in a feed. 

The `--feed` option is required if there is no default feed configured. The `--stable` parameter is optional, but when set true, latest stable versions of packages are returned instead of absolute latest versions. 

**Listing packages in a feed** requires the `--feed` option (e.g. `myNugetFeed`) if there is no default feed configured:

```bash
pgutil packages list  --feed=myNugetFeed
```

The `--package` option is optional, and can be used in the event that the user wishes to list the latest version of a single package in a feed. 

**Listing the latest stable npm package version** requires the package name (e.g. `myNpmPackage`), the scope (e.g. `@myScope`) and setting `--stable` as `true`:

```bash
pgutil packages list --package=@myScope/myNpmPackage --feed=myNpmFeed --stable=true
```

## HTTP Request Specification
To list packages, simply `GET` to the URL with a feed name, and `stableOnly` parameters, and an [appropriate API Key](/docs/proget/api/packages#authentication).

```plaintext
GET /api/packages/«feed-name»/latest[?group=«group»][&name=«name»][&stableOnly=«true/false»]
```

Note that all parameters are optional. The `group name`and `package name` parameters can be used to filter the results. The `stableOnly` parameter defaults to `false`, but when set true, latest stable versions of packages are returned instead of absolute latest versions

## HTTP Response Specification
A successful (`200`) response body will contain an array of [PackageVersionInfo](/docs/proget/api/packages#package-version) objects. For example, to querying the latest stable packages in a feed, the request would return this:

```json
GET /api/packages/MyNuGetFeed/latest&stableOnly=true
[
    {
        "purl": "pkg:nuget/myNugetPackage@1.2.3",
        "name": "myNugetPackage",
        "version": "1.2.3",
        "totalDownloads": 0,
        "downloads": 0,
        "published": "2024-05-28T09:14:31.313Z",
        "publishedBy": "j.smith",
        "size": 2596051,
        "md5": "a80c7fb0a5437ce4fa16a664bcc95c60",
        "sha1": "50d8ccef2efbc9b5bfc38f7ae41c989de2011b55",
        "sha256": "3d21caf909f9db2b5d13249d6728c2506c55e72e2123fbe2af65a056c0a0bf9d",
        "sha512": "6934665f0479c58bbe996c44f2bf16d435a72f4d92795f0bc1d40cb0b234jh3jc...",
    },
    { ... } // remaining packages
]
```

Note that, if an API call is made on a package that does not exist, an empty object is returned. In addition,  Package hash values may not be present for all packages because earlier versions of ProGet would typically only generate some of the hash types.

| Response | Details |
| --- | --- |
| **200 (Success)** | body will contain an array of [PackageVersionInfo](/docs/proget/api/packages#package-version) objects
|  **403 (Unauthorized API Key)** | indicates a [missing, unknown, or unauthorized API Key](/docs/proget/api/packages#authentication); the body will be empty
| **500 (Server Error)** | indicates an unexpected error; the body will contain the messsage and stack trace, and this will also be logged

## Sample Usage Scripts

### Latest npm packages in scope (Powershell)
This script will print out the name and latest version number of all stable (non prerelease) packages in the `@mygroup` scope in the `private-npm` feed.

```powershell
$feedName = "private-npm"
$apiUrl = "https://proget.corp.local/api/packages/$feedName/latest"
$apiKey = "a1b2c3d4e5"

$queryParams = @{
    group = "@mygroup"
    stableOnly = "true"
}

$headers = @{
    "X-API-Key" = $apiKey
}

$response = Invoke-RestMethod -Uri $apiUrl -Headers $headers -Method Get -Body $queryParams

foreach ($package in $response) {
    Write-Host "Package Name: $($package.name)"
    Write-Host "Latest Version: $($package.version)"
    Write-Host "--------------------"
}
```

Running this script will output something like this:

```
Package Name: core
Latest Version: 16.2.6
--------------------
Package Name: material
Latest Version: 16.2.9
--------------------
Package Name: cdk
Latest Version: 16.2.9
--------------------
```

### Check Latest version (Python)
This function checks to see if `4.2.1` is the latest version of the `GeneralUtils.NET` package in the `private-nuget` feed, and returns a message depending on the result.

```python
import requests

base_url = "https://proget.corp.local.org"
feed_name = "MyNuGetFeed"
package_name = "MyPackage"
desired_version = "13.0.3"
stable_only = True 
api_key = "a1b2c3d4e5"

api_url = f"{base_url}/api/packages/{feed_name}/latest?name={package_name}&stableOnly={stable_only}"

def is_latest_version(desired_version, latest_version):
    return latest_version == desired_version

response = requests.get(api_url, headers={"Authorization": f"Bearer {api_key}"})

if response.status_code == 200:
    data = response.json()

    if data and len(data) > 0:
        latest_version = data[0]["version"]

        is_latest = is_latest_version(desired_version, latest_version)

        if is_latest:
            print(f"{package_name} {desired_version} is the latest version.")
        else:
            print(f"{package_name} {desired_version} is NOT the latest version.")
    else:
        print("Failed to retrieve package information.")
else:
    print(f"Failed to make the API request. Status code: {response.status_code}")
```

Running this script will output something like this:

```python
GeneralUtils.NET 4.2.1 is the latest version.
```
