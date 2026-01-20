---
title: "Repackage Package"
order: 8
---

The Repackaging API is available as both a `pgutil` command and an HTTP Request, and provides the programmatic ability to [repackage prerelease packages](/docs/proget/packages/repackaging) by posting JSON-encoded or Form-encoded data.

:::(warning) 
⚠ The Repackaging API is available in paid and trial ProGet editions, and will raise a `500` in free editions.
:::

:::(Info) (🚀 Quick Example: Repackaging a NuGet package with pgutil)
This example will repackage `myNugetPackage` version `4.3.2-rc.1` to `4.3.2` in the feed `myNugetFeed`

```bash
pgutil packages repackage --feed=myNugetFeed --package=myNugetPackage --version=4.3.2-rc.1 --new-version=4.3.2
```
:::

This API requires ProGet 2023.0 or later.

## Command Specification (CLI)
The `packages repackage` command is used to repackage a package to a package with a different version.

The `--feed`, `--package` and `--version` options are always required, and the `--qualifier` option is required for multifile packages like Debian and RubyGems.

:::(info)(📄 Note: pgutil Sources)
Source options must also be specified unless you have the "Default" source configured, and that a feed may be instead specified in the source. See [Working with Sources](/docs/proget/api/pgutil#sources) to learn more.
:::

**Repackaging a NuGet Package** requires the feed (e.g. `myNugetFeed`), name (e.g. `myNugetPackage`) current version (e.g. `1.0.0-rc.1`) and new version (e.g. `1.0.0`):

```bash
pgutil packages repackage --feed=myNugetFeed --package=myNugetPackage --version=1.0.0-rc.1 --new-version=1.0.0
```

**Repackaging an npm Package** requires the feed (e.g. `MyNpmFeed`), name (e.g. `myNpmPackage`), scope (e.g. `myScope`), current version (e.g. `2.0.0-rc.1`) and new version (e.g. `2.0.0`):

```bash
pgutil packages repackage --feed=MyNpmFeed --package=@myScope/myNpmPackage --version=2.0.0-rc.1 --new-version=2.0.0
```

## HTTP Request Specification

To repackage a package, simply `POST` to the following URL with an [appropriate API Key](/docs/proget/api/security#authentication) and a `RepackageInput` object (see [RepackageInput.cs](https://github.com/Inedo/pgutil/blob/thousand/Inedo.ProGet/RepackageInput.cs)) object as the request body.

```json
POST /api/repackaging/repackage
```

You can also POST to the URL with Form-encoded data:

```
POST /api/repackaging/repackage

Content-Type: application/x-www-form-urlencoded

feed=myFeed&packageName=myPackage&version=4.3.2-rc.1&newVersion=4.3.2&comments=This+package+was+repackaged+by+an+automated+process
```

## HTTP Response Specification

A successful `200` creates a new "repackaged" package in the same feed, or`toFeed` when specified. A `403` response indicates a [missing, unknown, or unauthorized API Key](/docs/proget/api/packages#authentication).
