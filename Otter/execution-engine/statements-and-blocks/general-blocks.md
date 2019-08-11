---
title: General Blocks
keywords: otter,execution-engine,operation-groups
sequence: 100
---

General Blocks allow you to define properties that operations will use, and that will control how contained statements and blocks run.
    <tab-block>
        <tab name="Visual Mode">
            <img class="screenshot" src="/resources/documentation/otter/block-2-complete.png" alt="General Blocks in Otter" />
        </tab>
        <tab name="Text Mode (OtterScript)">
            <img class="screenshot" src="/resources/documentation/otter/general-block-text.png" alt="General Blocks in OtterScript" />
        </tab>
    </tab-block>

### General Group Properties {#general}

{.docs}
*   **Short Description** sets the description or name of the current block.
*   **Run on Server** sets the server used for execution of operations within the block
*   **Run if server has role** restricts operation based on server role
*   **Run for deployable** determines whether the current release in context has that deployable included, and skips the block if it does not
*   **Set working directory** sets the current working directory that file-based Operations will use
*   **Execution options**
    *   **Execute asynchronously (in parallel)** when checked (along with an optional token), the entire block will run asynchronously (see below).
    *   **Execute exclusively (locked)** when checked The exclusive or lock option will prevent other general blocks with that token from executing at the same time. (see below).
    *   **Specify execution timeout** when checked this block will timeout if not complete within specified number of seconds.
    *   **Retry on Error** when checked block will try to run a specified number of times whenever it encounters an error.
    *   **Disable this block** when checked this block will be ignored
    *   **Isolation** indicates that all remote operations inside the block will be performed in a new process that will be terminated when control flow leaves the block.
*   **Timeout** the number of seconds to wait for the contained items to run; if they don't run in this time, an error will be raised
*   **Retry count** if any of the contained items raise an error, then the entire block (and all nested blocks) will be run as many times specified; if a retry succeeds, then the execution status will not change
### Asynchronous Blocks {#async}

An *asynchronous* block works just like a general block, except the execution will continue with the statement/block immediately following the asynchronous block, while the asynchronous block runs in the background. In this way, multiple long-running blocks can execute in parallel.

An *Await Asynchronous Operation* statement will suspend the current execution until all asynchronous blocks have been completed. The execution engine adds an implicit *await* to the end of any plan if there are background blocks running, but you can always await sooner if part of your plan depends on the asynchronous blocks completing.

If an asynchronous block is given a token, then this token must be supplied to an await statement to act as a filter; that is, only asynchronous blocks with that specific token will be awaited.

When an *await* statement is executed, the execution status is inherited from the background block if it is in an error or warn state. An *await* statement can be enclosed inside a [Try/Catch](/docs/otter/execution-engine/statements-and-blocks/try-catch) block to handle any recoverable errors that happened in the background. {.info}

```
foreach server in @ServersInRole(database-nodes)
{
  # these will take an eternity to run, so run in background
  with async
  {
    PSExec >> some really long-running script >>;
  }
}

# Hopefully database servers are caught up by now, but wait just in case
await;

log-debug all async blocks finished!;
```
