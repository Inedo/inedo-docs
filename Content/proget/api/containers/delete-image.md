---
title: "Delete Image"
order: 3
---

## HTTP Request Specification
To delete an image, simply `POST` or `DELETE` to the URL with the following query arguments:

```plaintext
POST/DELETE /api/containers/images/delete?feed=<feed name>&repo=<repo name>&digest=<digest>
```

Note that `<digest>` must be the **full** [image digest](https://docs.docker.com/dhi/core-concepts/digests/) and not a shortened version.

## HTTP Response Specification
If the image was deleted, a successful `200` response is returned.

A `404` response indicates that the image did not exist.

A `403` response indicates a missing, unknown, or unauthorized API Key.
