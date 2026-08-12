---
title: "Delete Tag"
order: 2
---

*Delete Tag* is available as both a `pgutil` command and an HTTP Request, and will delete a specified tag from an image.

:::(Info) (🚀 Quick Example: Deleting a tag with pgutil)
This example will delete the tag `final-10` from the repo `mycontainer/mycontainer-ee` with the tag `10.1.1-ee.0` in the docker feed `myDockerFeed`:

```bash
pgutil containers tags delete --repo=mycontainer/mycontainer-ee --tag=final-10 --feed=MyDockerFeed
```
:::

## Command Specification (CLI)
The `containers tags delete` command is used to delete a tag.

The `--repo`, `tag`, and `--feed` options are always required. 

:::(info)(📄 Note: pgutil Sources)
Source options must also be specified unless you have the "Default" source configured, and that a feed may be instead specified in the source. See [Working with Sources](/docs/proget/api/pgutil#sources) to learn more.
:::

**Deleting a tag** requires the feed name (e.g. `MyDockerFeed`), repo (e.g. `mycontainer/mycontainer-ee`), and the tag (e.g. `final-10`):

```bash
pgutil containers tags delete --repo=mycontainer/mycontainer-ee --tag=final-10 --feed=MyDockerFeed
```

## HTTP Request Specification
To delete a tag, simply `POST` or `DELETE` to the URL with the following query arguments:

```plaintext
POST/DELETE /api/containers/tags/delete?feed=<feed name>&repo=<repo name>&tag=<tag>
```

## HTTP Response Specification
If the tag was deleted, a successful `200` response is returned.

A `404` response indicates that the tag did not exist.

A `403` response indicates a missing, unknown, or unauthorized API Key.
