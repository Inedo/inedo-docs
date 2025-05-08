---
title: "Other Statements"
order: 6
---

In addition to structured statements like `foreach`, `try-catch`, and `if-else`, OtterScript offers several standalone statements that give you fine-grained control over your deployment plans.

These "Other" statements help you:
* Manage variables
* Write to logs
* Control execution status
* Pause execution for asynchronous operations
* Raise manual errors

| Statement| Purpose |
|---|---|
| `set` | Declare or update a variable |
| `log` | Write a message to the execution log |
| `set-status` | Change execution status (Fail or Warn) |
| `raise-error` | Stop execution immediately |
| `await` | Wait for background asynchronous blocks |

## Set Variable Statement

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

## Log Statement

The `log` statement writes messages to the execution log at a specified level:
* Debug
* Information
* Warn
* Fail

```bash
log-information "Deployment started.";
```

Logging at the Fail or Warn level can also change the execution status.

| Log Level  | Behavior |
|---|---|
| Debug | Only shown in verbose logs |
| Information | Standard logs |
| Warn | Changes status to Warning (unless already Fail) |
| Fail | Changes status to Fail |

## Set-Status Statement

You can manually adjust the execution status using the `set-status` statement:

```bash
set-status Fail;
```

Use cases:
* After catching an error and deciding that it should escalate to a failure.
* After detecting a condition that should warn without stopping execution.

## Raise-Error Statement

Use the `raise-error` statement to immediately halt execution (unless caught in a `try-catch` statement block):

In this example:
* Without `try-catch`, the plan fails immediately.
* Inside `try-catch`, control jumps to the `catch` section.

```bash
raise-error "Critical file not found.";
```

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