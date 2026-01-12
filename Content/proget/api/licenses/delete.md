---
title: "Delete License"
order: 5
---

*Delete License* is available as both a pgutil command and an HTTP Request, and will delete a specified [LicenseInfo.cs](https://github.com/Inedo/pgutil/blob/thousand/Inedo.ProGet/LicenseInfo.cs) object.

:::(Info) (🚀 Quick Example: Deleting a license with pgutil)
This example will Delete the license `ABC License 1.0` with the ID `ABC-1.0`:

```bash
pgutil licenses delete --code=ABC-1.0
```
:::

## Command Specification (CLI)
The `licenses delete` command is used to delete a license.

The `--code` option is always required.

**Deleting a license** requires the license code (e.g. `ABC-1.0`):

```bash
pgutil licenses delete --code=ABC-1.0
```

## HTTP Request Specification
To delete a specified license simply `DELETE` to the URL with the `license` id and an [appropriate API Key](/docs/proget/api/licenses#authentication).

```bash
POST /api/licenses/delete?code=«license-id»
```

## HTTP Response Specification
A successful `200` response indicates that the specified license has been deleted. A `403` response indicates a [missing, unknown, or unauthorized API Key](/docs/proget/api/security#authentication). A `404` response indicates that the specified `license` does not exist.