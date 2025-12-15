---
title: "Delete Connector"
order: 5
---

*Delete Connector* is available as both a `pgutil` command and an HTTP Request, and will delete a specified [connector](/docs/proget/feeds/connector-overview).

:::(Info) (🚀 Quick Example: Deleting a connector with pgutil)
This example deletes the connector `myNugetConnector`:

```bash
pgutil connectors delete --connector=myNugetConnector
```
:::

## Command Specification (CLI)
The `connectors delete` command is used to delete a [connector](/docs/proget/feeds/connector-overview).

The `--connector` option is always required.

**Deleting a connector** requires the connector name (e.g. `myNugetConnector`):

```bash
pgutil connectors delete --connector=myNugetConnector
```

## HTTP Request Specification
To delete a specified [connector](/docs/proget/feeds/connector-overview) simply `DELETE` to the URL with the `connector` name and an [appropriate API Key](/docs/proget/api/connectors#authentication).

```plaintext
DELETE /api/management/connectors/delete/«connector-name»
```

## HTTP Response Specification

A `200` response will indicate the Connector has been deleted from ProGet. A `403` response indicates a [missing, unknown, or unauthorized API Key](/docs/proget/api/connectors#authentication).