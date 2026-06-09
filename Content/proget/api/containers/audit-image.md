---
title: "Audit Image"
order: 1
---

## HTTP Request Specification
To audit an image, simply `GET` to the URL with the following query arguments:

```plaintext
GET /api/containers/images/audit?feed=<feed name>&tagOrDigest=<full tag or digest>
```

`tagOrDigest` may be a full tag in the `<repo>:<tag>` format, or it may be a full [image digest](https://docs.docker.com/dhi/core-concepts/digests/).


## HTTP Response Specification
An `AuditContainerInfo` object will be returned on a successful `200` response. A `403` response indicates a [missing, unknown, or unauthorized API Key](/docs/proget/api/connectors#authentication).
