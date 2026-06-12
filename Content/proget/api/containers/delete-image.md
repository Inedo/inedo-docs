---
title: "Delete Image"
order: 3
---

*Delete Image* is available as both a `pgutil` command and an HTTP Request, and will delete a specified image.

:::(Info) (🚀 Quick Example: Deleting an image with pgutil)
This example will delete an image with the digest `sha256:80769751fe5806f4e4262b0d12f2c397ea965cf3ac6ffbe6562dd57054025b2d` and repo `mycontainer/mycontainer-ee` from the docker feed `myDockerFeed`:

```bash
pgutil containers images delete --repo=gitlab/gitlab-ee --digest=sha256:80769751fe5806f4e4262b0d12f2c397ea965cf3ac6ffbe6562dd57054025b2d --feed=MyDockerFeed
```
:::

## Command Specification (CLI)
The `containers tags delete` command is used to delete an image.

The `--repo`, `digest`, and `--feed` options are always required. 

:::(info)(📄 Note: pgutil Sources)
Source options must also be specified unless you have the "Default" source configured, and that a feed may be instead specified in the source. See [Working with Sources](/docs/proget/api/pgutil#sources) to learn more.
:::

**Deleting an image** requires the feed name (e.g. `MyDockerFeed`), repo (e.g. `mycontainer/mycontainer-ee`), and the digest (e.g. `sha256:80769751fe5806f4e4262b0d12f2c397ea965cf3ac6ffbe6562dd57054025b2d`):

```bash
pgutil containers images delete --repo=gitlab/gitlab-ee --digest=sha256:80769751fe5806f4e4262b0d12f2c397ea965cf3ac6ffbe6562dd57054025b2d --feed=MyDockerFeed
```

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
