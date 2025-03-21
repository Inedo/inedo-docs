---
title: "Repackage Package"
order: 7
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

The `--package` and `--version` options are always required, and the `--qualifier` option is required for multifile packages like Debian and RubyGems.

**Repackaging a NuGet Package** requires the feed (e.g. `myNugetFeed`), name (e.g. `myNugetPackage`) current version (e.g. `1.0.0-rc.1`) and new version (e.g. `1.0.0`):

```bash
pgutil packages repackage --feed=myNugetFeed --package=myNugetPackage --version=1.0.0-rc.1 --new-version=1.0.0
```

**Repackaging an npm Package** requires the feed (e.g. `MyNpmFeed`), name (e.g. `myNpmPackage`), scope (e.g. `myScope`), current version (e.g. `2.0.0-rc.1`) and new version (e.g. `2.0.0`):

```bash
pgutil packages repackage --feed=MyNpmFeed --package=@myScope/myNpmPackage --version=2.0.0-rc.1 --new-version=2.0.0
```

Note source options must also be specified unless you have the "Default" source configured, and that a feed may be instead specified in the source. See [Working with Sources](/docs/proget/reference-api/proget-pgutil#sources) to learn more.

## HTTP Request Specification

To promote a package, simply `POST` to the URL with an [appropriate API Key](/docs/proget/reference-api/proget-api-packages#authentication) and JSON object as the body with the following properties:

| Property |  Description
| --- | ---
| `feed`* | Name of the feed where the target package is located
| `group` | Group-portion of the package's name; this is only applicable for Universal Packages
| `name`* | Name of the target package
| `version`* | Version of the target package
| `newVersion`* | Version of the new package to create
| `comments` | Comments to append to repackaging audit history
| `toFeed` | When specified, promotes the package to the specified feed instead of keeping it in `feed`.

*\* required property.*

Previous versions of ProGet used `groupName` and `packageName` instead of `group` and `name`; these may still be used, but aren't recommended.

As JSON-encoded data:

```json
POST /api/repackaging/repackage

Content-Type: application/json

{
  "feed": "myFeed",
  "name": "myPackage",
  "version": "4.3.2-rc.1",
  "newVersion": "4.3.2",
  "comments": "This package was repackaged by an automated process."
}
```

As Form-encoded data:

```
POST /api/repackaging/repackage

Content-Type: application/x-www-form-urlencoded

feed=myFeed&packageName=myPackage&version=4.3.2-rc.1&newVersion=4.3.2&comments=This+package+was+repackaged+by+an+automated+process
```

## HTTP Response Specification

| Response | Details |
| --- | --- |
| **200 (Success)** | creates a new package in the same feed, or`toFeed` when specified. |
| **400 (Invalid Input)** | indicates invalid or missing properties on the package; the body will provide some details as text |
|  **403 (Unauthorized API Key)** | indicates a [missing, unknown, or unauthorized API Key](/docs/proget/reference-api/proget-api-packages#authentication) |
| **500 (Server Error)** | indicates an unexpected error; the body will contain the message and stack trace, and this will also be logged |



