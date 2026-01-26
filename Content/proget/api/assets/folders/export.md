---
title: "Export Asset Folder"
order: 4
---

*Export Asset Folder* is available as both a `pgutil` command and an HTTP Request, and will export the contents of the specified folder as either a `ZIP` or a `TGZ` archive.

:::(Info) (🚀 Quick Example: Exporting a folder with pgutil)
This example will export a folder `project-data` in the asset directory `myAssetDirectory` to the local path and archive file `C:\Inedo\data-export.zip`

```bash
pgutil assets folders export --file=C:\Inedo\data-export.zip --feed=myAssetDirectory --path=project-data
```
:::

## Command Specification (CLI)
The `assets folders export` command is used to export the contents of the specified folder as either a `ZIP` or a `TGZ` archive.

The `--file` and `--path` options are always required. The `--feed` option is required if there is no default feed configured.

:::(info)(📄 Note: pgutil Sources)
Source options must also be specified unless you have the "Default" source configured, and that a feed may be instead specified in the source. See [Working with Sources](/docs/proget/api/pgutil#sources) to learn more.
:::

**Exporting a folder** requires the asset directory (e.g. `MyAssetDirectory`), the folder to be exported (e.g. `MyFolder`) and the output archive file (e.g. `C:\Inedo\MyArchive.zip`):

```plaintext
pgutil assets folders export --file=C:\Inedo\MyArchive.zip --feed=myAssetDirectory --path=MyFolder
```

* The option `--overwrite` can be used to overwrite the output archive file if it already exists

## HTTP Request Specification
To export an asset directory or folder, simply `GET` to the URL with the `AssetDirectoryName`, path to the folder and the format.

```plaintext
GET /endpoints/«AssetDirectoryName»/export/«path_to_folder»?format=«zip/tgz»&recursive=«true/false»
```

The `format` argument may be either `zip` (for a zip file) or `tgz` (for a GZipped tar file). When `recursive` is `false` or not specified, only items contained directly in the specified path are included. When `recursive` is `true`, the archive will contain all subfolders as well. 

## HTTP Response Specification
A successful `200` response body will return download details and the contents of the asset directory or folder as either `zip` or `tgz`. A `401` response indicates a [missing, unknown, or unauthorized API Key](/docs/proget/api/assets#authentication).
