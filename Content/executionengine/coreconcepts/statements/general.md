---
title: "General Statements"
order: 2
---

General Statements are the foundational statement type in OtterScript. They allow you to define shared execution settings such as server targeting, working directories, error handling, and parallelism. General Statements are flexible, powerful, and critical for building robust, organized deployment plans.

## Basic Usage

A General Statement block groups statements together:

:::(info) (General Statement Example:)
A typical General Statement in OtterScript:

```bash
{
    Ensure-Directory $WebsitesRoot;

    Ensure-Asset
    (
        Name: Accounts.zip,
        Directory: $PackagesRoot
    );

    Extract-ZipFile $PathCombine($PackagesRoot,Accounts.zip)
    (
        Directory: $WebsitesRoot,
        Overwrite: True
    );
}
```
:::

When configuring a General Statement, you can control how and where the statement runs:

| Property | Description |
|-------------------------|-----------------------------------------------------------------------|
| Short Description | Sets the label that appears in the execution log. |
| Run on Server | Specifies which server the operations inside the statement run on. |
| Run if Server has Role  | Restricts statement execution to servers with a specific role. |
| Run for Deployable | Executes the statement only if a specific deployable is included in the release. |
| Set Working Directory | Changes the working directory for file-based operations. |

## Execution Options

General Statements also support advanced execution controls:

| Option | Behavior |
|---|---|
| Execute Asynchronously (with optional token) | Runs the statement in the background without waiting for completion. |
| Execute Exclusively (Locked with token) | Prevents simultaneous execution of statements sharing the same lock token. |
| Specify Execution Timeout | Fails the statement if it doesn’t complete within a set number of seconds. |
| Retry on Error | Retries the statement a specified number of times if an error occurs. |
| Disable this Statement Block | Ignores the statement block during execution. |
| Isolation | Runs all remote operations in a temporary process that terminates when the statement ends (available in BuildMaster 6.1.11+, Otter 2.2.5+). |

## Advanced Usage

### Asynchronous Execution { #asynchronous }

Running a statement asynchronously allows your plan to continue executing without waiting for the statement to finish:

```bash
for server @ServersInGroup(database-nodes)
{
    with async
    {
        PSExec >> Run-LongScript >>;
    }
}

# Later in the plan
await;
log-debug All async statements finished!;
```

Use an await statement to wait for asynchronous statement blocks to finish when needed. You can also assign and filter by tokens for more granular control.

### Exclusive Execution (Locking) { #exclusive }

You can prevent multiple statement blocks from executing simultaneously by assigning a lock token:

```bash
for server localhost
{
    with lock=abc123
    {
        Log-Information "This block is locked with token: abc123";
    }
}
```

Only one statement block with the same lock token (`abc123`) can run at a time.

### Running as a Different User { #different }

You can execute operations inside a statement block under different credentials:

```bash
for server remote-server
{
    with credentials=remote-server-credentials
    {
        // Operations here run under the specified user
    }
}
```

### Isolation: New Process Execution { #isolation }

Using Isolation ensures that remote operations inside the statement block are executed in a separate, disposable process:

```bash
for server remote-server
{
    with isolation
    {
        // Operations run in isolated process
    }
}
```

This improves security and prevents environment contamination between operations.

### Log Scoping { #logscoping }

General Statements automatically create log scopes, which make plan execution much easier to follow:
* Statement blocks appear as collapsible sections in the execution log.
* Short Descriptions label log scopes.
* Sensitive log data (e.g., operation arguments) can be protected by permission settings.

<image>