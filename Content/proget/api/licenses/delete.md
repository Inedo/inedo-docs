---
title: "Delete License"
order: 5
---

*Delete License* is available as both a pgutil command and an HTTP Request, and will delete a specified license.

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
To delete a specified license simply `DELETE` to the URL with the `license` id and an [appropriate API Key](/docs/proget/api/licenses#authentication). This request supports partial updating by only updating the properties that are supplied in the request.

```bash
POST /api/licenses/delete?code=«license-id»
```

## HTTP Response Specification

| Response | Details |
|---|---|
| **200 (Success)** | will delete the specified `license` |
| **400 (Invalid Input)** | indicates invalid or missing properties in the request; the body will provide some details as text |
| **403 (Unauthorized API Key)** | indicates a [missing, unknown, or unauthorized API Key](/docs/proget/api/licenses#authentication); the body will be empty |
| **404 (License Not Found)** | indicates that the specified `license` does not exist |