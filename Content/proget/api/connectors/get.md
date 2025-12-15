---
title: "Get Connector"
order: 2
---

*Get Connector* is available as both a `pgutil` command and an HTTP Request, and will return a single [ProgetConnector](/docs/proget/api/connectors#connector-object) object or a list of properties for the specified [connector](/docs/proget/feeds/connector-overview).

:::(Info) (🚀 Quick Example: Getting a connector with pgutil)
This example returns properties of the connector `myNugetConnector`:

```bash
pgutil connectors properties list --connector=myNugetConnector
```
:::

## Command Specification (CLI)
The `connectors properties list` command is used return the properties of a single [connector](/docs/proget/feeds/connector-overview).

The `--connector` option is always required. 

**Getting a connector** requires the connector name (e.g. `MyNugetConnector`):

```bash
pgutil connectors properties list --connector=myNugetConnector
```
This will return the properties of the specified connector:

```plaintext
url=https://api.nuget.org/v3/index.json
feedType=nuget
timeout=10
metadataCacheEnabled=false
metadataCacheCount=*not set*
metadataCacheMinutes=*not set*
```

## HTTP Request Specification
To return a specified [connector](/docs/proget/feeds/connector-overview), simply `GET` to the URL with the name of the `connector` and an [appropriate API Key](/docs/proget/api/connectors#authentication).

```plaintext
GET /api/management/connectors/get/«connector-name»
```

## HTTP Response Specification
A [Connector](/docs/proget/api/connectors#connector-object) (see [ProGetConnector.cs](https://github.com/Inedo/pgutil/blob/thousand/Inedo.ProGet/ProGetConnector.cs)) object will be returned on a successful `200` response. A `403` response indicates a [missing, unknown, or unauthorized API Key](/docs/proget/api/connectors#authentication).