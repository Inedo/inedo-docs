---
title: "Download Asset File"
order: 2
---

*Download File* is available as both a `pgutil` command and an HTTP Request, and will return the file as content, allowing the file to be downloaded. 

:::(Info) (🚀 Quick Example: Downloading a file with pgutil)
This example will download a file `info.txt` located in the folder `test-files` of the asset directory `myAssetDirectory` to the local path `C:\Inedo\test-files`:

```bash
pgutil assets download --feed=myAssetDirectory --path=test-files/info.txt --output=C:\Inedo\test-files
```
:::

## Command Specification (CLI)
The `assets download` command is used to download a file from the asset directory.

The`--path` and `--output` options are always required. The `--feed` option is required if there is no default feed configured.

**Downloading a file** requires the asset directory (e.g. `MyAssetDirectory`), the path of the file (e.g. `test-files/info.txt`) and the output path to download to (e.g. `C:\Inedo\test-files`):

```bash
pgutil assets download --feed=myAssetDirectory --path=test-files/info.txt --output=C:\Inedo\test-files
```

## HTTP Request Specification
To return the content a file, `GET` to the URL with the `AssetDirectoryName`, the `file path`, and an [appropriate API key](/docs/proget/api/assets#authentication).

```plaintext
GET /endpoints/«AssetDirectoryName»/content/«path_to_file»
```

To return only the headers of a file, simply `HEAD` to the URL with the `AssetDirectoryName`, the `file path`, and an [appropriate API key](/docs/proget/api/assets#authentication).

```plaintext
HEAD /endpoints/«AssetDirectoryName»/content/«path_to_file»
```

## HTTP Response Specification
A successful `200` response to a `GET` or `HEAD` request will contain the file headers. A `401` response indicates a [missing, unknown, or unauthorized API Key](/docs/proget/api/assets#authentication).

## Sample Usage Scripts

### Download all files in a folder (Powershell)
This script downloads all files within an asset directory, `internal-files` from a folder, `download-files`, to a local folder, `C:\Proget\DownloadedFiles`:

:::(info)
This can be used when bulk downloading individual files located in a folder. For a more efficient solution of downloading a batch of files as an archive, use our [Export Folder](/docs/proget/api/assets/folders/export) endpoint.
:::

```powershell
$baseUrl = "http://proget.corp.local"
$assetDirectory = "internal-files"
$assetFolder = "download-files"
$apiKey = "pra1b2c3d4e5oget"
$localFolder = "C:\ProGet\DownloadedFiles"

if (-not (Test-Path -Path $localFolder -PathType Container)) {
    New-Item -Path $localFolder -ItemType Directory | Out-Null
}

$listApiEndpoint = "$baseUrl/endpoints/$assetDirectory/dir/$assetFolder" + "?recursive=false"
$downloadApiEndpoint = "$baseUrl/endpoints/$assetDirectory/content/$assetFolder/"

$headers = @{"X-ApiKey" = $apiKey}

function Download-File {
    param (
        [string]$fileUrl,
        [string]$localFilePath
    )

    try {
        Invoke-WebRequest -Uri $fileUrl -OutFile $localFilePath -Headers $headers -ErrorAction Stop
        Write-Host "Downloaded: $($localFilePath | Split-Path -Leaf)"
    } catch {
        Write-Host "Failed to download: $($localFilePath | Split-Path -Leaf). $_"
    }
}

try {
    $listResponse = Invoke-RestMethod -Uri $listApiEndpoint -Method Get -Headers $headers

    if ($listResponse.Count -gt 0) {
        foreach ($fileInfo in $listResponse) {
            $fileName = $fileInfo.name
            $fileDownloadUrl = $fileInfo.content
            $completeDownloadUrl = $downloadApiEndpoint + $fileName
            $localFilePath = Join-Path $localFolder $fileName
            Download-File -fileUrl $completeDownloadUrl -localFilePath $localFilePath
        }

        Write-Host "All files downloaded successfully."
    } else {
        Write-Host "The folder is empty"
    }
} catch {
    Write-Host "Failed to retrieve the list of files. $_"
}
```