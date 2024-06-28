---
title: "Download Universal Package"
order: 5
---

The *Download Universal Package* is an endpoint in [ProGet's Universal Feed API](/docs/proget/upack/proget-api-universalfeed) that will return the contents of a package as either a `zip` or `tgz` archive.

:::(Info) (🚀 Quick Example: Downloading a Universal Package with Curl)
This example downloads version `1.2.3` of a universal package `myUniversalPackage`, in the group `MyGroup`, from the feed `myUniversalFeed`, to `C:\ProGet\MyPackage-1.2.3.upack`, authenticating with the API key `abc12345`:

````
curl -X GET --header "X-ApiKey: abc12345" "https://proget.corp.local/upack/myUniversalFeed/download/MyGroup/myUniversalPackage/1.2.3" -o "C:\ProGet\MyPackage-1.2.3.upack" 
````
:::

### Parameter	Description
| Parameter | Details |
| --- | --- |
| `group-name` | Optional. If not specified, the empty group will be searched. |
| `package-name` | Required. |
| `package-version` | Optional. |
| `contentOnly` | Optional. If specified, the contents of `/package` directory are returned as either a `zip` archive or `tgz` archive. If not specified, the contents of the root of the package are returned (`/package` and `upack.json`)|

## Request Specification
To download a **specific** package version, simply `GET` to the URL with a feed name, group name, package name, package version and content type:

```
GET /upack/«feed-name»/download/«group-name»/«package-name»/«package-version»?contentOnly=«zip|tgz»
```

To download the **latest** package version, simply `GET` to the URL with a feed name, group name, package name and content type:

```
GET /upack/«feed-name»/download/«group-name»/«package-name»?contentOnly=«zip|tgz»&latest
```

**Downloading a specific universal package version** requires the feed (e.g. `myUniversalFeed`),  group name (e.g. `myGroup`), package name (e.g. `myUniversalPackage`), version (e.g. `1.2.3`) and the content type (e.g. `tgz` ):

```
GET /upack/myUniversalFeed/download/myGroup/myUniversalPackage/1.2.3?contentOnly=tgz
```

**Downloading the latest universal package version** requires the feed (e.g. `myUniversalFeed`),  group name (e.g. `myGroup`), package name (e.g. `myUniversalPackage`), and the content type (e.g. `tgz` ):

```
GET /upack/myNpmFeed/download/MyScope/myNpmPackage?contentOnly=tgz&latest
```

## Response Specification

| Response | Details |
| --- | --- |
| **200 (Success)** | will successfully download the package
| **400 (Package Version not Found)** | returned if `package-version` is not specified, and latest is not specified in the URL |
|  **403 (Unauthorized API Key)** | indicates a [missing, unknown, or unauthorized API Key](/docs/proget/upack/proget-api-universalfeed#authentication) |
| **404 (Package Not Found)** | indicates the specified package does not exist |

## Sample Usage Scripts

### Download all versions of a package (Powershell)
This script will download all versions of the package `BaseModules` from the universal feed `universal-feed` to the local folder `C:\ProGet\BaseModule Packages`

```powershell
$baseUrl = "https://proget.corp.local"
$apiKey = "a1b2c3d4e5"
$feedName = "universal-feed"
$packageName = "BaseModules"
$outputFolder = "C:\ProGet\BaseModule Packages"

$listPackagesEndpoint = "$baseUrl/upack/$feedName/versions" + "?name=$packageName"
$downloadPackagesEndpoint = "$baseUrl/upack/$feedName/download"

$headers = @{"X-ApiKey" = $apiKey}
$listResponse = Invoke-RestMethod -Uri $listPackagesEndpoint -Headers $headers -Method Get

foreach ($package in $listResponse) {
    if ($package.isVirtual -eq $false) {
        $packageVersion = $package.version
        $packageGroup = $package.group

        $downloadUrl = "$downloadPackagesEndpoint/$packageGroup/$packageName/$packageVersion" + "?contentOnly=zip"
        $outputFilePath = Join-Path $outputFolder "$packageName-$packageVersion.upack"

        Invoke-WebRequest -Uri $downloadUrl -Headers $headers -OutFile $outputFilePath
        Write-Host "Downloaded: $outputFilePath"
    }
}
```

### Download all universal packages (Powershell)
This script will download all packages from the universal feed `universal-feed` to the local folder `C:\ProGet\Universal Packages`

```powershell
$baseUrl = "https://proget.corp.local"
$apiKey = "a1b2c3d4e5"
$feedName = "universal-feed"
$outputFolder = "C:\ProGet\Universal Packages"

$listPackagesEndpoint = "$baseUrl/upack/$feedName/versions"
$downloadPackagesEndpoint = "$baseUrl/upack/$feedName/download"

$headers = @{"X-ApiKey" = $apiKey}
$listResponse = Invoke-RestMethod -Uri $listPackagesEndpoint -Headers $headers -Method Get

foreach ($package in $listResponse) {
    if ($package.isVirtual -eq $false) {
        $packageName = $package.name
        $packageVersion = $package.version
        $packageGroup = $package.group

        $downloadUrl = "$downloadPackagesEndpoint/$packageGroup/$packageName/$packageVersion" + "?contentOnly=zip"
        $outputFilePath = Join-Path $outputFolder "$packageName-$packageVersion.upack"

        Invoke-WebRequest -Uri $downloadUrl -Headers $headers -OutFile $outputFilePath
        Write-Host "Downloaded: $outputFilePath"
    }
}
```