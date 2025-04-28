---
title: "Other Blocks"
order: 6
---

Other Code Blocks

In addition to structured code blocks like For Each, Try-Catch, and If-Else,
OtterScript offers several standalone statements that give you fine-grained control over your deployment plans.

These "Other" blocks and statements help you:

    Manage variables

    Write to logs

    Control execution status

    Pause execution for asynchronous operations

    Raise manual errors

Set Variable Statement

The Set Variable statement declares or updates a variable inside the current scope.

Example:

set $EnvironmentName = Production;

    If the variable doesn't exist yet, it is declared and assigned.

    If it already exists in the current scope, its value is updated.

Important:
Changing a variable inside a plan does not modify the variable globally (e.g., on a server or environment asset) — it only affects the current execution scope.
Log Statement

The Log statement writes messages to the execution log at a specified level:

    Debug

    Information

    Warn

    Fail

Example:

log-information "Deployment started.";

Logging at the Fail or Warn level can also change the execution status.
Log Level	Behavior
Debug	Only shown in verbose logs
Information	Standard logs
Warn	Changes status to Warning (unless already Fail)
Fail	Changes status to Fail
Set Status

You can manually adjust the execution status using the Set-Status statement.

Example:

set-status Fail;

Use cases:

    After catching an error and deciding that it should escalate to a failure.

    After detecting a condition that should warn without stopping execution.

Raise Error

Use the Raise-Error statement to immediately halt execution (unless caught in a Try-Catch block).

Example:

raise-error "Critical file not found.";

Behavior:

    Without Try-Catch, the plan fails immediately.

    Inside Try-Catch, control jumps to the Catch section.

Await

The Await statement pauses the current plan execution until asynchronous blocks finish running.

Basic Usage:

await;

With a Token:

await TokenName;

    If a token is provided, it will wait only for asynchronous blocks tagged with that token.

    Useful when you want to selectively wait for certain background operations.

Note:
If you don't insert an await, OtterScript automatically adds an implicit await at the end of the plan if background tasks are still running.
Summary Table
Statement	Purpose
set	Declare or update a variable
log	Write a message to the execution log
set-status	Change execution status (Fail or Warn)
raise-error	Stop execution immediately
await	Wait for background asynchronous blocks
Best Practices

    Use log and set-status inside Catch blocks for better error reporting.

    Raise errors intentionally — don't overuse raise-error if standard error handling suffices.

    Await selectively if some async blocks can continue independently.