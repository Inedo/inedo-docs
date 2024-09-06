---
title: "Set Package Status"
order: 6
---

*Set Package Status* is available as both a `pgutil` command and an HTTP Request, and will set the  unlisted, deprecated, or blocked status for a package. 

:::(Info) (🚀 Quick Example: Unlisting a NuGet package with pgutil)

```bash
pgutil packages status unlisted --feed=myNugetFeed --package=myNugetPackage --version=1.0.0
```
:::

This API requires ProGet 2023.18 or later.

## Command Specification (CLI)
The `packages status` command is used to change the status of a package on a ProGet feed. 

There are three commands available (`unlisted`, `blocked`, and `deprecated`), and each command has a `--state` option that can be specified to change the default state.

| Command | Valid States |  |
| --- | --- | --- |
| `unlisted` | `unlisted`, `listed` | state defaults to `unlisted` |
| `blocked` | `blocked`, `notset`, `allowed` | state defaults to `blocked` |
| `deprecated` | `deprecated` `clear` | state defaults to `deprecated`  |

The `deprecated` command also has a `--reason` option that can be used to provide additional deprecation information.

The `--package` and `--version` options are always required, and the `--feed` option is required if a default feed is not configured.

**Example: Re-Listing  a NuGet package** requires a feed (e.g. `MyNugetFeed`), package name (e.g. `myNugetPackage`),  version (e.g. `1.0.0`), and state (`listed`):

```bash
pgutil packages status unlisted --feed=myNugetFeed --package=myNugetPackage --version=1.0.0 --state=listed
```

**Example: Deprecating an NPM package with a reason** requires a feed (`MyNpmFeed`),  the name (e.g. `@myScope/myNpmPackage`), a version (e.g. `2.0.0`), and optionally a reason:

```bash
pgutil packages status deprecated --feed MyNpmFeed --package=@myScope/myNpmPackage --version=2.0.0 --reason=CriticalBugs
```

Note source options must also be specified unless you have the "Default" source configured, and that a feed may be instead specified in the soruce. See [Working with Sources](/docs/proget/reference-api/proget-pgutil#sources) to learn more.

## HTTP Request Specification
To set a package's status, simply `POST` or `PUT` to the URL with a feed name, [package identifiers](/docs/proget/reference-api/proget-api-packages#using-multiple-parameters), an [appropriate API Key](/docs/proget/reference-api/proget-api-packages#authentication) and a [PackageStatus](/docs/proget/reference-api/proget-api-packages#package-status) object as the request body.

```plaintext
POST /api/packages/«feed-name»/status?«package-identifiers»
```

Unless you use a `purl`, the parameters required will vary by feedtype. 

## HTTP Response Specification
| Response | Details |
| --- | --- |
| **200 (Success)** | indicates the package status was set
| **400 (Invalid Input)** | indicates invalid or missing properties on the input; the body will provide some details as text
|  **403 (Unauthorized API Key)** | indicates a [missing, unknown, or unauthorized API Key](/docs/proget/reference-api/proget-api-packages#authentication); the package status will not be updated
| **500 (Server Error)** | indicates an unexpected error; the body will contain the message and stack trace, and this will also be logged

## Sample Usage Scripts

### Deprecate All Packages Below a Specific Version (Powershell)
This script will deprecate all versions of `GeneralUtils.NET` package below version `3.0.0` in the `private-nuget` feed, with the reason given as "Legacy (no longer maintained)"

```powershell
$proGetBaseUrl = "https://proget.corp.local"
$apiKey = "a1b2c3d4e5"
$feedName = "private-nuget"
$packageName = "GeneralUtils.NET"
$deprecatedReason = @("Legacy (no longer maintained)") 
$targetVersion = "3.0.0"

$uri = "$proGetBaseUrl/api/packages/$feedName/versions?name=$packageName"
$versionsResponse = Invoke-RestMethod -Uri $uri -Headers @{"X-ApiKey" = $apiKey}
$packageVersions = $versionsResponse | Where-Object { $_.version -lt $targetVersion }

foreach ($packageVersion in $packageVersions) {
    $uri = "$proGetBaseUrl/api/packages/$feedName/status?name=$packageName&version=$($packageVersion.version)"
    
    $deprecationInfo = @{
        reason = $deprecatedReason
        alternateName = $packageName
        alternateVersions = $packageVersion.version
    }
    
    $requestBody = @{
        listed = $false
        deprecation = $deprecationInfo
    } | ConvertTo-Json
    
    Invoke-RestMethod -Uri $uri -Method Post -Headers @{"X-ApiKey" = $apiKey} -Body $requestBody
}

Write-Host "Deprecation completed for versions of $packageName below $targetVersion in $feedName feed."
```

Running this script will output something like this:

```powershell
Deprecation completed for versions of GeneralUtils.NET below 13.0.0 in test-feed-2 feed.
Deprecated 2.0.3 of GeneralUtils.NET in private-nuget feed.
Deprecated 2.0.3-beta2 of GeneralUtils.NET in private-nuget feed.
Deprecated 2.0.2-beta2 of GeneralUtils.NET in private-nuget feed.
Deprecation completed for versions of GeneralUtils.NET below 3.0.0 in private-nuget feed.
```

### Delist All Versions Except for the Latest Version (Powershell)
This script will delist all versions of the `GeneralUtils.NET` package in the `private-nuget` feed, except for the latest version.

```powershell
$proGetBaseUrl = "https://proget.corp.local"
$apiKey = "a1b2c3d4e5"
$feedName = "private-nuget"
$packageName = "GeneralUtils.NET"
$deprecatedReason = @("Legacy (no longer maintained)") 
$targetVersion = "3.0.0"

# Define a function to compare version numbers as numbers
function Compare-Versions ($a, $b) {
    $aVersion = [System.Version]::new($a)
    $bVersion = [System.Version]::new($b)
    return $aVersion.CompareTo($bVersion)
}

$uri = "$proGetBaseUrl/api/packages/$feedName/versions?name=$packageName"
$versionsResponse = Invoke-RestMethod -Uri $uri -Headers @{"X-ApiKey" = $apiKey}
$packageVersions = $versionsResponse | Where-Object { (Compare-Versions $_.version $targetVersion) -lt 0 }

foreach ($packageVersion in $packageVersions) {
    $uri = "$proGetBaseUrl/api/packages/$feedName/status?name=$packageName&version=$($packageVersion.version)"
    
    $deprecationInfo = @{
        reason = $deprecatedReason
        alternateName = $packageName
        alternateVersions = $packageVersion.version
    }
    
    $requestBody = @{
        listed = $false
        deprecation = $deprecationInfo
    } | ConvertTo-Json
    
    Invoke-RestMethod -Uri $uri -Method Post -Headers @{"X-ApiKey" = $apiKey} -Body $requestBody
}

Write-Host "Deprecation completed for versions of $packageName below $targetVersion in $feedName feed."
```

Running this script will output something like this:

```powershell
Delisted 12.0.3 of GeneralUtils.NET
Delisted 12.0.3-beta2 of GeneralUtils.NET
Delisted 12.0.2-beta2 of GeneralUtils.NET
Delisting completed for all versions below version (13.0.3) in private-nuget.
```




