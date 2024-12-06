---
title: "Universal Feed Metadata"
order: 8
---

*Universal Feed Metadata* is an endpoint in [ProGet's Universal Feed API](/docs/proget/reference-api/universal-feed) that will return a [FeedMetadata](/docs/proget/reference-api/universal-feed#feed-metadata) JSON object that describes the feed.

:::(Info) (🚀 Quick Example: Returning metadata of a Universal Package feed with Curl)
This example returns the metadata of a universal package feed `myUniversalFeed`,authenticating with the API key `abc12345`:

````
curl -X GET --header "X-ApiKey: abc12345" "https://proget.corp.local/upack/myUniversalFeed/meta"
````
:::
## Request Specification
To query the properties of a feed, simply `GET` to the URL with a feed name.

```
GET /upack/«feed-name»/meta
```

**Querying a Universal Feed** requires the feed (e.g. `myUniversalFeed`).

```
GET /upack/myUniversalFeed/meta
```

## Response Specification
A successful (`200`) response body will contain a single [FeedMetadata](/docs/proget/reference-api/universal-feed#feed-metadata) JSON object. For example, to querying a feed with the name `myUniversalFeed`, the request would return:

```json
GET /upack/myUniversalFeed/meta

{
  "apiVersion": "1.3.0",
  "name": "myUniversalFeed",
  "description": "A Universal Feed",
  "packageCount": 4,
  "packageVersionCount": 5,
  "services": [
    "VirtualPackages",
    "RemotePackages",
    "UploadPackage",
    "UploadContents",
    "Delete",
    "DownloadContents"
  ]
}
```

| Response | Details |
| --- | --- |
| **200 (Success)** | will return a single [FeedMetadata](/docs/proget/reference-api/universal-feed#feed-metadata) JSON object
| **400 (Version < 1.3.0)** | indicates the feed version is less than `1.3.0`. |
|  **403 (Unauthorized API Key)** | indicates a [missing, unknown, or unauthorized API Key](/docs/proget/reference-api/universal-feed#authentication) |
| **404 (Feed Not Found)** | indicates the specified feed does not exist |