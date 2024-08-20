---
title: "Create License"
order: 1
---

:::(internal) (⚠ Not Working)
Not working as of ProGet version 2023.22
:::

*Create License* is available as both a `pgutil` command and an HTTP Request, and will create a license, with the properties defined by a [LicenseInfo](/docs/proget/reference-api/proget-api-licenses#license-object) object in the body of the request.

:::(Info) (🚀 Quick Example: Creating a license with pgutil)
This example will create the license `ABC License 1.0` with the ID `ABC-1.0`
```
pgutil licenses create --title="ABC License 1.0" --code=ABC-1.0
```
:::

## Command Specification (CLI)
The `licenses create` command is used to create a license by name and code.

The `--title` and `--code` options are always required.

**Adding a license** requires the license name (e.g. `ABC License 1.0`) and license code (e.g. `ABC-1.0`):
```
pgutil licenses create --title="ABC License 1.0" --code=ABC-1.0
```

## Request Specification
To create a license, simply `PUT` or `POST` to the URL with an [appropriate API Key](/docs/proget/reference-api/proget-api-licenses#authentication) and a [LicenseInfo](/docs/proget/reference-api/proget-api-licenses#license-object) object as the request body.

```
POST/PUT /api/licenses/add
```

## HTTP Response Specification
A successful (`200`) response body will be empty.

| Response | Details |
|---|---|
| **200 (Success)** | a `license` will be created |
| **400 (Invalid Input)** | indicates invalid or missing properties in the request; the body will provide some details as text |
| **403 (Unauthorized API Key)** | indicates a [missing, unknown, or unauthorized API Key](/docs/proget/reference-api/proget-api-licenses#authentication); the body will be empty |