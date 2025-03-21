---
title: "List Asset Folders"
order: 2
---

*List Asset Folders* is available as both a `pgutil` command and an HTTP Request, and will list Asset "items" (files and folders) in the specified path.

:::(Info) (🚀 Quick Example: Listing Asset Files and Folders with pgutil)
This example will list the files in the folder `production-files` in the asset directory `myAssetDirectory`:

```bash
pgutil assets list --feed=myAssetDirectory --path=production-files
```
:::

## Command Specification (CLI)
The `assets list` command is used to list files and folders in an asset directory.

The `--feed` option is always required, and the `--path` option is required for folders within the asset directory.

**Listing the contents of the root folder of the asset directory** requires the asset directory (e.g. `myAssetDirectory`):

```bash
pgutil assets list --feed=myAssetDirectory
```

**Listing the contents of a folder of the asset directory** requires the asset directory (e.g. `myAssetDirectory`) and the folder (e.g. `test-files`):

```bash
pgutil assets list --feed=myAssetDirectory --path=test-files
```

## HTTP Request Specification
To list item data, simply `GET` to the URL with the `AssetDirectoryName` and path to the folder. 

```plaintext
GET /endpoints/«AssetDirectoryName»/dir/«path-to-folder»[?recursive=«true/false»]
```

* When `recursive` is `false` or not specified, only data of items directly contained in the specified path are returned. When `recursive` is `true`, data of all items within the folders of the specified path are also returned. 

* Omitting the `«path_to_folder»` will return items located in the root folder of the asset directory.

