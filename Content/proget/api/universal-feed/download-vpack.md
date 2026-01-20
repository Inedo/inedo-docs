---
title: "Download Virtual Package Manifest"
order: 6
---

*Download Virtual Package Manifest* is available as an HTTP Request, and will download the `package.version.vpack` manifest file for a virtual package, or throw error if not a virtual package.

:::(Info) (🚀 Quick Example: Downloading a Virtual Package with Curl)
This example downloads version `1.2.3` of a universal package `myUniversalPackage` manifest, in the group `MyGroup`, from the feed `myUniversalFeed`, to `C:\ProGet\MyPackage-1.2.3.vpack`, 

````
curl -X GET -H "X-ApiKey: abc12345" "https://proget.corp.local/upack/myUniversalFeed/download-vpack/MyGroup/myUniversalPackage/1.2.3" -o "C:\ProGet\MyPackage-1.2.3.vpack"
````
:::

### Parameter	Description
| Parameter | Details |
| --- | --- |
| `group-name` | Optional. If not specified, the empty group will be searched. |
| `package-name` | Required. |
| `package-version` | Optional. |

## HTTP Request Specification
To download a **specific** virtual package manifest version, simply `GET` to the URL with a feed name, group name, package name and package version:

```
GET /upack/«feed-name»/download-vpack/«group-name»/«package-name»/«package-version»
```

To download the **latest** version of a virtual package manifest, simply `GET` to the URL with a feed name, group name and package name:

```
GET /upack/«feed-name»/download-vpack/«group-name»/«package-name»?latest
```

**Downloading a specific version of a virtual package manifest** requires the feed (e.g. `myUniversalFeed`),  group name (e.g. `myGroup`), package name (e.g. `myVirtualPackage`) and version (e.g. `1.2.3`):

```
GET /upack/myUniversalFeed/download-vpack/myGroup/myVirtualPackage/1.2.3
```

**Downloading the latest version of a virtual package manifest** requires the feed (e.g. `myUniversalFeed`),  group name (e.g. `myGroup`), and package name (e.g. `myVirtualPackage`):

```
GET /upack/myUniversalFeed/download-vpack/myGroup/myVirtualPackage?latest
```

## HTTP Response Specification
The virtual package will be downloaded on a successful `200` response. A `403` response indicates a [missing, unknown, or unauthorized API Key](/docs/proget/api/universal-feed#authentication). A `404` response indicates that the specified virtual package does not exist.
