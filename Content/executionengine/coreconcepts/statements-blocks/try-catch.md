---
title: "Try/Catch Blocks"
order: 5
---

Try-Catch Blocks in OtterScript allow you to gracefully handle errors that occur during execution.
Instead of immediately failing a deployment when an error occurs, you can catch the error, run corrective actions, change execution status, or log information.
Basic Usage

A Try-Catch Block has two parts:

    The Try section: where you place operations that might fail.

    The Catch section: where you define what happens if an error occurs in the Try section.

Example (Text Mode):

try
{
    Start-Service Accounts
}
catch
{
    Ensure-Service Accounts
    (
        StartupType: Automatic,
        EnsureRunning: true
    );
}

In this example:

    Try to start the Accounts service.

    If it fails (e.g., service does not exist), Catch the error and ensure the service is created and started.

Visual Mode

In Visual Mode:

    Insert a Try-Catch block.

    Add statements inside both the Try and Catch sections.

    Customize behavior (optional): change status or add logs inside the Catch section.

How Error Handling Works

By default:

    Any error inside the Try section stops execution and marks the job as failing.

    With a Try-Catch Block:

        The error is caught.

        Execution continues with the Catch block instead of halting.

After the Catch block:

    Execution resumes with the next statement after the Try-Catch block.

Changing Execution Status in Catch Blocks

Sometimes you don't want an error to count as a total failure — maybe just a warning.

You can use the Set-Status statement inside Catch:

try
{
    Run-Tests
}
catch
{
    Log-Warning "Tests failed, but continuing.";
    set-status Warn;
}

In this case:

    The overall execution status becomes Warn instead of Fail.

    Execution proceeds normally after the block.

Common Scenarios

    Recoverable Failures: If a primary operation fails, run an alternative or corrective action.

    Changing Severity: Convert a failure into a warning when failure isn't critical.

    Custom Error Logging: Log specific error details to help with troubleshooting.

Best Practices

    Only catch specific errors when needed: Broad Try-Catch usage can sometimes mask real problems.

    Always log inside Catch blocks: It helps future readers (and yourself!) understand what happened.

    Use Set-Status cautiously: Overriding status can hide serious problems if used improperly.

Nested Try-Catch

You can also nest Try-Catch blocks if needed:

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