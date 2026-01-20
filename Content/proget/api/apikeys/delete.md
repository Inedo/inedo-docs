---
title: "Delete ApiKey"
order: 3
---

*Delete ApiKey* is available as both a `pgutil` command and an HTTP Request, and will delete an API key in ProGet.

:::(Info) (🚀 Quick Example: Delete an API Key with pgutil)

```bash
pgutil apikeys delete --id=1000
```
:::

This API requires ProGet 2024.3 or later. 

## Command Specification (CLI)
The `apikeys delete` command requires an `--id` option and has an optional `--source` and related authentication options (i.e. `--api-key` or `--username` / `--password`). See [Working with Sources in pgutil](/docs/proget/api/pgutil#working-with-sources) to learn more.

:::(info)(📄 Note: pgutil Sources)
Source options must also be specified unless you have the "Default" source configured. See [Working with Sources](/docs/proget/api/pgutil#sources) to learn more.
:::

### Example: Finding then Deleting an API Key
You can find the Id of an ApiKey using the `apikeys list` command. See [List ApiKeys](/docs/proget/api/apikeys/list) to learn more.

```bash
$> pgutil apikeys list
(unnamed key)
  Id:          1000
  Expiration:  None
  Logging:     None
  Type:        Personal
  User:        Admin
  
  
$> pgutil apikeys delete --id=1000
Deleting API key (id=1000)...
API key deleted.
```

## HTTP Request Specification

To delete an API key, simply `POST` to `/api/api-keys/delete?id=«apikey-id»`. 

## HTTP Response Specification

| Response | Details |
| --- | --- |
| **200 (Success)** | indicates the API key was deleted; the body will contain the text of the api key created |
| **400 (Invalid Input)** | indicates a missing or non-integer value for `id` |
| **403 (Unauthorized API Key)** | indicates a [missing, unknown, or unauthorized API Key](/docs/proget/api/apikeys) |
| **404 (API Key Not Found)** | indicates the specified key was not found |

