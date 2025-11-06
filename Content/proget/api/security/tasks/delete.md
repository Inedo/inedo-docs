---
title: "Delete Task"
order: 3
---

*Delete Task* is available as both a `pgutil` command and an HTTP Request, and will delete a specified task from ProGet.

:::(Info) (🚀 Quick Example: Deleting a task with pgutil)
This example will delete a task `"Basic Access"`:

```bash
pgutil security tasks delete --name="Basic Access"
```
:::

## Command Specification (CLI)
The `security tasks delete` command is used to delete a task from ProGet.

The `--name` options is always required. 

**Deleting a task** requires the user name (e.g. `"Basic Access"`):

```bash
pgutil security tasks delete --name="Basic Access"
```

## HTTP Request Specification

To delete a task, simply `DELETE` to the URL with the `task` query argument specifying the task:

```plaintext
DELETE /api/security/tasks/delete?task=«taskname»
```

## HTTP Response Specification

| Response | Details |
| --- | --- |
| **200 (Success)** | the user is deleted from ProGet |
| **401 (Authentication Required)** | indicates a [missing, unknown, or unauthorized API Key](/docs/proget/api/assets#authentication) |
