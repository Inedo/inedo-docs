---
title: "Set Asset Metadata"
order: 1
---

*Update Asset Metadata* is available as both a `pgutil` command and an HTTP Request, and will set or update the `Content-Type` or the user-defined metadata of an "item" (file or folder). The request body must have a `Content-Type` of application/json, and must contain a [AssetItemMetadataUpdate](/docs/proget/api/assets#metadata-update) JSON object.

:::(Info) (🚀 Quick Example: Setting asset custom metadata with pgutil)
This example will set custom metadata for the file `data.bin` in the `data-files` folder of the asset directory `myAssetDirectory`, with the key `myKey` and value `myValue`:

```bash
pgutil assets metadata set custom --path=data-files/data.bin --feed=myAssetDirectory --key=myKey --value=myValue
```
:::

## Command Specification (CLI)
The `assets metadata set` command is used to write metadata for the specified asset item.

There are two commands available (`custom`, and `cache`), and each command has options that correspond to fields in an asset's metadata. See [Custom Metadata](/docs/proget/asset-directories-file-storage/what-is-an-asset-directory#custom-metadata) to learn more. 

| Command | Description | Options  |
| --- | --- | --- |
| `custom` | Sets a custom metadata field | `--key` `--value` `--include-in-http-response` |
| `cache`  | Sets the cache header | `--type` `--value` | 

For both commands `--path` option is always required. The `--feed` option is required if there is no default feed configured. 

The `--key` and `--value` options are required for the `custom` command. The `--value` option is required for the `cache` command.

**Updating an asset's custom metadata** requires the asset directory (e.g. `MyAssetDirectory`), asset path (e.g. `data-files/data.bin`), the metadata key (e.g. `myKey`) and metadata value (e.g. `myValue`):

```bash
pgutil assets metadata set custom --path=data-files/data.bin --feed=myAssetDirectory --key=myKey --value=myValue
```

**Updating an asset's cache header** requires the asset directory (e.g. `MyAssetDirectory`), asset path (e.g. `data-files/data.bin`), the header type (e.g. `TTL`) and value (e.g. `60`):

```bash
pgutil assets metadata set cache --path=data-files/data.bin --feed=myAssetDirectory --type=TTL --value=60
```

## HTTP Request Specification
To update an item's metadata, simply `POST` to the URL with the `AssetDirectoryName`, path to the item and a a [AssetItemMetadataUpdate](/docs/proget/api/assets#metadata-update) object as the body.

```plaintext
GET /endpoints/«AssetDirectoryName»/metadata/«path_to_file»
```

## HTTP Response Specification

| Response | Details |
| --- | --- |
| **200 (Success)** | the file or folder's metadata will be updated |
| **401 (Authentication Required)** | indicates a [missing, unknown, or unauthorized API Key](/docs/proget/api/assets#authentication) |
| **404 (Item not Found)** | indicates that the file or folder does not exist |
