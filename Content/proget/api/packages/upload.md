---
title: "Upload Package"
order: 4
---

*Upload Package* is available as both a `pgutil` command and an HTTP Request, and will upload a package file to the feed. 

:::(Info) (🚀 Quick Example: Uploading a NuGet package with pgutil)
This example will upload `myNugetPackage` version `1.2.3` to the feed `myNugetFeed`, using the file and path  `C:\inedo\packages\Newtonsoft.Json.12.0.3.nupkg`:

```bash
pgutil packages upload --feed=myNugetFeed --input-file=C:\inedo\packages\Newtonsoft.Json.12.0.3.nupkg
```
:::

This API requires ProGet 2023.0 or later.

## Command Specification (CLI)
The `packages upload` command is used to upload a package to a feed.

The `--input` option is always required. The `--feed` option is required if a default feed is not configured, and the `--distribution` option is required for Debian packages. 

**Uploading an npm Package** requires the feed (e.g. `MyNpmFeed`) and input file path (e.g. `C:\inedo\packages\myNpmPackage.tgz`):

```bash
pgutil packages upload --feed=MyNpmFeed --input-file=C:\inedo\packages\myNpmPackage.tgz
```

**Uploading a Debian Package** requires the feed (e.g. `MyDebianFeed`), the input file path (e.g. `C:\inedo\packages\myDebianPackage_3.0.0_all.deb`), and the distribution (e.g. `main`)

```bash
pgutil packages upload --feed=approved-debian --input-file=C:\inedo\packages\myDebianPackage_3.0.0_all.deb --distribution=main
```

**Uploading a Maven Package** is not supported on [Maven (Classic) feeds](/docs/proget/feeds/maven#maven-classic-feed-types), and requires the feed (e.g. `internal-maven`), the input file path (e.g. `c:\apps\my-app-1.1\my-app-1.1.jar`), and the artifactPath (e.g. `/com/my-company/my-app/1.1`)

```bash
pgutil packages upload --feed=internal-maven --input-file=c:\apps\my-app-1.1\my-app-1.1.jar --artifactPath=/com/my-company/my-app/1.1
```

Note source options must also be specified unless you have the "Default" source configured, and that a feed may be instead specified in the source. See [Working with Sources](/docs/proget/api/pgutil#sources) to learn more.

## HTTP Request Specification

To upload a package, simply `PUT` to the URL with a feed name, [package identifiers](/docs/proget/api/packages#using-multiple-parameters), an [appropriate API Key](/docs/proget/api/packages#authentication) and a package in either `ZIP`, `JAR` or `TAR` format depending on the value of the `Content-Type` header.

```plaintext
PUT /api/packages/«feed-name»/upload?«package-identifiers»
```

Unless you use a `purl`, the parameters required will vary by feedtype. 

:::(info) (📄 Note)
The `«package-file-name»` part of the URL is only required for the PyPI and RPM feed types.
:::

## HTTP Response Specification

A successful `200` response indicates the package was uploaded. A `403` response indicates a [missing, unknown, or unauthorized API Key](/docs/proget/api/security#authentication).

:::(warning) (⚠ Error Messages)
If you receive errors like following, it generally means that an invalid package (i.e. zip file) was uploaded and could not be read by ProGet. This endpoint does not support `multipart/form-data`-encoded content, but instead should be `PUT` with `application/octet-stream`.

```plaintext
End of Central Directory record could not be found.
```

Or

```plaintext
End Of Central Directory does not correspond to number of entries in Central Directory.
```
:::

## Sample Usage Scripts

### Upload A NuGet Package (Curl)
This script will upload the `GeneralUtils.NET.nupkg` package file stored in the local `C:\MyOrganizationFolder\Packages` folder to the `private-nuget` feed.

```bash
@echo off

set api_key=a1b2c3d4e5
set package_file=GeneralUtils.NET.nupkg
set package_file_path=C:\MyOrganizationFolder\Packages\%package_file%
set api_endpoint=https://proget.corp.local/api/packages/private-nuget/upload/

curl -X POST -H "X-ApiKey: %api_key%" -T "%package_file_path%" "%api_endpoint%"
```

### Upload All NuGet Packages in a Folder (Powershell)
This script will upload all `.nupkg` packages stored in the local `C:\MyOrganizationFolder\Packages` folder  to the `private-nuget` feed.

```powershell
$apiUrl = "https://proget.corp.local"
$feedName = "private-nuget"
$apiKey = "a1b2c3d4e5"  
$folderPath = "C:\MyOrganizationFolder\Packages"

$nupkgFiles = Get-ChildItem -Path $folderPath -Filter *.nupkg

foreach ($nupkgFile in $nupkgFiles) {
    $fileFullPath = $nupkgFile.FullName
    $packageName = $nupkgFile.BaseName

    $fullApiUrl = "$apiUrl/api/packages/$feedName/upload/$packageName.nupkg"

    $result = Invoke-WebRequest -Uri $fullApiUrl -Headers @{"X-ApiKey" = $apiKey} -Method POST -InFile $fileFullPath

    if ($result.StatusCode -eq 200) {
        Write-Host "Package '$packageName' uploaded successfully."
    } else {
        Write-Host "Failed to upload package '$packageName'. Status code: $($result.StatusCode)"
    }
}
```

Running this script will output something like this:

```powershell
Package 'NewUtils.5.1.0' uploaded successfully.
Package 'NewUtils.5.1.1' uploaded successfully.
Package 'GeneralUtils.NET.12.0.2-beta2' uploaded successfully.
Package 'GeneralUtils.NET.12.0.3-beta2' uploaded successfully.
Package 'GeneralUtils.NET.12.0.3' uploaded successfully.
Package 'GeneralUtils.NET.13.0.3' uploaded successfully.
```

### Upload All NuGet Packages to Specific Feeds (Python)
This script will upload all `.nupkg` packages stored in the local `C:\MyOrganizationFolder\Packages` folder, where `C:\MyOrganization\Packages\private-nuget-1`will upload packages to the `private-nuget-1` feed and so on.

```python
import os
import requests

api_url = "https://proget.corp.local"
api_key = "a1b2c3d4e5"
folder_path = r"C:\MyOrganization\Packages"

for root, _, files in os.walk(folder_path):
    for file_name in files:
        feed_name = os.path.relpath(root, folder_path).replace("\\", "/")
        package_name = file_name
        upload_url = f"{api_url}/api/packages/{feed_name}/upload/{package_name}"

        try:
            with open(os.path.join(root, file_name), "rb") as package_file:
                response = requests.post(
                    upload_url, headers={"X-ApiKey": api_key}, files={"file": package_file}
                )
                response.raise_for_status()
                print(f'Uploading "{package_name}" to "{feed_name}" feed... Success')
        except requests.exceptions.RequestException as e:
            print(f'Uploading "{package_name}" to "{feed_name}" feed... Failed: {e}')
```

Running this script will output something like this:

```python
Uploading "NewUtils.5.1.0.nupkg" to "private-nuget-2" feed... Success
Uploading "NewUtils.5.1.1.nupkg" to "private-nuget-2" feed... Success
Uploading "GeneralUtils.NET.12.0.2-beta2.nupkg" to "private-nuget-1" feed... Success
```

If the feed does not exist, the following message will be returned:

```python
Uploading "RandomUtils.3.0.3.nupkg" to "private-nuget-3" feed... Failed: Feed private-nuget-3 not found.
```
