---
title: "Download Package"
order: 3
---

*Download Package* is available as both a `pgutil` command and an HTTP Request, and will download a package to the specified path or return it as content.

:::(Info) (🚀 Quick Example: Downloading a package with pgutil)
This example downloads version `1.2.3` of the package `myNugetPackage` to the path `C:\packages\nuget-packages`
```
pgutil packages download --feed=myNugetFeed --package=myNugetPackage --version=1.2.3 --output-file=C:\packages\nuget-packages\myNugetPackage.nupkg
```
:::

## Command Specification (CLI)
The `packages download` command is used to list files and folders in an asset directory.

The `--package` and `--version` options are always required. The `--feed` option is required if there is no default feed configured.

Additional options may be required depending on the package type. If a required option is missing, an error will be returned indicating the missing option.

**Downloading a NuGet Package** requires the package (e.g. `myNugetPackage`) and version (e.g. `1.2.3`)
```
pgutil packages download --feed=myNugetFeed --package=myNugetPackage --version=1.2.3 --output-file=C:\packages\nuget-packages\myNugetPackage.nupkg
```
**Downloading an npm Package** requires the package (e.g. `myNpmPackage`), version (e.g. `4.5.6`) and scope (e.g. `@scope`)
```
pgutil packages download --feed=myNpmFeed --package=@scope/myNpmPackage --version=4.5.6 --output-file=C:\npm-packages\package.tgz
```
**Downloading a Pypi Package** requires the package (e.g. `myPypiPackage`), version (e.g. `7.8.9`) and filename (e.g. `myPypiPackage-7.8.9.tar.gz`)
```
pgutil packages download --feed=myPypiFeed --package=myPypiPackage --version=7.8.9 --filename=myPypiPackage-7.8.9.tar.gz --output-file=C:\pypi-packages\myPypiPackage-7.8.9.tar.gz
```

**Downloading a Debian Package** requires the package (e.g. `myDebianPackage`), version (e.g. `2.3.4`), component (e.g. `main`), distribution (e.g. `stable`) and architecture (e.g. `amd64`)
```
pgutil packages download --feed=myDebianFeed --package=myDebianPackage --version=2.3.4 --component=main --distro=stable --arch=amd64 --output-file=C:\debian-packages\myDebianPackage_2.3.4_amd64.deb
```

