---
title: "List Comments"
order: 2
---

*List Comments* is available as both a `pgutil` command and an HTTP Request, and will return an array of [CommentInfo Object](/docs/proget/reference-api/proget-api-sca#commentinfo-object) objects describing all comments in a project release.

Note, that if there are no comments in a release, an empty array is returned.

:::(Info) (🚀 Quick Example: Listing Comments with Curl)
This example lists all comments in release version `1.2.3` of a project `myProject`, authenticating with the API key `abc12345`:

````
curl -X GET -H "X-ApiKey: abc12345" "https://proget.corp.local/api/sca/comments?project=myProject&version=1.2.3"
````
:::

## Command Specification (CLI)
:::(Info) (🚧 Coming Soon 🚧)
The `sca comments list` command is coming soon. Similar to the HTTP Request, it will list all comments in a project release.
:::

## HTTP Request Specification
To list all comments of a release, simply `GET` to the URL with an [appropriate API Key](/docs/proget/reference-api/proget-api-sca#authentication).

```
GET /api/sca/comments?project=«projectName»&version=«releaseVersion»
```

## HTTP Response Specification

A successful (`200`) response body will contain an array of [CommentInfo Object](/docs/proget/reference-api/proget-api-sca#commentinfo-object) objects. For example, to listing all comments in release version `1.2.3` of a project `myProject`, the request would return this:

```json
GET /api/sca/comments?project=myProject&version=1.2.3

[
  {
    "number":1,
    "by":"proget",
    "date":"2024-01-24T02:10:36.993Z",
    "comment":"I am a new comment."
  },
  {
    "number":2,
    "by":"proget",
    "date":"2024-01-24T03:56:51.55Z",
    "comment":"I am a newer comment."
  },
  {
    "number":3,
    "by":"proget",
    "date":"2024-01-24T04:03:38.51Z",
    "comment":"I am the newest comment."},
  {....}
]
```

| Response | Details |
| --- | --- |
| **200 (Success)** | body will contain an array of [CommentInfo](/docs/proget/reference-api/proget-api-sca#commentinfo-object) objects |
| **400 (Invalid Input)** | indicates invalid or missing properties |
|  **403 (Unauthorized API Key)** | indicates a [missing, unknown, or unauthorized API Key](/docs/proget/reference-api/proget-api-sca#authentication); the body will be empty |
|  **404 (Project or Release Not Found)** | indicates that the specified project |
| **500 (Server Error)** | indicates an unexpected error; the body will contain the message and stack trace, and this will also be logged |