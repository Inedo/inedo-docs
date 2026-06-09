---
title: "Delete Tag"
order: 2
---

## HTTP Request Specification
To delete a tag, simply `POST` or `DELETE` to the URL with the following query arguments:

```plaintext
POST/DELETE /api/containers/tags/delete?feed=<feed name>&repo=<repo name>&tag=<tag>
```

## HTTP Response Specification
If the tag was deleted, a successful `200` response is returned.

A `404` response indicates that the tag did not exist.

A `403` response indicates a missing, unknown, or unauthorized API Key.
