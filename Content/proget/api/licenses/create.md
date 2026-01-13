---
title: "Create License"
order: 1
---

:::(internal) (⚠ Not Working)
Not working as of ProGet version 2023.22
:::

*Create License* is available as both a `pgutil` command and an HTTP Request, and will create a license.

:::(Info) (🚀 Quick Example: Creating a license with pgutil)
This example will create the license `ABC License 1.0` with the ID `ABC-1.0`:

```bash
pgutil licenses create --title="ABC License 1.0" --code=ABC-1.0
```
:::

## Command Specification (CLI)
The `licenses create` command is used to create a license by name and code.

The `--title` and `--code` options are always required.

**Adding a license** requires the license name (e.g. `ABC License 1.0`) and license code (e.g. `ABC-1.0`):

```bash
pgutil licenses create --title="ABC License 1.0" --code=ABC-1.0
```

## Request Specification
To create a license, simply `POST` to the following URL with an [appropriate API Key](/docs/proget/api/licenses#authentication) and a `LicenseInfo` object (see [LicenseInfo.cs](https://github.com/Inedo/pgutil/blob/thousand/Inedo.ProGet/LicenseInfo.cs)) as the request body.

```plaintext
POST/PUT /api/licenses/add
```

## HTTP Response Specification
A `LicenseInfo` object will be returned on a successful `200` response. A '403' response indicates a [missing, unknown, or unauthorized API Key](/docs/proget/api/licenses#authentication).
