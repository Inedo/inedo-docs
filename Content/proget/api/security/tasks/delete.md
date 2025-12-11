---
title: "Delete Task"
order: 3
---

*Delete Task* is available as both a `pgutil` command and an HTTP Request, and will delete a specified Task from ProGet.

:::(Info) (🚀 Quick Example: Deleting a Task with pgutil)
This example will delete a Task `"Basic Access"`:

```bash
pgutil security tasks delete --name="Basic Access"
```
:::

## Command Specification (CLI)
The `security tasks delete` command is used to delete a Task from ProGet.

The `--name` options is always required. 

**Deleting a Task** requires the user name (e.g. `"Basic Access"`):

```bash
pgutil security tasks delete --name="Basic Access"
```

## HTTP Request Specification

To delete a Task, simply `DELETE` to the following URL with the `task` query argument specifying the Task:

```plaintext
DELETE /api/security/tasks/delete?task=«taskname»
```

## HTTP Response Specification

A `200` response will indicate the Task has been deleted from ProGet. a `403` response indicates a [missing, unknown, or unauthorized API Key](/docs/proget/api/security#authentication).