---
title: "Delete Universal Package"
order: 4
---

*Delete Universal Package* is an endpoint in [ProGet's Universal Feed API](/docs/proget/reference-api/universal-feed) that deletes a specified universal package (if `group-name` is omitted, then the empty group is used).

:::(Info) (🚀 Quick Example: Deleting a Universal Package with Curl)
This example deletes version `1.2.3` of a universal package `myUniversalPackage`, in the group `MyGroup`, from the feed `myUniversalFeed`, authenticating with the API key `abc12345`:

````
curl -X DELETE --header "X-ApiKey: abc12345" "https://proget.corp.local/upack/myUniversalFeed/delete/MyGroup/MyUniversalPackage/1.2.3"
````
:::

## Request Specification
To delete a universal package, simply `DELETE` to the URL with a feed name, group name, package name, and package version.

```
DELETE /upack/«feed-name»/delete/«group-name»/«package-name»/«package-version»
```

**Deleting a universal package** requires the feed (e.g. `myUniversalFeed`),  group name (e.g. `myGroup`), package name (e.g. `myUniversalPackage`), and version (e.g. `1.0.0`):
```
DELETE /upack/myUniversalFeed/delete/myGroup/myUniversalPackage/1.0.0
```

## Response Specification

| Response | Details |
| --- | --- |
| **200 (Success)** | will successfully delete the package
| **400 (Invalid Input)** | indicates invalid or missing properties on the package; the body will provide some details as text |
|  **403 (Unauthorized API Key)** | indicates a [missing, unknown, or unauthorized API Key](/docs/proget/reference-api/universal-feed#authentication) |
| **404 (Package Not Found)** | indicates the specified package does not exist |


### Delete all package versions (PowerShell)
This script will delete all versions of the package `BaseModules` from the universal feed `universal-feed`:

```powershell
$baseUrl = "https://proget.corp.local"
$apiKey = "a1b2c3d4e5"
$universalFeed = "universal-feed"
$packageName = "BaseModules"

function Get-AuthHeader {
    param($apiKey)
    return @{ "X-ApiKey" = $apiKey }
}

function List-PackageVersions {
    param($baseUrl, $apiKey, $packageName)

    $url = "$baseUrl/upack/$universalFeed/packages" + "?name=$packageName"
    $authHeader = Get-AuthHeader -apiKey $apiKey
    $response = Invoke-RestMethod -Uri $url -Headers $authHeader -Method Get
    $group = $response.group
    $versions = $response.versions
    return $group, $versions
}

function Delete-PackageVersion {
    param($baseUrl, $apiKey, $group, $packageName, $version)
    $url = "$baseUrl/upack/universal-feed/delete/$group/$packageName/$version"
    $authHeader = Get-AuthHeader -apiKey $apiKey

    Invoke-RestMethod -Uri $url -Headers $authHeader -Method Delete
}

$group, $versions = List-PackageVersions -baseUrl $baseUrl -apiKey $apiKey -packageName $packageName

foreach ($version in $versions) {
    Delete-PackageVersion -baseUrl $baseUrl -apiKey $apiKey -group $group -packageName $packageName -version $version
    Write-Host "Deleted version $version of $packageName"
}
```