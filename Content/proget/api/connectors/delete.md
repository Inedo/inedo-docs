---
title: "Delete Connector"
order: 5
---

*Delete Connector* is available as both a `pgutil` command and an HTTP Request, and will delete a specified [Connector](/docs/proget/feeds/connector-overview).

:::(Info) (🚀 Quick Example: Deleting a Connector with pgutil)
This example deletes the connector `myNugetConnector`:

```bash
pgutil connectors delete --connector=myNugetConnector
```
:::

## Command Specification (CLI)
The `connectors delete` command is used to delete a Connector.

The `--connector` option is always required.

:::(info)(📄 Note: pgutil Sources)
Source options must also be specified unless you have the "Default" source configured. See [Working with Sources](/docs/proget/api/pgutil#sources) to learn more.
:::

**Deleting a connector** requires the connector name (e.g. `myNugetConnector`):

```bash
pgutil connectors delete --connector=myNugetConnector
```

## HTTP Request Specification
To delete a specified Connector, simply `DELETE` to the following URL with the Connector name and an [appropriate API Key](/docs/proget/api/connectors#authentication).

```plaintext
DELETE /api/management/connectors/delete/«connector-name»
```

## HTTP Response Specification

A successful `200` response will indicate the Connector has been deleted from ProGet. A `403` response indicates a [missing, unknown, or unauthorized API Key](/docs/proget/api/connectors#authentication).
