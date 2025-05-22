---
title: "Try/Catch Blocks"
order: 5
---

`try/catch` Statements in OtterScript function like typical "try/catch" statements found in other languages, offering a way to manage runtime errors, letting you catch exceptions, run fixes, tweak the flow, or log issues without halting the whole deployment.

:::(info) (`try/catch` Example:)
A typical `try/catch` statement in OtterScript:

```bash
try
{
    throw Something failed;
}
catch
{
    Log-Information Something went wrong, but continuing;
}
Log-Information Script continued;
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

You can use the `warn` statement inside `catch`:

```bash
try
{
    throw Something failed;
}
catch
{
    warn;
}
Log-Information Script continued;
```

Example Output:

```bash
DEBUG: 2025-05-22 03:30:13Z - Beginning execution run...
ERROR: 2025-05-22 03:30:13Z - Something failed
INFO : 2025-05-22 03:30:13Z - Script continued
WARN : 2025-05-22 03:30:13Z - Execution run succeeded with warnings.
```

In this case:
* The overall execution status becomes "Warn" instead of "Fail".
* Execution proceeds normally after the block.

You can also use `force normal` to suppresses the error, treating the execution as fully successful.

```bash
try
{
    throw an error occurred;
}
catch
{
    force normal;
}
Log-Information Script continued;
```

Example Output:

```bash
DEBUG: 2025-05-22 03:34:13Z - Beginning execution run...
ERROR: 2025-05-22 03:34:13Z - an error occurred
INFO : 2025-05-22 03:34:13Z - Script continued
INFO : 2025-05-22 03:34:13Z - Execution run succeeded.
```

:::(warn)
Use `force` cautiously. Overriding status can hide serious problems if used improperly.
:::

## Nested `try/catch` Statements

OtterScript lets you nest `try/catch` statements if needed:

```bash
try
{
    try
    {
        throw Inner error occurred;
    }
    catch
    {
        Log-Information Inner error caught;
        throw Outer error occurred;
    }
    Log-Information After inner try/catch;
}
catch
{
    Log-Information Outer error caught;
}
Log-Information Script continued;
```

Example Output:

```bash
DEBUG: 2025-05-22 13:07:00Z - Beginning execution run...
ERROR: 2025-05-22 13:07:00Z - Inner error occurred
INFO : 2025-05-22 13:07:00Z - Inner error caught
ERROR: 2025-05-22 13:07:00Z - Outer error occurred
INFO : 2025-05-22 13:07:00Z - Outer error caught
INFO : 2025-05-22 13:07:00Z - Script continued
```

## Retry on Error { #retry }

You can configure certain statements or operations to automatically retry on failure. This is especially useful for transient issues like temporary network glitches:

```bash
retry 3
{
    Log-Information script running;
}
```