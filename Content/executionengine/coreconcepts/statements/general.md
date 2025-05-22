---
title: "General Statements"
order: 2
---

In addition to structured statements like [`foreach`](/docs/executionengine/coreconcepts/statements/foreach), [`try/catch`](/docs/executionengine/coreconcepts/statements/try-catch), and [`if/else`](/docs/executionengine/coreconcepts/statements/if-else), OtterScript offers several standalone statements that give you fine-grained control over your deployment plans.

These statements help you:
* Manage variables
* Write to logs
* Control execution status
* Pause execution for asynchronous operations
* Raise manual errors

| Statement| Purpose |
|---|---|
| `set` | Declare or update a variable |
| `log` | Write a message to the execution log |
| `warn`, `fail`, `force normal` | Change execution status |
| `await` | Wait for background asynchronous blocks |

## `set` Variable Statement

The `set` variable statement declares or updates a variable inside the current scope.

In these examples:
* If the variable doesn't exist yet, it is declared and assigned.
* If it already exists in the current scope, its value is updated.

```bash
set $EnvironmentName = Production;
```

:::(info)
Changing a variable inside a plan does not modify the variable globally (e.g., on a server or environment asset) — it only affects the current execution scope.
:::

## `log` Statement ( #log-statement )

The `log` statement writes messages to the execution log at a specified level:

| Log Level  | Behavior |
|---|---|
| `-debug` | Only shown in verbose logs |
| `-information` | Standard logs |
| `-warning` | Writes a warning message to Standard logs |
| `-error` | Writes an error message to Standard Logs |

* Logging a `-warning` message will set the execution status to `warn`
* Logging an `-error` message will set the execution status to `fail`

Example:

```bash
log-information "Deployment started.";
```

## `warn`, `fail` and `force normal` Statements

You can manually adjust the execution status using the `warn`, `fail` and `force normal` Statements

| Execution Status  | Behavior |
|---|---|
| `warn `| Executions will complete with a warning |
| `fail` | Executions will fail |
| `force normal` | Executions will complete as successful |

Examples:

```bash
force warn;
```

```bash
fail;
```

:::(internal)(Removed?)
## Raise-Error Statement

Use the `raise-error` statement to immediately halt execution (unless caught in a `try-catch` statement block):

In this example:
* Without `try-catch`, the plan fails immediately.
* Inside `try-catch`, control jumps to the `catch` section.

```bash
raise-error "Critical file not found.";
```
:::

## Await Statement

The `await` statement pauses the current plan execution until asynchronous blocks finish running.

#### Basic Usage:

```bash
await;
```

#### With a Token:

```bash
await TokenName;
```

* If a token is provided, it will wait only for asynchronous statement blocks tagged with that token.
* Useful when you want to selectively wait for certain background operations.

If you don't insert an `await`, OtterScript automatically adds an implicit `await` at the end of the plan if background tasks are still running.