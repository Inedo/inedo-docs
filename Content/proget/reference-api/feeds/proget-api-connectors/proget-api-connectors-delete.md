---
title: "Delete Connector"
order: 5
---

*Delete Connector* is available as both a `pgutil` command and an HTTP Request, and will delete a specified connector.

:::(Info) (🚀 Quick Example: Deleting a connector with pgutil)
This example deletes the connector `myNugetConnector`
```
pgutil connectors delete --name=myNugetConnector
```
:::

## Command Specification (CLI)
The `connectors delete` command set is used to delete a connector.

The `--name` option is always required.

**Deleting a connector** requires the connector name (e.g. `myNugetConnector`):
```
pgutil connectors delete --name=myNugetConnector
```

## HTTP Request Specification
To delete a specified connector simply `DELETE` to the URL with the `connector` name and an [appropriate API Key](/docs/proget/reference-api/feeds/proget-api-connectors#authentication).

```
DELETE /api/management/connectors/delete/«connector-name»
```

## HTTP Response Specification

| Response | Details |
|---|---|
| **200 (Success)** | will delete the specified `connector` |
| **400 (Invalid Input)** | indicates invalid or missing properties in the request; the body will provide some details as text |
| **403 (Unauthorized API Key)** | indicates a [missing, unknown, or unauthorized API Key](/docs/proget/reference-api/feeds/proget-api-connectors#authentication); the body will be empty |
| **404 (Connector Not Found)** | indicates that the specified `connector` does not exist |