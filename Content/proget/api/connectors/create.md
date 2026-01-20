---
title: "Create Connector"
order: 1
---

*Create Connector* is available as both a `pgutil` command and an HTTP Request, and will create a [Connector](/docs/proget/feeds/connector-overview).

:::(Info) (🚀 Quick Example: Creating a Connector with pgutil)
This example will create a `NuGet` Connector `myNugetConnector`, connecting to `https://proget.connector.local`:

```bash
pgutil connectors create --name=myNugetConnector --type=NuGet --url=https://proget.connector.local
```
:::

## Command Specification (CLI)
The `connectors create` command is used to create a Connector.

The `--name`, `--type` and `--url` options are always required. 

:::(info)(📄 Note: pgutil Sources)
Source options must also be specified unless you have the "Default" source configured. See [Working with Sources](/docs/proget/api/pgutil#sources) to learn more.
:::

**Creating a connector** requires the Connector name (e.g. `myNugetConnector`), type (e.g. `NuGet`) and the URL (e.g. `https://proget.connector.local`):

```bash
pgutil connectors create --name=myNugetConnector --type=NuGet --url=https://proget.connector.local
```

## HTTP Request Specification
To create a Connector, simply `POST` to the URL with an [appropriate API Key](/docs/proget/api/connectors#authentication) and a `ProgetConnector` (see [ProGetConnector.cs](https://github.com/Inedo/pgutil/blob/thousand/Inedo.ProGet/ProGetConnector.cs)) object as the request body.

```plaintext
POST/PUT /api/management/connectors/create
```

## HTTP Response Specification
A `Connector` object will be returned on a successful `200` response. A `403` response indicates a [missing, unknown, or unauthorized API Key](/docs/proget/api/connectors#authentication).
