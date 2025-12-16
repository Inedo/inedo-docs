---
title: "List ApiKeys"
order: 2
---

*List ApiKeys* is available as both a `pgutil` command and an HTTP Request, and will list the API keys in ProGet.

:::(Info) (🚀 Quick Example: List API Keys with pgutil)

```bash
pgutil apikeys list
```
:::

This API requires ProGet 2024.3 or later. 

## Command Specification (CLI)

The `apikeys list` has an optional `--source` and related authentication options (i.e. `--api-key` or `--username` / `--password`). See [Working with Sources in pgutil](/docs/proget/api/pgutil#working-with-sources) to learn more.

Running the command will print API Key and related fields to the standard output.

```bash
$> pgutil apikeys list
(unnamed key)
  Id:          1000
  Expiration:  None
  Logging:     None
  Type:        Personal
  User:        Admin
```

Note source options must also be specified unless you have the "Default" source configured, and that a feed may be instead specified in the source. See [Working with Sources](/docs/proget/api/pgutil#sources) to learn more.

## HTTP Request Specification

To get a list of ApiKeys, simply `GET` to `/api/api-keys/list` and an array of `ApiKeyInfo` JSON object (see [`ApiKeyInfo.cs`](https://github.com/Inedo/pgutil/blob/thousand/Inedo.ProGet/ApiKeyInfo.cs)) will be returned.

## HTTP Response Specification

An array of [`ApiKeyInfo.cs`](https://github.com/Inedo/pgutil/blob/thousand/Inedo.ProGet/ApiKeyInfo.cs) objects will be returned on a successful `200` response. A `403` response indicates a [missing, unknown, or unauthorized API Key](/docs/proget/api/apikeys).