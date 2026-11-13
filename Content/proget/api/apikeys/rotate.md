---
title: "Rotate ApiKey"
order: 3
---

*Rotate ApiKey* is available as both a `pgutil` command and an HTTP Request, and will generate a new key for an existing API key in ProGet.

:::(Info) (🚀 Quick Example: Rotate an API Key with pgutil)

```bash
pgutil apikeys rotate --id=1000
```
:::

This API requires ProGet 2026.14 or later. 

## Command Specification (CLI)
The `apikeys rotate` command requires an `--id` option and has an optional `--source` and related authentication options (i.e. `--api-key` or `--username` / `--password`). See [Working with Sources in pgutil](/docs/proget/api/pgutil#working-with-sources) to learn more.

:::(info)(📄 Note: pgutil Sources)
Source options must also be specified unless you have the "Default" source configured. See [Working with Sources](/docs/proget/api/pgutil#sources) to learn more.
:::

### Example: Finding then Rotating an API Key
You can find the Id of an ApiKey using the `apikeys list` command. See [List ApiKeys](/docs/proget/api/apikeys/list) to learn more.

```bash
$> pgutil apikeys list
(unnamed key)
  Id:          1000
  Expiration:  None
  Logging:     None
  Type:        Personal
  User:        Admin
  
  
$> pgutil apikeys rotate --id=1000
Rotating API key (id=1000)...
API key rotated. New key:
bba30fa0cd8804118625a8c24fed6bdcb21e42ac
```

## HTTP Request Specification

To rotate and return the new key for an API key, simply `POST` to `/api/api-keys/rotate?id=«apikey-id»`. 

## HTTP Response Specification

A successful `201` response indicates the API key has been rotated. A `403` response indicates a [missing, unknown, or unauthorized API Key](/docs/proget/api/apikeys).