## HTTP Response Specification
A successful (`200`) response body will contain a JSON array of [AssetDirectoryItem](/docs/proget/api/assets#item-data) objects describing files and folders in the specified path.

When listing the `internal-files` directory and omitting the `«path-to-folder»` and `recursive` argument, only the items in the root folder of the asset directory will be returned:

```json
GET /endpoints/internal-files/dir

[
  {
    "name": "text-file-1.txt",
    "size": 4,
    "type": "multipart/form-data; boundary=---------------------8dbf4d26a9319e6",
    "content": "http://54.168.224.223:8624/endpoints/public-files/content/test.txt",
    "md5": "098f6bcd4621d373cade4e832627b4f6",
    "sha1": "a94a8fe5ccb19ba61c4c0873d391e987982fbbd3",
    "sha256": "9f86d081884c7d659a2feaa0c55ad015a3bf4f1b2b0b822cd15d6c15b0f00a08",
    "sha512": "ee26b0dd4af7e749aa1a8ee3c10ae9923f618980772e473f8819a5d4940e0db27ac185f8a0e1d5f84f88bc887fd67b143732c304cc5fa9ad8e6f57f50028a8ff",
    "created": "2023-12-04T05:18:52.87Z",
    "modified": "2023-12-04T05:18:52.87Z",
    "cacheHeader": {
      "type": "Inherit"
    }
  },
  {
    "name": "text-file-2.txt",
    "size": 24,
    "type": "text/plain",
    "content": "http://54.168.224.223:8624/endpoints/public-files/content/test2.txt",
    "md5": "ea4f8c77deb125e1f77b8f8441f0adc5",
    "sha1": "2d0d84c3b4fd22248029cbc9065eece95251cdd9",
    "sha256": "ea3ba9b43925c4cd3b626b2666fd842ee3b17af53ef3785ffdb195f65c7b2fa3",
    "sha512": "5a92efa218b3815f38324d78657611ea39cbe6a3685eabcd5971904e2910a180037e9fcd17fbb21ff6632a0884de3109b3ef17580119ccba81f142a44ba66d62",
    "created": "2023-12-04T05:18:53.493Z",
    "modified": "2023-12-04T05:18:53.493Z",
    "cacheHeader": {
      "type": "Inherit"
    }
  },
  {
    "name": "subfolder",
    "type": "dir",
    "created": "2023-12-04T05:19:00.19Z",
    "modified": "2023-12-04T05:19:00.19Z",
    "cacheHeader": {
      "type": "Inherit"
    }
  }
]
```

When listing the `internal-files` directory, setting recursive to `true` and omitting the `«path_to_folder»`, lists items all data of items within the folders located in the root folder of the asset directory:

```json
GET /endpoints/internal-files/dir/
[
  {
    "name": "test.txt",
    "size": 4,
    "type": "multipart/form-data; boundary=---------------------8dbf4d26a9319e6",
    "content": "http://54.168.224.223:8624/endpoints/public-files/content/test.txt",
    "md5": "098f6bcd4621d373cade4e832627b4f6",
    "sha1": "a94a8fe5ccb19ba61c4c0873d391e987982fbbd3",
    "sha256": "9f86d081884c7d659a2feaa0c55ad015a3bf4f1b2b0b822cd15d6c15b0f00a08",
    "sha512": "ee26b0dd4af7e749aa1a8ee3c10ae9923f618980772e473f8819a5d4940e0db27ac185f8a0e1d5f84f88bc887fd67b143732c304cc5fa9ad8e6f57f50028a8ff",
    "created": "2023-12-04T05:18:52.87Z",
    "modified": "2023-12-04T05:18:52.87Z",
    "cacheHeader": {
      "type": "Inherit"
    }
  },
  {
    "name": "test2.txt",
    "size": 24,
    "type": "text/plain",
    "content": "http://54.168.224.223:8624/endpoints/public-files/content/test2.txt",
    "md5": "ea4f8c77deb125e1f77b8f8441f0adc5",
    "sha1": "2d0d84c3b4fd22248029cbc9065eece95251cdd9",
    "sha256": "ea3ba9b43925c4cd3b626b2666fd842ee3b17af53ef3785ffdb195f65c7b2fa3",
    "sha512": "5a92efa218b3815f38324d78657611ea39cbe6a3685eabcd5971904e2910a180037e9fcd17fbb21ff6632a0884de3109b3ef17580119ccba81f142a44ba66d62",
    "created": "2023-12-04T05:18:53.493Z",
    "modified": "2023-12-04T05:18:53.493Z",
    "cacheHeader": {
      "type": "Inherit"
    }
  },
  {
    "name": "subfolder",
    "type": "dir",
    "created": "2023-12-04T05:19:00.19Z",
    "modified": "2023-12-04T05:19:00.19Z",
    "cacheHeader": {
      "type": "Inherit"
    }
  },
  {
    "name": "video.mp4",
    "parent": "subfolder",
    "size": 1543944,
    "type": "video/mp4",
    "content": "http://54.168.224.223:8624/endpoints/public-files/content/subfolder/Intro-1080p.mp4",
    "md5": "a4d8624717898c2a433e3f7583399502",
    "sha1": "9780db46f39faf9a4942203bdefa36ad081de815",
    "sha256": "379fd916c601e92bfd879bf189f5803f0c664728734ce4c7b07ed34235821b93",
    "sha512": "5ad1cf30cb083485426f1edcb6460ddcd88aaed550fc426dc944956ccfa19736f234cb7a94017ab60f92372f18898f9f24735a56ea5d03c549881e365f2e898c",
    "created": "2023-12-04T05:19:13.4Z",
    "modified": "2023-12-04T05:19:13.4Z",
    "cacheHeader": {
      "type": "Inherit"
    }
  },
  {
    "name": "image.png",
    "parent": "subfolder",
    "size": 60318,
    "type": "image/png",
    "content": "http://54.168.224.223:8624/endpoints/public-files/content/subfolder/Inedo-logo.png",
    "md5": "6b14f835c7cb26bfb0dc181e77397b83",
    "sha1": "7bf4fcd15227d1fa95011fbb09443c8df76f9cc0",
    "sha256": "c97a85ad28a93810da4cda508628a1031d3232c796cecdf107297e26f79b050d",
    "sha512": "026a691da5dab19bf0cd140d652f3b90de5bd2abfb8e5967a56145fa41c365d79e81cf854f292aa0e57931d3a93cd1f37ae6755a08ec2a8a8b81c80082d59d85",
    "created": "2023-12-04T05:19:13.373Z",
    "modified": "2023-12-04T05:19:13.373Z",
    "cacheHeader": {
      "type": "Inherit"
    }
  }
]
```

Note that querying a nonexistent path (i.e. a folder that does not exist) will not return a `404`, but will instead return an empty array:

```plaintext
GET /endpoints/internal-files/dir/does/not/exist
[]
```

To check if a folder exists, you could either use the [Get Metadata](/docs/proget/api/assets/metadata/get) endpoint or inspect the array returned in `/endpoints/internal-files/dir/does/not` to see if the desired folder exists.

| Response | Details |
| --- | --- |
| **201 (Success)** | the body will contain a JSON array of [AssetDirectoryItem](/docs/proget/api/assets#item-data) objects |
| **401 (Authentication Required)** |  indicates a [missing, unknown, or unauthorized API Key](/docs/proget/api/assets#authentication) |
| **404 (Asset Directory Not Found)** | indicates that the asset directory does not exist |