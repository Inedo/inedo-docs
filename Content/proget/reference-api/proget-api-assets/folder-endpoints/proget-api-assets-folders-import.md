---
title: "Import Archive"
order: 5
---

*Import Archive* is available as both a `pgutil` command and an HTTP Request, and will add the contents of an uploaded archive in `zip` or `tar` format to the specified folder path. 

:::(Info) (🚀 Quick Example: Importing an archive with pgutil)
This example will import an archive `data-import.zip` from the local path `C:\Inedo\data-files` in to the `data-files` folder in the asset directory `myAssetDirectory`

```bash
pgutil assets folder import --file=C:\Inedo\data-files\data-import.zip --path=data-files --feed=myAssetDirectory
```
:::

## Command Specification (CLI)
The `assets folder import` command is used to import the contents of the specified `zip` or a `.tar.gz` archive as a folder in an asset directory. 

The `--file` and `--path` options are always required. The `--feed` option is required if there is no default feed configured.

**Importing an archive** requires the path of the archive (e.g. `C:\Inedo\myArchive.zip`), the asset directory (e.g. `myAssetDirectory`) and folder name (e.g. `myFolder`):

```plaintext
pgutil assets folder import  --file=C:\Inedo\data-files\myArchive.zip --path=myFolder --feed=myAssetDirectory
```

## HTTP Request Specification
To import an archive, simply `POST` to the URL with the `AssetDirectoryName`, path to the folder and the format.

```plaintext
POST /endpoints/«AssetDirectoryName»/import/«path_to_folder»?format=«zip/tgz»&overwrite=«true/false»
```

The `format` argument may be either `zip` (for a zip file) or `tgz` (for a GZipped tar file). When `overwrite` is `false` or not specified, items already in the asset directory will never be overwritten. When `overwrite` is `true`, items in the asset directory will be overwritten. If the specified folder does not exist, it will be created. 

## HTTP Response Specification

| Response | Details |
| --- | --- |
| **200 (Success)** | the contents of the archive is uploaded to the asset directory |
| **400 (Invalid Format)** | indicates that the format is invalid |
| **401 (Authentication Required)** | indicates a [missing, unknown, or unauthorized API Key](/docs/proget/reference-api/proget-api-assets#authentication) |

## Sample Usage Scripts

### Upload multiple archives (Powershell)
This script uploads all archives (`zip` or `tgz`) in a folder, `C:\Proget\development-files` and creates a folder for each based on the name of the archive:

```powershell
$apiKey = "a1b2c3d4e5"
$folderPath = "C:\Proget\development-files"
$baseUrl = "https://proget.corp.local"
$assetDirectory = "internal-files"

$headers = @{
    "X-ApiKey" = $apiKey
}

$fileList = Get-ChildItem -Path $folderPath -File | Where-Object { $_.Extension -eq ".zip" -or $_.Extension -eq ".tgz" }

foreach ($file in $fileList) {
    $folderName = [System.IO.Path]::GetFileNameWithoutExtension($file.Name)

    $dynamicEndpoint = "$baseUrl/endpoints/$assetDirectory/import/$folderName"

    $response = Invoke-RestMethod -Uri $dynamicEndpoint -Method Post -Headers $headers -InFile $file.FullName -ContentType "application/octet-stream"

    Write-Host "Uploaded $($file.Name)"
    Write-Host $response
}
```