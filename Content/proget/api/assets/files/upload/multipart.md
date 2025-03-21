---
title: "Multipart Asset File Upload"
order: 1
---

The *Multipart Asset File Upload* is an HTTP Request specifically for multipart uploads of very large (2GB+) files. 

The HTTP request can be used to initiate, continue or complete a multipart upload. The file will not be added to the asset directory until the upload has been marked as complete with this endpoint.

## HTTP Request Specifications

### Initiate Multipart Upload
To initiate a multipart upload, simply `POST` to the URL with the `AssetDirectoryName` and the necessary parameters.

All arguments are required to initiate or continue a multipart upload. Only `id` is required to complete the upload:

| Parameter | Details |
| --- | --- |
| `id` | Unique identifier for the upload. It is the client's responsibility to generate this. It does not need to be a GUID, though a GUID is perfectly valid. |
| `index` | Zero-based index of the part being uploaded. |
| `offset` | Zero-based byte offset of the part being uploaded. |
| `totalSize` | Size of the entire file being uploaded in bytes. This must be exactly the sum of all individual uploaded part sizes. |
| `partSize` | Size of the part being uploaded. |
| `totalParts` | Total number of parts that will be uploaded for the entire file. |


```plaintext
POST /endpoints/«AssetDirectoryName»/content/«path»?multipart=upload&id=«uuid»&index=«partIndex»&offset=«byteOffset»&totalSize=«byteSize»&partSize=«partSize»&totalParts=«partCount»
```

### Complete Multipart Upload

To complete a multi-part upload, simply `POST` to the URL with the `AssetDirectoryName` and the unique identifier `id`.

```plaintext
POST /endpoints/«AssetDirectoryName»/content/«path»?multipart=complete&id=«uuid»
```

:::(info) (📄 Note)
Orphaned parts will be deleted during the "Feed Cleanup" scheduled task. Orphaned parts can occur when a client uploads one or more parts but never completes the upload.
:::

## HTTP Response Specification

