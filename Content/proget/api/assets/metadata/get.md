---
title: "Get Asset Metadata"
order: 2
---

*Get Asset Metadata* is available as both a `pgutil` command and an HTTP Request, and will return metadata for the specified asset "item" (file or folder) as a JSON [AssetDirectoryItem](/docs/proget/api/assets#item-data) object.

:::(Info) (🚀 Quick Example: Getting asset metadata with pgutil)
This example will return the metadata for the file `data.bin` in the `data-files` folder of the asset directory `myAssetDirectory`:

```bash
pgutil assets metadata get --path=data-files/data.bin --feed=myAssetDirectory
```
:::

## Command Specification (CLI)
The `assets metadata get` command is used to return metadata for the specified asset item.

The `--path` option is always required. The `--feed` option is required if there is no default feed configured.

:::(info)(📄 Note: pgutil Sources)
Source options must also be specified unless you have the "Default" source configured, and that a feed may be instead specified in the source. See [Working with Sources](/docs/proget/api/pgutil#sources) to learn more.
:::

**Getting asset metadata** requires the asset directory (e.g. `MyAssetDirectory`) and asset path (e.g. `data-files/data.bin`):

```bash
pgutil assets metadata get --path=data-files/data.bin --feed=myAssetDirectory
```

Running the command will return metadata of the specified asset:

```bash
Name: example.txt
Size: 4
Type: http://proget.corp.local/endpoints/myAssetDirectory/content/data-files/example.txt
Created: 6/5/2024 3:29:14 PM
Modified: 6/5/2024 3:29:14 PM
Caching: TTL (60 seconds)

-- Hashes --
MD5: 098f6bcd4621d373cade4e832627b4f6
SHA1: a94a8fe5ccb19ba61c4c0873d391e987982fbbd3
SHA256: 9f86d081884c7d659a2feaa0c55ad015a3bf4f1b2b0b822cd15d6c15b0f00a08
SHA512: ee26b0dd4af7e749aa1a8ee3c10ae9923f618980772e473f8819a5d4940e0db27ac185f8a0e1d5f84f88bc887fd67b143732c304cc5fa9ad8e6f57f50028a8ff

-- Custom Metadata --
myKey1: myValue1
myKey2: myValue2
```

## HTTP Request Specification
To get the metadata of an item, simply `GET` to the URL with the `AssetDirectoryName` and path to the item.

```plaintext
GET /endpoints/«AssetDirectoryName»/metadata/«path»
```

## HTTP Response Specification
An `AssetDirectoryItem` (see [AssetDirectoryItem.cs](https://github.com/Inedo/pgutil/blob/thousand/Inedo.ProGet/AssetDirectories/AssetDirectoryItem.cs)) object will be returned on a successful `200` response. A `401` response indicates a [missing, unknown, or unauthorized API Key](/docs/proget/api/assets#authentication).
