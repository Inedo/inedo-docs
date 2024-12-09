---
title: "Download Virtual Package"
order: 6
---

*Download Virtual Package* is available as both a `pgutil` command and an HTTP Request, and will download the `package.version.vpack` file for a virtual package, or throw error if not a virtual package.

:::(Info) (🚀 Quick Example: Downloading a Virtual Package with Curl)
This example downloads version `1.2.3` of a universal package `myUniversalPackage`, in the group `MyGroup`, from the feed `myUniversalFeed`, to `C:\ProGet\MyPackage-1.2.3.vpack`, 

````
curl -X GET --header "X-ApiKey: abc12345" "https://proget.corp.local/upack/myUniversalFeed/download-vpack/MyGroup/myUniversalPackage/1.2.3" -o "C:\ProGet\MyPackage-1.2.3.vpack"
````
:::

### Parameter	Description
| Parameter | Details |
| --- | --- |
| `group-name` | Optional. If not specified, the empty group will be searched. |
| `package-name` | Required. |
| `package-version` | Optional. |

## HTTP Request Specification
To download a **specific** virtual package version, simply `GET` to the URL with a feed name, group name, package name and package version:

```
GET /upack/«feed-name»/download-vpack/«group-name»/«package-name»/«package-version»
```

To download the **latest** version of a virtual package, simply `GET` to the URL with a feed name, group name and package name:

```
GET /upack/«feed-name»/download-vpack/«group-name»/«package-name»?latest
```

**Downloading a specific version of a virtual package** requires the feed (e.g. `myUniversalFeed`),  group name (e.g. `myGroup`), package name (e.g. `myVirtualPackage`) and version (e.g. `1.2.3`):

```
GET /upack/myUniversalFeed/download-vpack/myGroup/myVirtualPackage/1.2.3
```

**Downloading the latest version of a virtual package** requires the feed (e.g. `myUniversalFeed`),  group name (e.g. `myGroup`), and package name (e.g. `myVirtualPackage`):

```
GET /upack/myUniversalFeed/download-vpack/myGroup/myVirtualPackage?latest
```
## Command Specification (CLI)
:::(Info) (🚧 Coming Soon 🚧)
The `universal vpackage download` command is coming soon. Similar to the HTTP Request, it will download the `package.version.vpack` file for a virtual package.
:::

## HTTP Response Specification

| Response | Details |
| --- | --- |
| **200 (Success)** | will successfully download the virtual package
| **400 (Virtual Package Version not Found)** | returned if `package-version` is not specified, and latest is not specified in the URL |
|  **403 (Unauthorized API Key)** | indicates a [missing, unknown, or unauthorized API Key](/docs/proget/reference-api/universal-feed#authentication) |
| **404 (Virtual Package Not Found)** | indicates the specified virtual package does not exist |

## Sample Usage Scripts

### Download all versions of a virtual package (Powershell)
This script will download all versions of the virtual package `BaseModules.Combined` from the universal feed `universal-feed` to the local folder `C:\ProGet\BaseModule Packages`

```powershell
$baseUrl = "https://proget.corp.local"
$apiKey = "a1b2c3d4e5"
$feedName = "universal-feed"
$packageName = "BaseModules.Combined"
$outputFolder = "C:\ProGet\BaseModule Packages"

$listPackagesEndpoint = "$baseUrl/upack/$feedName/versions" + "?name=$packageName"
$downloadPackagesEndpoint = "$baseUrl/upack/$feedName/download-vpack"

$headers = @{"X-ApiKey" = $apiKey}
$listResponse = Invoke-RestMethod -Uri $listPackagesEndpoint -Headers $headers -Method Get

foreach ($package in $listResponse) {
    if ($package.isVirtual -eq $true) {
        $packageVersion = $package.version
        $packageGroup = $package.group

        $downloadUrl = "$downloadPackagesEndpoint/$packageGroup/$packageName/$packageVersion" + "?contentOnly=zip"
        $outputFilePath = Join-Path $outputFolder "$packageName-$packageVersion.vpack"

        Invoke-WebRequest -Uri $downloadUrl -Headers $headers -OutFile $outputFilePath
        Write-Host "Downloaded: $outputFilePath"
    }
}
```

### Download all virtual package (Powershell)
This script will download all virtual packages from the universal feed `universal-feed` to the local folder `C:\ProGet\Virtual Packages`

```powershell
$baseUrl = "https://proget.corp.local"
$apiKey = "a1b2c3d4e5"
$feedName = "universal-feed"
$outputFolder = "C:\ProGet\Virtual Packages"

$listPackagesEndpoint = "$baseUrl/upack/$feedName/versions"
$downloadPackagesEndpoint = "$baseUrl/upack/$feedName/download-vpack"

$headers = @{"X-ApiKey" = $apiKey}
$listResponse = Invoke-RestMethod -Uri $listPackagesEndpoint -Headers $headers -Method Get

foreach ($package in $listResponse) {
    if ($package.isVirtual -eq $true) {
        $packageName = $package.name
        $packageVersion = $package.version
        $packageGroup = $package.group

        $downloadUrl = "$downloadPackagesEndpoint/$packageGroup/$packageName/$packageVersion" + "?contentOnly=zip"
        $outputFilePath = Join-Path $outputFolder "$packageName-$packageVersion.vpack"

        Invoke-WebRequest -Uri $downloadUrl -Headers $headers -OutFile $outputFilePath
        Write-Host "Downloaded: $outputFilePath"
    }
}
```