| Response | Details |
| --- | --- |
| **200 (Success)** | successfully initiates, continues or completes a multipart upload |
| **400 (Invalid Arguements)** | indicates invalid arguments such as index/offset out of range, overlapping parts or invalid size |
| **401 (Authentication Required)** | indicates a [missing, unknown, or unauthorized API Key](/docs/proget/api/assets#authentication) |

## Sample Usage Scripts


### Multipart Upload (Powershell - Single Script)

This PowerShell script can be used to automatically perform a multipart upload if necessary. 

In this example, it uploads `application_data.bin` from `C:\ProGet` to a folder (`multipart`) in an asset directory (`internal-files`). `$ChunkSize` indicates the minimum size of the file in order to perform a multipart upload (e.g. `5 MB`)

```powershell
$LocalFileName = "C:\ProGet\application_data.bin"
$EndpointUrl = "https://proget.corp.local/endpoints/internal-files"
$FilePath = "multipart/application_data.bin"
$ChunkSize = 5 * 1024 * 1024
$ApiKey = "abc12345"

function Upload-ProGetAsset {
    param(
        [Parameter(Mandatory = $true)]
        [string] $fileName,
        [Parameter(Mandatory = $true)]
        [string] $endpointUrl,
        [Parameter(Mandatory = $true)]
        [string] $assetName,
        [int] $chunkSize = 5 * 1024 * 1024,
        [string] $apiKey
    )

    function CopyMaxBytes {
        param($source, $target, $maxBytes, $startOffset, $totalSize)
        $buffer = [Array]::CreateInstance([System.Byte], 32767)
        $totalBytesRead = 0
        while ($true) {
            $bytesRead = $source.Read($buffer, 0, [Math]::Min($maxBytes - $totalBytesRead, $buffer.Length))
            if(!$bytesRead) { break }
            $target.Write($buffer, 0, $bytesRead)
            $totalBytesRead += $bytesRead
            if($totalBytesRead -ge $maxBytes) { break }
            $overallProgress = $startOffset + $totalBytesRead
            Write-Progress -Activity "Uploading $fileName..." -Status "$overallProgress/$totalSize" -PercentComplete ($overallProgress / $totalSize * 100)
        }
    }

    if(-not $endpointUrl.EndsWith('/')) { $endpointUrl += '/' }

    $targetUrl = $endpointUrl + 'content/' + [Uri]::EscapeUriString($assetName.Replace('\', '/'))

    $fileInfo = (Get-ChildItem -Path $fileName)
    if($fileInfo.Length -le $chunkSize) {
        Invoke-WebRequest -Method Post -Uri $targetUrl -InFile $fileName -Headers @{"X-ApiKey" = $apiKey}
    } else {
        $sourceStream = New-Object IO.FileStream -ArgumentList $fileName, [IO.FileMode]::Open, [IO.FileAccess]::Read, [IO.FileShare]::Read, 4096, [IO.FileOptions]::SequentialScan
        try {
            $fileLength = $sourceStream.Length
            $remainder = [long]0
            $totalParts = [Math]::DivRem([long]$fileLength, [long]$chunkSize, [ref]$remainder)
            if($remainder -ne 0) { $totalParts++ }
            $uuid = (New-Guid).ToString("N")

            0..($totalParts-1) | ForEach-Object {
                $index = $_
                $offset = $index * $chunkSize
                $currentChunkSize = if($index -eq ($totalParts - 1)) { $fileLength - $offset } else { $chunkSize }

                $req = [System.Net.WebRequest]::CreateHttp("${targetUrl}?multipart=upload&id=$uuid&index=$index&offset=$offset&totalSize=$fileLength&partSize=$currentChunkSize&totalParts=$totalParts")
                $req.Method = 'POST'
                $req.Headers.Add("X-ApiKey", $apiKey)
                $req.ContentLength = $currentChunkSize
                $req.AllowWriteStreamBuffering = $false
                $reqStream = $req.GetRequestStream()
                try { CopyMaxBytes -source $sourceStream -target $reqStream -maxBytes $currentChunkSize -startOffset $offset -totalSize $fileLength }
                finally { if($reqStream) { $reqStream.Dispose() } }

                $response = $req.GetResponse()
                try { } finally { if($response) { $response.Dispose() } }
            }

            Write-Progress -Activity "Uploading $fileName..." -Status "Completing upload..." -PercentComplete -1

            $req = [System.Net.WebRequest]::CreateHttp("${targetUrl}?multipart=complete&id=$uuid")
            $req.Method = 'POST'
            $req.Headers.Add("X-ApiKey", $apiKey)
            $req.ContentLength = 0
            $response = $req.GetResponse()
            try { } finally { if($response) { $response.Dispose() } }
        }
        finally { if($sourceStream) { $sourceStream.Dispose() } }
    }
}

Upload-ProGetAsset -FileName $LocalFileName -AssetName $FilePath -EndpointUrl $EndpointUrl -ApiKey $ApiKey
```

### Multipart Upload (Powershell - .PS1 file)

This PowerShell script can be used by saving it (e.g. `Upload-ProGetAsset.ps1`), loading it in a Powershell terminal, and then running the following:

```powershell
Upload-ProGetAsset -FileName "C:\ProGet\application_data.bin" -AssetName "multipart/application_data.bin" -EndpointUrl "https://proget.corp.local/endpoints/internal-files"
```

In this example, it uploads `application_data.bin` from `C:\ProGet` to a folder (`multipart`) in an asset directory (`internal-files`)

```powershell
<#
.Synopsis
Transfers a file to a ProGet asset directory.
.Description
Transfers a file to a ProGet asset directory. This function performs automatic chunking
if the file is larger than a specified threshold.
.Parameter FileName
Name of the file to upload from the local file system.
.Parameter EndpointUrl
Full URL of the ProGet asset directory's API endpoint. This is typically something like http://proget/endpoints/<directoryname>
.Parameter AssetName
Full path of the asset to create in ProGet's asset directory.
.Parameter ChunkSize
Uploads larger than this value will be uploaded using multiple requests. The default is 5 MB.
.Example
Upload-ProGetAsset -FileName C:\Files\Image.jpg -AssetName images/image.jpg -EndpointUrl http://proget/endpoints/MyAssetDir
#>

function Upload-ProGetAsset {
    param(
        [Parameter(Mandatory = $true)]
        [string] $fileName,
        [Parameter(Mandatory = $true)]
        [string] $endpointUrl,
        [Parameter(Mandatory = $true)]
        [string] $assetName,
        [int] $chunkSize = 5 * 1024 * 1024
    )

    function CopyMaxBytes {
        param($source, $target, $maxBytes, $startOffset, $totalSize)
        $buffer = [byte[]]::CreateInstance([byte], 32767)
        $totalBytesRead = 0
        while ($true) {
            $bytesRead = $source.Read($buffer, 0, [Math]::Min($maxBytes - $totalBytesRead, $buffer.Length))
            if(!$bytesRead) { break }
            $target.Write($buffer, 0, $bytesRead)
            $totalBytesRead += $bytesRead
            if($totalBytesRead -ge $maxBytes) { break }
            $overallProgress = $startOffset + $totalBytesRead
            Write-Progress -Activity "Uploading $fileName..." -Status "$overallProgress/$totalSize" -PercentComplete ($overallProgress / $totalSize * 100)
        }
    }

    if(-not $endpointUrl.EndsWith('/')) { $endpointUrl += '/' }
    $targetUrl = $endpointUrl + 'content/' + [Uri]::EscapeUriString($assetName.Replace('\', '/'))
    $fileInfo = Get-ChildItem -Path $fileName

    if($fileInfo.Length -le $chunkSize) {
        Invoke-WebRequest -Method Post -Uri $targetUrl -InFile $fileName
    } else {
        $sourceStream = [System.IO.FileStream]::new($fileName, [System.IO.FileMode]::Open, [System.IO.FileAccess]::Read, [System.IO.FileShare]::Read, 4096, [System.IO.FileOptions]::SequentialScan)

        try {
            $fileLength = $sourceStream.Length
            $remainder = 0
            $totalParts = [Math]::DivRem($fileLength, $chunkSize, [ref]$remainder)
            if($remainder -ne 0) { $totalParts++ }
            $uuid = [Guid]::NewGuid().ToString("N")

            0..($totalParts-1) | ForEach-Object {
                $index = $_
                $offset = $index * $chunkSize
                $currentChunkSize = if($index -eq ($totalParts - 1)) { $fileLength - $offset } else { $chunkSize }

                $req = [System.Net.WebRequest]::CreateHttp("${targetUrl}?multipart=upload&id=$uuid&index=$index&offset=$offset&totalSize=$fileLength&partSize=$currentChunkSize&totalParts=$totalParts")
                $req.Method = 'POST'
                $req.ContentLength = $currentChunkSize
                $req.AllowWriteStreamBuffering = $false
                $reqStream = $req.GetRequestStream()

                try { CopyMaxBytes -source $sourceStream -target $reqStream -maxBytes $currentChunkSize -startOffset $offset -totalSize $fileLength }
                finally { if($reqStream) { $reqStream.Dispose() } }

                $response = $req.GetResponse()
                try { } finally { if($response) { $response.Dispose() } }
            }

            Write-Progress -Activity "Uploading $fileName..." -Status "Completing upload..." -PercentComplete -1

            $req = [System.Net.WebRequest]::CreateHttp("${targetUrl}?multipart=complete&id=$uuid")
            $req.Method = 'POST'
            $req.ContentLength = 0
            $response = $req.GetResponse()
            try { } finally { if($response) { $response.Dispose() } }
        }
        finally { if($sourceStream) { $sourceStream.Dispose() } }
    }
}
```