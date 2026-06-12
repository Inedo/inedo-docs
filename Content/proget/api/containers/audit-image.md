---
title: "Audit Image"
order: 1
---

*Audit Image* is available as both a `pgutil` command and an HTTP Request, and will audit a specified image for vulnerabilities and provide a report.

:::(Info) (🚀 Quick Example: Auditing an image with pgutil)
This example will audit the tag `10.1.1-ee.0` of the repo `mycontainer/mycontainer-ee` in the docker feed `myDockerFeed`:

```bash
pgutil containers audit --image=mycontainer/mycontainer-ee:10.1.1-ee.0 --feed=myDockerFeed
```
:::

## Command Specification (CLI)
The `containers audit` command is used to audit an image.

The `--image` and `--feed` options are always required. 

:::(info)(📄 Note: pgutil Sources)
Source options must also be specified unless you have the "Default" source configured, and that a feed may be instead specified in the source. See [Working with Sources](/docs/proget/api/pgutil#sources) to learn more.
:::

**Auditing an image** requires the feed name (e.g. `MyDockerFeed`), repo, and tag (e.g. `mycontainer/mycontainer-ee:10.1.1-ee.0`):

```bash
pgutil containers audit --image=mycontainer/mycontainer-ee:10.1.1-ee.0 --feed=myDockerFeed
```

## HTTP Request Specification
To audit an image, simply `GET` to the URL with the following query arguments:

```plaintext
GET /api/containers/images/audit?feed=<feed name>&tagOrDigest=<full tag or digest>
```

`tagOrDigest` may be a full tag in the `<repo>:<tag>` format, or it may be a full [image digest](https://docs.docker.com/dhi/core-concepts/digests/).


## HTTP Response Specification
An `AuditContainerInfo` object will be returned on a successful `200` response. A `403` response indicates a [missing, unknown, or unauthorized API Key](/docs/proget/api/connectors#authentication).
