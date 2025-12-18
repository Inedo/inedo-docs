---
title: "List Packages"
order: 1
---

*List Packages* is available as both a `pgutil` command and an HTTP Request, and will return a list describing the packages in a feed that match the specified query arguments.

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

Note source options must also be specified unless you have the "Default" source configured, and that a feed may be instead specified in the source. See [Working with Sources](/docs/proget/api/pgutil#sources) to learn more.

## HTTP Request Specification
To list packages, simply `GET` to the URL with a feed name, and `stableOnly` parameters, and an [appropriate API Key](/docs/proget/api/packages#authentication).

```plaintext
GET /api/packages/«feed-name»/latest[?group=«group»][&name=«name»][&stableOnly=«true/false»]
```

Note that all parameters are optional. The `group name`and `package name` parameters can be used to filter the results. The `stableOnly` parameter defaults to `false`, but when set true, latest stable versions of packages are returned instead of absolute latest versions

## HTTP Response Specification
A successful (`200`) response body will contain an array of `PackageVersionInfo` (see [PackageVersionInfo.cs](https://github.com/Inedo/pgutil/blob/thousand/Inedo.ProGet/PackageVersionInfo.cs)) objects. A `403` response indicates a [missing, unknown, or unauthorized API Key](/docs/proget/api/packages#authentication).

Note that, if an API call is made on a package that does not exist, an empty object is returned. In addition, Package hash values may not be present for all packages because earlier versions of ProGet would typically only generate some of the hash types.

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
    "X-ApiKey" = $apiKey
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
Package Name: MyPackage
Latest Version: 6.2.6
--------------------
Package Name: AnotherPackage
Latest Version: 6.2.9
--------------------
Package Name: NpmPackage
Latest Version: 16.2.9
--------------------
```

### Check Latest version (Python)
This function checks to see if `4.2.1` is the latest version of the `GeneralUtils.NET` package in the `private-nuget` feed, and returns a message depending on the result.

```python
import requests

base_url = "https://proget.corp.local.org"
feed_name = "private-nuget"
package_name = "GeneralUtils.NET"
desired_version = "4.2.1"
stable_only = True 
api_key = "a1b2c3d4e5"

api_url = f"{base_url}/api/packages/{feed_name}/latest?name={package_name}&stableOnly={stable_only}"

def is_latest_version(desired_version, latest_version):
    return latest_version == desired_version

response = requests.get(api_url, headers={"X-ApiKey": api_key})

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
