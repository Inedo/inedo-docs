---
title: "Import Universal Package"
order: 1
---

The *Import Universal Package* is an endpoint in [ProGet's Universal Feed API](/docs/proget/api/universal-feed) that will add a package from an asset directory, file share, or local path on the ProGet server.

:::(Info) (🚀 Quick Example: Importing a Universal Package from an asset directory with Curl)
This example imports version `1.2.3` of a universal package `myUniversalPackage`, from an asset directory `internal-files`, to the feed `myUniversalFeed`, authenticating with the API key `abc12345`:

````
curl -X POST -H "X-ApiKey: abc12345" -H "Content-Type: application/json" -d "{\"assetDirectory\": \"internal-files\", \"from\": \"MyUniversalPackage-1.0.0.upack\"}" "https://proget.corp.local/upack/myUniversalFeed/import"
````
:::

* Content Type: The `Content-Type` header should be `application/json`.

### Content Parameter	Description
These properties are specified as properties of the JSON object in the request content body:

| Parameter | Details |
| --- | --- |
| `assetDirectory` | Name of the asset directory to import the package from. The asset directory must be in the same ProGet instance as the Universal feed. |
| `from` | Required. Path of `.upack` file to import. If `assetDirectory` is specified, this refers to the path within the specified asset directory. Otherwise, it is a local/UNC file path relative to the ProGet server. |
| `deleteFromSource` | Specifies whether to delete the file from the source after import. The default is `true`. |

The intended usage of this endpoint is to allow an asset directory to act as a staging area for uploading very large universal packages. For optimal performance, `assetDirectory` should be specified, the asset directory and universal package feed should both be configured for local disk storage, and `deleteFromSource` must be true. 

In this case, a fast filesystem move operation can be performed. In all other cases, the operation will still complete, but may take longer depending on the size of the package imported.

## Request Specification
To import a package, simply `POST` to the URL with a feed name and JSON object in the request content body:

```
POST /upack/«feed-name»/import
```

**Importing a universal package from an asset directory** requires the feed (e.g. `myUniversalFeed`) and a JSON object as the request body:

```
POST /upack/MyFeed/import "Content-Type: application/json"

{
  assetDirectory: "myAssetDirectory"
  from: "myFolder\example-lib.upack"
}
```

## Response Specification
The package will be imported on a successful `200` response. A `404` response indicates the specified package does not exist.

### Bulk Import of Packages (Powershell)
This script will import all `.upack` and `.vpack` packages from the folder `universal-packages` in the asset directory `internal-files`, to the universal-feed `asset-directory-imports`:

```powershell
$baseUrl = "https://proget.corp.local"
$assetDirectoryApiKey = "abcde12345"
$universalFeedApiKey = "vwxyz67890"
$assetDirectory = "internal-files"
$assetDirectoryFolder = "universal-packages"
$universalFeed = "asset-directory-imports"

$listFolderHeaders = @{"X-ApiKey" = $assetDirectoryApiKey}
$listFolderEndpoint = "$baseUrl/endpoints/$assetDirectory/dir/$assetDirectoryFolder"

$importHeaders = @{"X-ApiKey" = $universalFeedApiKey}
$importEndpoint = "$baseUrl/upack/$universalFeed/import"

$response = Invoke-RestMethod -Uri $listFolderEndpoint -Headers $listFolderHeaders -Method Get

foreach ($file in $response) {
    if ($file.name -like "*.upack" -or $file.name -like "*.vpack") {
        $importPayload = @{
            assetDirectory = $assetDirectory
            from = "$assetDirectoryFolder\$($file.name)"
        } | ConvertTo-Json

        Invoke-RestMethod -Uri $importEndpoint -Headers $importHeaders -Method Post -Body $importPayload

        Write-Host "Imported $($file.name) to the feed."
    }
}
```

Note, this will only import any packages in the root of the asset directory folder given. Any packages within subfolders of this folder will need to be imported separately.
