---
title: "Create Feed"
order: 1
---

*Create Feed* is available as both a `pgutil` command and an HTTP Request, and will create a new feed of the specified package type.

:::(Info) (🚀 Quick Example: Creating a NuGet feed with pgutil)
This example will create a `NuGet` feed `myNugetFeed`:

```bash
pgutil feeds create --name=myNugetFeed --type=NuGet
```
:::

## Command Specification (CLI)
The `feeds create` command is used to create a feed.

The `--name` and `--type` options are always required. 

:::(info)(📄 Note: pgutil Sources)
Source options must also be specified unless you have the "Default" source configured, and that a feed may be instead specified in the source. See [Working with Sources](/docs/proget/api/pgutil#sources) to learn more.
:::

**Creating a feed** requires the feed name (e.g. `MyNugetFeed`) and type (e.g. `NuGet`):

```bash
pgutil feeds create --name=myNugetFeed --type=NuGet
```

## HTTP Request Specification
To create a feed, simply `POST` to the URL with an [appropriate API Key](/docs/proget/api/feeds#authentication) and a `ProgetFeed` (see [ProGetFeed.cs](https://github.com/Inedo/pgutil/blob/thousand/Inedo.ProGet/ProGetFeed.cs)) object as the request body.

```plaintext
POST/PUT /api/management/feeds/create
```

## HTTP Response Specification
A `ProGetFeed` object will be returned on a successful `200` response. A `403` response indicates a [missing, unknown, or unauthorized API Key](/docs/proget/api/security#authentication).