## HTTP Request Specification
To download a package, simply `GET` to the URL with a feed name, [package identifiers](/docs/proget/reference-api/proget-api-packages#using-multiple-parameters), and an [appropriate API Key](/docs/proget/reference-api/proget-api-packages#authentication).
````
GET /api/packages/«feed-name»/download?«package-identifiers»
````
Unless you use a `purl`, the parameters required will vary by feedtype. 
:::(info) (📄 Note)
The package identifier you use must resolve to a single package, otherwise a `400` will be returned.
:::

## HTTP Response Specification
| Response | Details |
| --- | --- |
| **200 (Success)** | the package file is returned as content
| **403 (Unauthorized API Key)** | indicates a [missing, unknown, or unauthorized API Key](/docs/proget/reference-api/proget-api-packages#authentication); the package file will not download
| **404 (Package Not Found)** | indicates that the specified package does not exist
| **500 (Server Error)** | indicates an unexpected error; the body will contain the message and stack trace, and this will also be logged

## Sample Usage Scripts

### Download A Package (Curl)
This script will download version 3.0.3 of the `GeneralUtils.NET` package in the `private-nuget` feed.
```bash
api_key="a1b2c3d4e5"
output_file="C:/MyFiles/GeneralUtils.NET-3.0.3.nupkg"
base_url="https://proget.corp.local"
nuget_feed="private-nuget"
nuget_package="GeneralUtils.NET"
package_version="3.0.3"

curl_command="curl -o \"$output_file\" --header \"X-ApiKey: $api_key\" \"$base_url/api/packages/$nuget_feed/download?purl=pkg:nuget/$nuget_package@$package_version\""

eval $curl_command
```
### Powershell: Download Latest Package (Powershell)
This script will download the latest stable (non-prerelease) version of the `GeneralUtils.NET` package in the `private-nuget` feed.
```powershell
$apiUrl = "https://proget.corp.local"
$apiKey = "a1b2c3d4e5" 
$feedName = "private-nuget" 
$packageName = "GeneralUtils.NET"
$downloadPath = "C:\MyOrganizationFolder\GeneralUtilsPackages"
$queryUrl = "$apiUrl/api/packages/$feedName/latest?name=$packageName&stableOnly=true"

$overwritePrompt = $true # Set to $false if you don't want to prompt for overwriting

$response = Invoke-RestMethod -Uri $queryUrl -Method Get -Headers @{ "X-APIKey" = $apiKey }

if ($response -eq $null) {
    Write-Host "No package information found. Please check the feed and package name."
} else {
    $latestVersion = $response[0].version
    $downloadUrl = "$apiUrl/api/packages/$feedName/download?name=$packageName&version=$latestVersion"
    $outputFileName = "$downloadPath\$packageName-$latestVersion.nupkg"

    if (Test-Path $outputFileName) {
        if ($overwritePrompt) {
            $userInput = Read-Host "The file '$outputFileName' already exists. Do you want to overwrite it? (Y/N)"
            if ($userInput -eq "Y" -or $userInput -eq "y") {
                Invoke-WebRequest -Uri $downloadUrl -OutFile $outputFileName -Headers @{ "X-APIKey" = $apiKey }
                Write-Host "Package downloaded and overwritten: $outputFileName"
            } else {
                Write-Host "Download canceled. The existing file remains: $outputFileName"
            }
        } else {
            Write-Host "The file '$outputFileName' already exists. Use -overwritePrompt $true to enable overwriting."
        }
    } else {
        Invoke-WebRequest -Uri $downloadUrl -OutFile $outputFileName -Headers @{ "X-APIKey" = $apiKey }
        Write-Host "Package downloaded: $outputFileName"
    }
}
```
This will download the package to the folder `C:\MyOrganizationFolder\GeneralUtilsPackages`. If the file already exists the user will be prompted to overwrite the existing file or not.

### Download All Packages (Python)
This script will download all versions of the `GeneralUtils.NET` package in the `private-nuget` feed.
```python
import os
import requests

api_key = "a1b2c3d4e5"
feed_name = "private-nuget"
package_name = "GeneralUtils.NET"
base_url = "https://proget.corp.local"
target_directory = r"C:\MyOrganizationFolder\GeneralUtilsPackages"

if not os.path.exists(target_directory):
    os.makedirs(target_directory)

package_versions_url = f"{base_url}/api/packages/{feed_name}/versions?name={package_name}&apikey={api_key}"
package_versions_response = requests.get(package_versions_url)
package_versions = package_versions_response.json()

for version in package_versions:
    version_name = version["version"]
    download_url = f"{base_url}/api/packages/{feed_name}/download?name={package_name}&version={version_name}&apikey={api_key}"
    target_file = os.path.join(target_directory, f"{package_name}.{version_name}.nupkg")

    if os.path.exists(target_file):
        print(f"File '{target_file}' already exists.")
        user_input = input("Do you want to overwrite the existing file? (Y/N): ").strip().upper()
        if user_input != "Y":
            print(f"Skipping download of {package_name} version {version_name}.")
            continue

    response = requests.get(download_url)
    if response.status_code == 200:
        with open(target_file, 'wb') as file:
            file.write(response.content)
        print(f"Downloaded {package_name} version {version_name} to {target_file}")
    else:
        print(f"Failed to download {package_name} version {version_name}")

print(f"All versions of {package_name} have been downloaded to {target_directory}.")
```
This will download the all packages to the folder `C:\MyOrganizationFolder\GeneralUtilsPackages`. If the file already exists the user will be prompted to overwrite the existing file or not.