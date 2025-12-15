---
title: "Delete Package"
order: 5
---

*Delete Package* is available as both a `pgutil` command and an HTTP Request, and will delete a single package from the specified feed. 

:::(Info) (🚀 Quick Example: Deleting a NuGet package with pgutil)

```bash
pgutil packages delete --feed=myNugetFeed --package=myNugetPackage --version=1.0.0
```
:::

This API requires ProGet 2023.0 or later.

## Command Specification (CLI)
The `packages delete` command is used to delete a package from a feed.

The `--package` and `--version` options are always required. The `--feed` option is required if a default feed is not configured.

Additional options may be required depending on the package type. If a required option is missing, an error will be returned indicating the missing option.

**Deleting an npm Package** requires the scope (e.g. `myscope`), name (e.g. `@myscope/my-npm-package`) and version (e.g. `2.0.0`):

```bash
pgutil packages delete --feed=MyNpmFeed --package=@my-scope/my-npm-package --version=2.0.0
```

**Deleting a Pypi Package** requires the package (e.g. `myPypiPackage`), version (e.g. `7.8.9`) and filename (e.g. `myPypiPackage-7.8.9.tar.gz`):

```bash
pgutil packages delete --feed=myPypiFeed --package=myPypiPackage --version=7.8.9 --filename=myPypiPackage-7.8.9.tar.gz
```

**Deleting a Debian Package** requires the package (e.g. `myDebianPackage`), version (e.g. `2.3.4`), component (e.g. `main`), distribution (e.g. `stable`) and architecture (e.g. `amd64`):

```bash
pgutil packages delete --feed=myDebianFeed --package=myDebianPackage --version=2.3.4 --component=main --distro=stable --arch=amd64
```

Note source options must also be specified unless you have the "Default" source configured, and that a feed may be instead specified in the source. See [Working with Sources](/docs/proget/api/pgutil#sources) to learn more.

## HTTP Request Specification

To delete a package, simply `POST` to the URL with a feed name, [package identifiers](/docs/proget/api/packages#using-multiple-parameters), and an [appropriate API Key](/docs/proget/api/packages#authentication).

```plaintext
POST /api/packages/«feed-name»/delete?«package-identifiers»
```

Unless you use a `purl`, the parameters required will vary by feedtype. 

## HTTP Response Specification

A successful `200` response indicates that the package was deleted. A `403` response indicates a [missing, unknown, or unauthorized API Key](/docs/proget/api/packages#authentication).

## Sample Usage Scripts

### Delete All Packages Except for Latest Version (Powershell)
The following script will delete all non-latest versions of `GeneralUtils.NET` in the `private-nuget` feed.

```powershell
$baseUrl = "https://proget.corp.local/api/packages"
$feedName = "private-nuget"
$packageName = "GeneralUtils.NET"
$apiKey = "a1b2c3d4e5"

$allVersionsUrl = "$baseUrl/$feedName/versions?name=$packageName"
$allVersions = Invoke-RestMethod -Uri $allVersionsUrl -Headers @{"X-ApiKey" = $apiKey}

$latestVersionUrl = "$baseUrl/$feedName/latest?name=$packageName"
$latestVersion = Invoke-RestMethod -Uri $latestVersionUrl -Headers @{"X-ApiKey" = $apiKey}

foreach ($version in $allVersions) 
    {
        if ($version.Version -ne $latestVersion.Version) 
            {
                $deleteVersionUrl = "$baseUrl/$feedName/delete?name=$packageName&version=$($version.Version)"
            Invoke-RestMethod -Uri $deleteVersionUrl -Headers @{"X-ApiKey" = $apiKey} -Method POST
            Write-Host "Deleted version $($version.Version)"
        }
    }

Write-Host "All old versions of $packageName have been deleted, except for the latest version $($latestVersion.Version)."
````
This returns:
````powershell
Deleted version 3.0.3-beta1
Deleted version 2.0.2
Deleted version 2.0.2-beta3
All old versions of GeneralUtils.NET have been deleted, except for the latest version 3.0.3.
```

### Delete All Pre-release (Alpha or Beta) Versions of a Package (Python)
The following script will delete all "alpha" and "beta" versions of the `GeneralUtils.NET` package in the `private-nuget`, leaving only release versions in the feed.

```python
import requests

packageName = "GeneralUtils.NET"
baseUrl = "https://proget.corp.local/api/packages/private-nuget"
apiKey = "a1b2c3d4e5"

package_info_url = f"{baseUrl}/versions?name={packageName}"

headers = {"X-ApiKey": apiKey}

response = requests.get(package_info_url, headers=headers)

if response.status_code == 200:
    package_info = response.json()

    for version in package_info:
        package_name = version["Name"]
        package_version = version["Version"]

        if "beta" in package_version or "alpha" in package_version:
            delete_url = f"{baseUrl}/delete?name={package_name}&version={package_version}"
            response = requests.post(delete_url, headers=headers)

            if response.status_code == 200:
                print(f"{package_name} version {package_version} deleted.")
            else:
                print(f"Failed to delete {package_name} version {package_version}.")

    print(f"All 'beta' or 'alpha' versions of {packageName} have been deleted.")
else:
    print(f"Failed to retrieve package information for {packageName}. Status code: {response.status_code}")
```

Running this script will output something like this:

```python
GeneralUtils.NET version 13.0.3-beta1 deleted.
GeneralUtils.NET version 13.0.2-beta3 deleted.
All 'beta' or 'alpha' versions of GeneralUtils.NET have been deleted.
```
