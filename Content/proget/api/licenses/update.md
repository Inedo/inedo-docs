---
title: "Update License"
order: 4
---

:::(internal) (⚠ Not Working)
Not working as of ProGet version 2023.22
:::

*Update License* is available as both a `pgutil` command and an HTTP Request, and will update a specified license.

:::(Info) (🚀 Quick Example: Updating a license with pgutil)
This example updates a nuget license with the id `XYZ-1.0`, adding version `1.2.3` of the nuget package `myNugetPackage` as a `PUrl`

```bash
pgutil licenses detection add --code=XYZ-1.0 --type=purl --value=pkg:nuget/myNugetPackage@1.2.3
```
:::

## Command Specification (CLI)
The `licenses detection` command set is used to update a license by adding or removing detection types. There are two commands available:

| Command | Description |
| --- | --- |
| `add` | Adds a `spdx`, `url`, `packagename` or `purl` detection type | 
| `remove`  | Removes a `spdx`, `url`, `packagename` or `purl` detection type` |

The `--code`, `--type`, and `--value` options are always required.

:::(info)(📄 Note: pgutil Sources)
Source options must also be specified unless you have the "Default" source configured. See [Working with Sources](/docs/proget/api/pgutil#sources) to learn more.
:::

**Adding a PUrl detection type to a license** requires the license code (e.g. `ABC-1.0`), the detection type (e.g `purl`), and the value (e.g. `pkg:nuget/myNugetPackage@1.2.3`):

```bash
pgutil licenses detection add --code=ABC-1.0 --type=purl --value=pkg:nuget/myNugetPackage@1.2.3
```

**Removing an SPDX detection type to a license** requires the license code (e.g. `ABC-1.0`), the detection type (e.g `spdx`), and the value (e.g. `MIT`):

```bash
pgutil licenses detection remove --code=ABC-1.0 --type=spdx --value=MIT
```
### Updating License Files

The `licenses files` command set is used to add, remove, or view license files in a license. There are two commands available:

| Command | Description | Required Options |
| --- | --- | --- |
| `add` | Uploads a license file to ProGet | `--code`, `--file` |
| `delete`  | Removes a license file from ProGet | `--hash` |

**Adding a license file to a license** requires the license code (e.g. `ABC-1.0`) and the file (e.g. `C:\documents\license-files\abc-1.0-license-file.txt`):

```bash
pgutil licenses files add --code=ABC-1.0 --file=C:\documents\license-files\abc-1.0-license-file.txt
```

**Deleting a license file** requires the license file's hash (e.g. `00462de3d7b6f3e5551a69ae84344bc69d23c02e1353be3e8445d16f025e523b`):

```bash
pgutil licenses files delete --hash=00462de3d7b6f3e5551a69ae84344bc69d23c02e1353be3e8445d16f025e523b
```

## HTTP Request Specification
To update a license, simply `POST` to the URL with the `license` ID, an [appropriate API Key](/docs/proget/api/licenses#authentication) and a [LicenseInfo.cs](https://github.com/Inedo/pgutil/blob/thousand/Inedo.ProGet/LicenseInfo.cs) object as the request body.

This endpoint supports partial updating by only updating the properties that are supplied in the request. 

:::(info) (📄 Note)
When updating, any properties omitted will keep their existing values. Updating a property with an array value will overwrite the existing value. For example, if a license has `allowedFeeds: ["A", "B"]`, updating with `allowedFeeds: ["C"]` will remove allowedFeeds "A" and "B" from the license, keeping only "C". 

To append values, perform a [List License](/docs/proget/api/licenses/list) first, then append the existing values returned in the "Update License" request. For example, GET the values of allowedFeeds "A" and "B", and add them along with "C", supplying `allowedFeeds: ["A", "B", "C"]`in the update request.
:::

```plaintext
POST /api/licenses/update
```

## HTTP Response Specification
A successful `200` response body will contain an updated [LicenseInfo.cs](https://github.com/Inedo/pgutil/blob/thousand/Inedo.ProGet/LicenseInfo.cs) object. A `403` response indicates a [missing, unknown, or unauthorized API Key](/docs/proget/api/licenses#authentication). A `404` response indicates that the specified `license` does not exist.
