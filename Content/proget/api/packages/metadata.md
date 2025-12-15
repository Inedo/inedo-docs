---
title: "Show Package Metadata"
order: 1
---

*Show Package Metadata* is available as a `pgutil` command and an HTTP Request, and will display the Metadata of a specified package. 

:::(Info) (🚀 Quick Example: Showing package metadata with pgutil)
This example will display the metadata of the package `Newtonsoft.Json` version `13.0.3` in the feed `public-nuget`

```bash
pgutil packages metadata --package="Newtonsoft.Json" --version=13.0.3 --feed=nuget-approved
```
:::

## Command Specification (CLI)
The `packages metadata` command is used to list the packages in a feed. 

The `--package` and `--version` options are always required. The `--feed` option is required if there is no default feed configured.

**Displaying a package's metadata** requires the package (e.g. `Newtonsoft.Json`) and version (e.g. `13.0.3`):

```bash
pgutil packages metadata --package="Newtonsoft.Json" --version=13.0.3 --feed=nuget-approved
```

Example Output:

```plaintext
Newtonsoft.Json 13.0.3
 Published: 3/8/2023 4:42:54 PM by jsmith
 Status: Remote
 Compliance: Compliant
 License: MIT
```

## HTTP Request Specification
To show a package's metadata, simply `GET` to the URL following with a feed name, [package identifiers](/docs/proget/api/packages#using-multiple-parameters), and an [appropriate API Key](/docs/proget/api/packages#authentication).

```plaintext
GET /api/packages/«feed-name»/metadata?«package-identifiers»
```

Unless you use a `purl`, the parameters required will vary by feedtype.

:::(info) (📄 Note)
The package identifier you use must resolve to a single package, otherwise a `400` will be returned.
:::

## HTTP Response Specification
A `PackageMetadata` (see [PackageMetadata.cs](https://github.com/Inedo/pgutil/blob/thousand/Inedo.ProGet/PackageMetadata.cs)) object will be returned on a successful `200` response. A `403` response indicates a [missing, unknown, or unauthorized API Key](/docs/proget/api/packages#authentication).