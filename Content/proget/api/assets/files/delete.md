---
title: "Delete Asset File"
order: 3
---

*Delete Asset File* is available as both a `pgutil` command and an HTTP Request, and will delete a file at the specified path. Note that the path must refer to an individual file (not a folder), and it's *not* considered an error to delete a file that does not exist. 

:::(Info) (🚀 Quick Example: Deleting a file with pgutil)
This example will delete a file `old-file.txt` located in the folder `test-files` of the asset directory `myAssetDirectory`:

```bash
pgutil assets delete --feed=myAssetDirectory --path=test-files/old-file.txt
```
:::

## Command Specification (CLI)
The `assets delete` command is used to delete a file in the asset directory.

The `--path` options is always required. The `--feed` option is required if there is no default feed configured.

**Deleting a file** requires the asset directory (e.g. `MyAssetDirectory`) and the path of the file (e.g. `test-files/old-file.txt`):

```bash
pgutil assets delete --feed=myAssetDirectory --path=test-files/old-file.txt
```

## HTTP Request Specification
To delete a file, simply `DELETE` to the URL with the `AssetDirectoryName` and path to the file.

```plaintext
DELETE /endpoints/«AssetDirectoryName»/content/«path_to_file»
```

## HTTP Response Specification

A successful `200` response will delete the file from the asset directory. A `401` response indicates a [missing, unknown, or unauthorized API Key](/docs/proget/api/security#authentication).