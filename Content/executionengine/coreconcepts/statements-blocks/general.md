---
title: "General Code Blocks"
order: 2
---

General Blocks are the foundational block type in OtterScript. They group together related statements and allow you to define shared execution settings such as server targeting, working directories, error handling, and parallelism.

General Blocks are flexible, powerful, and critical for building robust, organized deployment plans.
Basic Usage

A General Block groups statements together:

Example (Text Mode):

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

You can add a Short Description to label the block, which helps structure the Execution Log with collapsible sections for easier review.
General Block Properties

When configuring a General Block, you can control how and where the block runs:
Property	Description
Short Description	Sets the label that appears in the execution log.
Run on Server	Specifies which server the operations inside the block run on.
Run if Server has Role	Restricts block execution to servers with a specific role.
Run for Deployable	Executes the block only if a specific deployable is included in the release.
Set Working Directory	Changes the working directory for file-based operations.
Execution Options

General Blocks also support advanced execution controls:
Option	Behavior
Execute Asynchronously (with optional token)	Runs the block in the background without waiting for completion.
Execute Exclusively (Locked with token)	Prevents simultaneous execution of blocks sharing the same lock token.
Specify Execution Timeout	Fails the block if it doesn’t complete within a set number of seconds.
Retry on Error	Retries the block a specified number of times if an error occurs.
Disable this Block	Ignores the block during execution.
Isolation	Runs all remote operations in a temporary process that terminates when the block ends (available in BuildMaster 6.1.11+, Otter 2.2.5+).
Advanced Usage
Asynchronous Execution

Running a block asynchronously allows your plan to continue executing without waiting for the block to finish.

Example:

for server @ServersInGroup(database-nodes)
{
    with async
    {
        PSExec >> Run-LongScript >>;
    }
}

# Later in the plan
await;
log-debug All async blocks finished!;

Use an await statement to wait for asynchronous blocks to finish when needed.
You can also assign and filter by tokens for more granular control.
Exclusive Execution (Locking)

You can prevent multiple blocks from executing simultaneously by assigning a lock token.

Example:

for server localhost
{
    with lock=abc123
    {
        Log-Information "This block is locked with token: abc123";
    }
}

Only one block with the same lock token (abc123) can run at a time.
Running as a Different User

You can execute operations inside a block under different credentials:

for server remote-server
{
    with credentials=remote-server-credentials
    {
        // Operations here run under the specified user
    }
}

Isolation: New Process Execution

Using Isolation ensures that remote operations inside the block are executed in a separate, disposable process:

for server remote-server
{
    with isolation
    {
        // Operations run in isolated process
    }
}

This improves security and prevents environment contamination between operations.
Log Scoping

General Blocks automatically create log scopes, which make plan execution much easier to follow:

    Blocks appear as collapsible sections in the execution log.

    Short Descriptions label log scopes.

    Sensitive log data (e.g., operation arguments) can be protected by permission settings.

Example of scoped logs:

> Ensure Accounts App (General Block)
    > Ensure-Directory
    > Ensure-Asset
    > Extract-ZipFile