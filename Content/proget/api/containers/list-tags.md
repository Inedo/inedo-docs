---
title: "List Tags"
order: 4
---

*List Tags* is available as both a `pgutil` command and an HTTP Request, and will create an alternative tag for a specified repository.

:::(Info) (🚀 Quick Example: Listing tags with pgutil)
This example will list the tags for the repo `mycontainer/mycontainer-ee` in the docker feed `myDockerFeed`:

```bash
pgutil containers tags list --repo=mycontainer/mycontainer-ee --feed=myDockerFeed
```
:::

## Command Specification (CLI)
The `containers tags list` command is used to list tags for a repository.

The `--repo` and `--feed` options are always required. 

:::(info)(📄 Note: pgutil Sources)
Source options must also be specified unless you have the "Default" source configured, and that a feed may be instead specified in the source. See [Working with Sources](/docs/proget/api/pgutil#sources) to learn more.
:::

**Listing tags** requires the feed name (e.g. `MyDockerFeed`) and repository:

```bash
pgutil containers tags list --repo=mycontainer/mycontainer-ee --feed=myDockerFeed
```

## HTTP Request Specification
To list tags, simply `POST` to the URL with the following query arguments:

```plaintext
POST /api/containers/tags/list?feed=<feed name>&repo=<repo name>
```

## HTTP Response Specification
A successful (`200`) response body will contain an array of `DockerTagInfo` (see [DockerTagInfo.cs](https://github.com/Inedo/pgutil/blob/thousand/Inedo.ProGet/DockerTagInfo.cs)) objects for each tag on a Docker repository.

A `400` response indicates a missing repo parameter.

A `403` response indicates a missing, unknown, or unauthorized API Key.
