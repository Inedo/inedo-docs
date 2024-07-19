---
title: "Try / Catch Blocks"
order: 3
---

Try/Catch Blocks are helpful when you need to change the default handling of errors, perhaps to run an error handler, or to change the status shown to viewers of the execution.

By default, when an errors occur during the execution of a plan:

1. the execution status is changed to failing
2. the execution halts

With a Try/Catch block, if an error occurs in the main ("try") body, then the error handler ("catch") statements are run instead.

## Example: Warn of Error
For example, a single failing statement in a testing environment will, by default, fail the job run. But because of the environment scope (testing) a warning might be more appropriate. This can be achieved by inserting a Try/Catch block to change the failing status to a warning and the job run will then continue.

#### Visual Mode
![try-catch-if-status-pic.png](/resources/docs/try-catch-if-status-pic.png){height="" width=""}

Text Mode (OtterScript)
![try-catch-if-status-text \(1\).png](/resources/docs/try-catch-if-status-text%20%281%29.png){height="" width=""}

## Example: Corrective Action
Below a Try/Catch block has been created to start a Windows Service for `Accounts`. Because the plan assumes the service already exists, however if the service doesn't exist the Try/Catch block will then run an [ensure `Accounts` operation](/docs/buildmaster/reference/operations/services/ensure-service){target="_blank"} to create the service and start the service it created. If both the start service AND the ensure service operations fail, the plan will fail as expected.

#### Visual Mode
![try-catch-block.png](/resources/docs/try-catch-block.png){height="" width=""}

#### Text Mode (OtterScript)
![try-catch-text.png](/resources/docs/try-catch-text.png){height="" width=""}