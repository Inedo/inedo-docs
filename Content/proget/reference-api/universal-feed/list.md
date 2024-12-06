---
title: "List Universal Packages"
order: 3
---

*List Universal Packages* is an endpoint in [ProGet's Universal Feed API](/docs/proget/reference-api/universal-feed) that will return either [PackageData](/docs/proget/reference-api/universal-feed#package-data) JSON object (if name is specified) or a JSON array of objects, depending on parameters defined.

:::(Info) (🚀 Quick Example: Listing a package with Curl)
This example lists a package `myUniversalPackage`, in the group `myGroup`, from the feed `myUniversalFeed`, authenticating with the API key `abc12345`:

````
curl -X GET --header "X-ApiKey: abc12345" "https://proget.corp.local/upack/myUniversalFeed/packages?group=myGroup&name=myUniversalPackage"
````
:::

### Parameter	Description
| Parameter | Details |
| --- | --- |
| `group` | Optional. If specified without name, returns an array of packages with a matching group name or an empty array. |
| `name` | If specified, returns an object with a matching name and group or a 404 status with an error message in the body. |
| `count` | Optional. If specified, returns an array with at most as many entries as specified; otherwise, at most 1000 packages are returned. This is ignored if name is specified. |

## Request Specification
To list a package, simply `GET` to the URL with one or more parameters.

```
GET /upack/«feed-name»/packages?group=«group»&name=«name»&count=«count»
```

**Listing a package version** requires the feed (e.g. `myUniversalFeed`) and package name (e.g. `myUniversalPackage`):

```
GET /upack/myUniversalFeed/packages?name=myUniversalPackage
```

**Listing all packages with a package count specified** requires the feed (e.g. `myUniversalFeed`) and count (e.g. `50`):

```
GET /upack/myUniversalFeed/packages?count=50
```

Note that the group parameter must be a full match; future versions of this endpoint may allow for a sub-group searching (such as groupName*) if anyone requests.

## Response Specification
A successful (`200`) response body will contain either a [PackageData](/docs/proget/reference-api/universal-feed#package-data) JSON object (if name is specified) or a JSON array of objects. For example:

When querying with no parameters, the request would return:

```json
GET /upack/myUniversalFeed/packages

[
  {
    "group": "MyGroup",
    "name": "MyUniversalPackage",
    "latestVersion": "2.0.4",
    "title": "Test Package",
    "description": "Test Package for API Documentation",
    "downloads": "5",
    "isLocal": true,
    "isCached": false,
    "versions": ["2.0.4", "1.2.3", "1.0.0"]
  },
  { ... } // other packages
]

```

When querying a package by specifying a group (e.g. `MyGroup`) and/or name (e.g. `MyUniversalPackage`), the request returns:

```json
GET /upack/myUniversalFeed/packages?group=myGroup&name=myUniversalPackage

{
  "group": "MyGroup",
  "name": "MyUniversalPackage",
  "latestVersion": "2.0.4",
  "title": "Test Package",
  "description": "Test Package for API Documentation",
  "downloads": "5",
  "isLocal": true,
  "isCached": false,
  "versions": ["2.0.4", "1.2.3", "1.0.0"]
}
```

| Response | Details |
| --- | --- |
| **200 (Success)** | will return either a [PackageData](/docs/proget/reference-api/universal-feed#package-data) JSON object (if name is specified) or a JSON array of objects|
| **403 (Unauthorized API Key)** | indicates a [missing, unknown, or unauthorized API Key](/docs/proget/reference-api/universal-feed#authentication) |
| **404 (Package not Found)** | indicates the specified package does not exist, the body will provide some details as text |