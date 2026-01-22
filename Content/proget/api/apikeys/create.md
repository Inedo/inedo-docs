---
title: "Create ApiKey"
order: 1
---

*Create ApiKey* is available as both a `pgutil` command and an HTTP Request, and will create a System, Personal, or Feed API key in ProGet.

:::(Info) (🚀 Quick Example: Create a Personal API Key with pgutil)

```bash
pgutil apikeys create personal --source=https://proget.corp.local/ --username=jrdobbs --password=hunter42
```
:::

This API requires ProGet 2024.3 or later. 

## Command Specification (CLI)
The `apikeys create` command set is used to create an ApiKey in a ProGet instance. 

There are three commands available (`system`, `personal`, and `feed`), and each command has options that correspond to fields in the web UI, including `--description`, `--expiration`, `--logging`, `--name`, and `--key`. See [API Keys & Access](/docs/proget/api/apikeys) to learn more.

These commands also have optional `--source` and related authentication options (i.e. `--api-key` or `--username` / `--password`). See [Working with Sources in pgutil](/docs/proget/api/pgutil#working-with-sources) to learn more.

| Command | Additional Options |  |
| --- | --- | --- |
| `system` | `apis` | `full-control` or a comma-separated list with a combination  `feeds`, `sca`, and  `sbom-upload`; defaults to `full-control` |
| `feed`  | `feed`,  `group` | name of a feed or feed group in ProGet; exactly one must be specified |
| `personal` | `user` | defaults to the user specified in the  `--username` option or source |

The key will be written to stdout on success.

**Example: Creating a System API key with restrictions**  can be done by limiting the APIs  (e.g. Feeds and SCA) and setting an expiration (`2024/08/01``).

```bash
pgutil apikeys create system --apis="feeds,sca" --expiration="2024/08/01"
```

**Example: Creating a Feed API key** requires a feed name (`MyNpmFeed`):

```bash
pgutil apikeys create feed --feed=MyNpmFeed
```

**Example: Creating a Feed API key for a group** requires a feed group name (`MyFeedGroup`):

```bash
pgutil apikeys create feed --group=MyFeedGroup
```

**Example: Creating a Personal Key for another user** requires the user's name (`johnsmith`):

```bash
pgutil apikeys create personal --user=johnsmith
```

## HTTP Request Specification
To create an API key, simply `POST` a `ApiKeyInfo` JSON object (see [ApiKeyInfo.cs](https://github.com/Inedo/pgutil/blob/thousand/Inedo.ProGet/ApiKeyInfo.cs)) as the request body to `/api/api-keys/create`. 

## HTTP Response Specification

Text of the API key will be returned on a successful `200` response and indicates the API key has been created. A `403` response indicates a [missing, unknown, or unauthorized API Key](/docs/proget/api/apikeys).

