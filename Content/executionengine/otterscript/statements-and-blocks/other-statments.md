---
title: "Other Statements"
order: 5
---

In addition to running [operations](/docs/executionengine/overview/executionengine-components-operations-functions){target="_blank"} and other plans as templates, the following statements are available:

### Set Variable Statement
This will both declare and set the value of a variable for the current and all nested blocks. If a variable was already set and declared in a parent block, then the variable's value will be set.

If a variable is specified outside of the plan execution (for example, a variable on a Server or Environment), setting the value within the plan will not change the external value, but will instead only create an alias for the current block. You should try to avoid doing this when possible.

### Log Statement
This statement will write text to the execution log at the specified level (Debug, Information, Warn, Fail) and may change the execution status.

If you log at the Fail level, then the execution status will be set to Fail but other blocks and statements will execute as per normal. If you log Warning, the status will change to Warn unless it's already Fail.

### Set Status
The execution status is generally set by the success or failure of operations in a plan, but you can also change the status to 'Fail' or 'Warn' as needed. This is commonly used inside of the Catch (On Error) portion of a [Try/Catch](/docs/executionengine/otterscript/statements-and-blocks/try-catch){target="_blank"} block to set the status to the desired level.

### Raise Error
You can halt the execution engine, in either a recoverable (i.e. inside of a Try/Catch block) or irrecoverable manner. In both cases, the execution status will be set to `Fail`.

### Await
You can also pause the execution engine and wait for asynchronous blocks to complete; if you only want to wait for a certain blocks, specify the same token in both the block and the await statement.