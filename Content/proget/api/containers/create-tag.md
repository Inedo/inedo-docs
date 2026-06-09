---
title: "Create Tag"
order: 1
---

## HTTP Request Specification
To create a tag, simply `POST` to the URL with the following query arguments:

```plaintext
POST /api/containers/tags/create?feed=<feed name>&repo=<repo name>&target=<target tag or digest>&name=<new tag name>[&force=true]
```

If `force` is specified and the user has the `Feeds_OverwritePackage` permission, then an existing tag can be updated to a new target.

## HTTP Response Specification
If a new tag was created, a successful `201` response is returned along with the content text `Created`. If the tag already exists with the specified target, a successful `200` response is returned with the content text `Exists`. If the tag already exists and was overwritten with a new target, a successful `200` response is returned with the content text `Updated`.

A `409` response indicates that the tag already exists with a different target, and the `force=true` argument was not specified.

A `403` response indicates a missing, unknown, or unauthorized API Key.
