---
title: "Try/Catch Blocks"
order: 5
---

`try/catch` Statements in OtterScript function like typical "try/catch" statements found in other languages, offering a way to manage runtime errors, letting you catch exceptions, run fixes, tweak the flow, or log issues without halting the whole deployment.

#### Common `try/catch` Usage
* Recoverable Failures: If a primary operation fails, run an alternative or corrective action.
* Changing Severity: Convert a failure into a warning when failure isn't critical.
* Custom Error Logging: Log specific error details to help with troubleshooting.

:::(info) (`try/catch` Example:)
A typical `try/catch` statement in OtterScript:

```bash
try
{
    Log-Information "Trying to start service...";
    throw "Simulated failure";
}
catch
{
    Log-Information "Service failed to start. Attempting recovery steps...";
}
```
:::

## How Error Handling Works in OtterScript

By default:
* Any error inside `try` stops execution and marks the job as failing.
* With a `try/catch` statement:
    * The error is caught.
    * Execution continues with `catch` instead of halting.

After `catch`:
    * Execution resumes with the next statement after the `try/catch` statement.

## Changing Execution Status in `catch` 

Sometimes you don't want an error to count as a total failure — maybe just a warning.

You can use the `set-status` statement inside `catch`:

```bash
try
{
    Delete;
}
catch
{
    Log-Warning "Tests failed, but continuing.";
    set-status Warn;
}
```

In this case:
* The overall execution status becomes Warn instead of Fail.
* Execution proceeds normally after the block.

:::(warn)
Use Set-Status cautiously. Overriding status can hide serious problems if used improperly.
:::

## Nested `try/catch` Statements

OtterScript lets you nest `try/catch` statements if needed:

```bash
try
{
    try
    {
        Critical-Setup;
    }
    catch
    {
        Log-Error "Critical setup failed.";
        set-status Fail;
    }
}
catch
{
    Log-Information "Main block error handling.";
}
```

## Retry on Error { #retry }

You can configure certain statements or operations to automatically retry on failure. This is especially useful for transient issues like temporary network glitches:

```bash
retry 3
{
    Run-DeploymentStep;
}
```
