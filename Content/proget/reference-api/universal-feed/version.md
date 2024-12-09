---
title: "List Universal Package Version"
order: 9
---

*List Universal Package Versions* is an endpoint in [ProGet's Universal Feed API](/docs/proget/reference-api/universal-feed) that will return [VersionData](/docs/proget/reference-api/universal-feed#version-data) JSON object (if version is specified) or a JSON array of objects, depending on parameters defined.

:::(Info) (🚀 Quick Example: Listing versions of a Universal Package with Curl)
This example lists the versions of a universal package `myUniversalPackage`, in the group `MyGroup`, from the feed `myUniversalFeed`, authenticating with the API key `abc12345`:

````
curl -X GET --header "X-ApiKey: abc12345" "https://proget.corp.local/upack/myUniversalFeed/versions?name=myUniversalPackage"

````
:::

### Parameter	Description
| Parameter | Details |
| --- | --- |
| `group` | Optional. If specified without version, returns an array of packages with a matching group name or an empty array. |
| `name` | Optional. If specified without version, returns an array of packages with a matching group name or an empty array. |
| `version` | Optional. If specified, returns an object with a matching group, name, and version, or a 404 status with an error message in the body. |
| `includeFileList` | Optional. If true, then inspects each package returned and includes a list of files in the body. |
| `count` | Optional. If specified, returns an array with at most as many entries as specified; otherwise, at most 1000 packages are returned. This is ignored if name is specified. |

## Request Specification
To list a package version, simply `GET` to the URL with a feed name, and one or more parameters.

```
GET /upack/«feed-name»/versions?group=«group»&name=«name»&version=«version»&includeFileList=«includeFileList»&count=«count»
```

**Listing a universal package version** requires the feed (e.g. `myUniversalFeed`), package name (e.g. `myUniversalPackage`) and package version (e.g. `1.0.0`):


```
GET /upack/myUniversalFeed/versions?name=myUniversalPackage&version=1.0.0
```

Note that specifying the name parameter will cause an object to be returned instead of an array; if you don't specify a group, then packages in the empty group will be searched

**Listing all versions of a universal package with a package count specified** requires the feed (e.g. `myUniversalFeed`),name (e.g. `myUniversalPackage`), and count (e.g. `50`):

```
GET /upack/myUniversalFeed/versions?name=myUniversalPackage&count=50
```

**Listing packages with the list of files** requires the feed (e.g. `myUniversalFeed`), package name (e.g. `myUniversalPackage`) and package version (e.g. `1.0.0`):

```
GET /upack/myUniversalFeed/versions?name=myUniversalPackage&version=1.0.0&includeFileList=true
```

Note that the group parameter must be a full match; future versions of this endpoint may allow for a sub-group searching (such as groupName*), if anyone requests.

## Response Specification
A successful (`200`) response body will contain either [VersionData](/docs/proget/reference-api/universal-feed#version-data) JSON object (if version is specified) or a JSON array of objects. For example:

To querying a package with the name `myUniversalPackage` with version `1.2.3`, in a feed `myUniversalFeed`, the request would return:

```
GET /upack/myUniversalFeed/versions?name=myUniversalPackage&version=1.2.3
```
```json
{
  "isLocal": true,
  "isCached": false,
  "isVirtual": false,
  "sha1": "00c1624a6bc2fbed3a8b49c9c90fa4f2b4a1241e",
  "title": "Test Package",
  "description": "Test Package for API Documentation",
  "group": "MyGroup",
  "name": "MyUniversalPackage",
  "version": "1.2.3",
  "downloads": 6,
  "published": "2023-12-11T02:13:47.86Z",
  "size": 1435884
}

```

To query a package with the name `myUniversalPackage`, in a feed `myUniversalFeed`, the request would return:

```
GET /upack/myUniversalFeed/versions?name=myUniversalPackage
```
```json
[
  {
    "isLocal": true,
    "isCached": false,
    "isVirtual": false,
    "sha1": "00c1624a6bc2fbed3a8b49c9c90fa4f2b4a1241e",
    "title": "Test Package",
    "description": "Test Package for API Documentation",
    "group": "MyGroup",
    "name": "MyUniversalPackage",
    "version": "1.0.0",
    "downloads": 6,
    "published": "2023-12-11T02:13:47.86Z",
    "size": 1435884
  },
  { ...  } // other package versions
]

```

To query a package with the name `myUniversalPackage` with version `1.2.3`, in a feed `myUniversalFeed`, with the list of files included, the request would return:

```
GET /upack/myUniversalFeed/versions?name=myUniversalPackage&version=1.2.3&includeFileList=true
```
```json
{
  "isLocal": true,
  "isCached": false,
  "isVirtual": false,
  "sha1": "00c1624a6bc2fbed3a8b49c9c90fa4f2b4a1241e",
  "title": "Test Package",
  "description": "Test Package for API Documentation",
  "group": "MyGroup",
  "name": "MyUniversalPackage",
  "version": "1.2.3",
  "downloads": 6,
  "published": "2023-12-11T02:13:47.86Z",
  "size": 1435884,
  "fileList": [
    {
        "name": "example.bin",
        "size": 1543944,
        "date": "2023-12-08T10:25:50Z"
    },
    { ... } // other files within package
  ]
}
```

| Response | Details |
| --- | --- |
| **200 (Success)** | will return either a [VersionData](/docs/proget/reference-api/universal-feed#version-data) JSON object (if version is specified) or a JSON array of objects |
| **403 (Unauthorized API Key)** | indicates a [missing, unknown, or unauthorized API Key](/docs/proget/reference-api/universal-feed#authentication) |
| **404 (Version not Found)** | indicates the specified version does not exist, the body will provide some details as text |