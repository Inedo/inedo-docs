---
title: "Create Tag"
order: 1
---

*Create Tag* is available as both a `pgutil` command and an HTTP Request, and will create an alternative tag for a specified image.

:::(Info) (🚀 Quick Example: Creating a tag with pgutil)
This example will add the tag `final-10` to the repo `mycontainer/mycontainer-ee` with the tag `10.1.1-ee.0` in the docker feed `myDockerFeed`:

```bash
pgutil containers tags add --repo=mycontainer/mycontainer-ee --target=10.1.1-ee.0 --name=final-10 --feed=myDockerFeed
```
:::

## Command Specification (CLI)
The `containers tags add` command is used to create an alternative tag.

The `--repo`, `--target`, `--name`, and `--feed` options are always required. 

:::(info)(📄 Note: pgutil Sources)
Source options must also be specified unless you have the "Default" source configured, and that a feed may be instead specified in the source. See [Working with Sources](/docs/proget/api/pgutil#sources) to learn more.
:::

**Creating a tag** requires the feed name (e.g. `MyDockerFeed`), repo and tag (e.g. `mycontainer/mycontainer-ee:10.1.1-ee.0`), and the name of the new tag (e.g. `final-10`):

```bash
pgutil containers tags add --repo=mycontainer/mycontainer-ee --target=10.1.1-ee.0 --name=final-10
```

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
