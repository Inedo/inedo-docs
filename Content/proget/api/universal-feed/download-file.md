---
title: "Download Universal Package File"
order: 7
---

*Download Universal Package File* is an endpoint in [ProGet's Universal Feed API](/docs/proget/reference-api/universal-feed) that will download a specific file within a package, so that you don't need to download the entire package.

:::(Info) (🚀 Quick Example: Downloading a file from a Universal Package with Curl)
This example downloads the file `log_data.bin` from version `1.2.3` of a universal package `myUniversalPackage`, in the group `MyGroup`, from the feed `myUniversalFeed`, authenticating with the API key `abc12345`:

````
curl -X GET -H "X-ApiKey: abc12345" "https://proget.corp.local/upack/myUniversalFeed/download-file/MyGroup/myUniversalPackage/1.2.3?path=package/log_data.bin"
````
:::

### Parameter Description
| Parameter | Details |
| --- | --- |
| `group-name` | Optional. If not specified, the empty group will be searched. |
| `package-name` | Required. |
| `package-version` | Optional. |
| `path` | Required. Relative path to the file within the package. Note, package contents are contained within the "package" directory of the package, so most request paths should start with "package" unless files in the package root are desired, such as the upack.json file. If the file is not found within the package, a 404 is returned. |

## Request Specification
To download a file from a **specific** package version, simply `GET` to the URL with a feed name, group name, package name, package version and file path:

```
GET /upack/«feed-name»/download-file/«group-name»/«package-name»/«package-version»?path=«path»
```

To download a file from the **latest** package version, simply `GET` to the URL with a feed name, group name, package name, and file path:

```
GET /upack/«feed-name»/download-file/«group-name»/«package-name»?latest&path=«path»
```

**Downloading a file within a specific version of a universal package** requires the feed (e.g. `myUniversalFeed`),  group name (e.g. `myGroup`), package name (e.g. `myUniversalPackage`), version (e.g. `1.0.0`) and path of the file (e.g.`package/mycontent.bin`):

```
GET /upack/myUniversalFeed/download-file/MyGroup/myUniversalPackage/1.0.0?path=package/mycontent.bin
```

**Downloading a file within the latest version of a universal package** requires the feed (e.g. `myUniversalFeed`),  group name (e.g. `myGroup`), package name (e.g. `myUniversalPackage`), and path of the file (e.g.`package/mycontent.bin`):

```
GET /upack/myUniversalFeed/download-file/MyGroup/myUniversalPackage?latest&path=package/mycontent.bin
```

## Response Specification

| Response | Details |
| --- | --- |
| **200 (Success)** | will successfully download the file
| **400 (Package Version not Found)** | returned if `package-version` is not specified, and latest is not specified in the URL |
|  **403 (Unauthorized API Key)** | indicates a [missing, unknown, or unauthorized API Key](/docs/proget/reference-api/universal-feed#authentication) |
| **404 (Package/File Not Found)** | indicates the specified package or file does not exist |