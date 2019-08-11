---
title: Try / Catch Blocks
keywords: otter,execution-engine,operation-groups
sequence: 300
---

Try/Catch Blocks are helpful when you need to change the default handling of errors, perhaps to run an error handler, or to change the status shown to viewers of the execution. Try/Catch Blocks are most commonly found in orchestration plan.

By default, when an error occurs during the execution of a plan:

1. the execution status is changed to failing
2. the execution halts

With a Try/Catch block, if an error occurs in the main ("try") body, then the error handler ("catch") statements are run instead. 

## Example: Warn of Error {#warn-error}

For example, a single failing statement in a testing environment will, by default, fail the job run. But because of the environment scope (testing) a warning might be more appropriate. This can be achieved by inserting a Try/Catch Block to change the failing status to a warning, and the job run will then continue.

<tab-block>
    <tab name="Visual Mode">
        <img class="screenshot" src="/resources/documentation/otter/try-catch-if-status-pic.png" alt="Try Catch and change status" />
    </tab>
    <tab name="Text Mode (OtterScript)">
        <img class="screenshot" src="/resources/documentation/otter/try-catch-if-status-text.png" alt="Try Catch and change status in Otter" />
    </tab>
</tab-block>

## Example: Corrective Action {#corrective-action}

Below, a Try/Catch block has been created to start a Windows Service for `Accounts`. The plan assumes the service already exists, however if the service doesn't exist, the Try/Catch Block will then run an [ensure `Accounts` operation](/docs/otter/reference/operations/services/ensure-service) to create the service and start the service it created. If both the start service AND the ensure service operations fail, the plan will fail as expected.

<tab-block>
    <tab name="Visual Mode">
        <img class="screenshot" src="/resources/documentation/otter/try-catch-block.png" alt="Try Catch Block" />
    </tab>
    <tab name="Text Mode (OtterScript)">
        <img class="screenshot" src="/resources/documentation/otter/try-catch-text.png" alt="Try Catch Text" />
    </tab>
</tab-block>

You probably wouldn't want to do this in production - a [Configuration Plan](/docs/otter/core-concepts/plans#configuration) is a more appropriate way to ensure the service configuration in the first place - but this just shows you the flexibility of what you can do when you need to.
