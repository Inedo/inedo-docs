---
title: Manual Steps and Tasks
subtitle: Manual Steps and Tasks
sequence: 400
keywords: buildmaster, manual
show-headings-in-nav: true
---
While OtterScript and PowerShell will allow you to automate just about anything, there are times when it’s simpler to do things manually. For example:

{.docs}
- Updating a version number on a WordPress page as a new release is being published
- Enabling a VPN to a customer site for a short time during a deployment
- Running an ancient GUI-based deployment for part of an application

This is where the [Manual Operations](/docs/buildmaster/reference/operations/buildmaster/manual-operation) comes in: when executed in a deployment plan, the execution will halt until an assigned person indicates that a specified task has been completed.

## Using in a Deployment Plan {#using-plan data-title="Using in a Deployment Plan"}

You can add the Manual Operation to your plan just like you would any other operation, by dragging-and-dropping it from the statement list, or directly in OtterScript.

<tab-block>
<tab name="Visual Mode">

![](/resources/documentation/buildmaster/6/manual-operation.png){.screenshot}

</tab>
<tab name="Text Mode (OtterScript)">

```
Perform-ManualOperation
(
    Name: Run HDARS-Deployer,
    Description: "Log onto HDARS server, run the desktop hdars-deployer.exe app and follow instructions",
    AssignedTo: Deployer,
    SendEmail: true
);
```

</tab>
</tab-block>

When this statement is encountered, the execution engine will first send an email (if specified) to the user and/or groups specified with a link to their My Tasks page in BuildMaster. You can also specify additional email addresses in the Advanced tab.

From the My Tasks page, a user can see all pending manual tasks, and mark them complete as needed.  After marking a manual task complete, the execution can continue.

## Debugging with Manual Tasks {#debug-manual data-title="Debugging with Manual Tasks"}

Manual tasks can also help you debug a deployment plan.

{.docs}
- **Inspect working directory;** If an operation is failing, indicating that files are missing from the current working directory, or are otherwise different than expected, you can simply put a manual operation immediately before the failing operation to investigate the state of the working directory
- **Manual error handling;** you can place a manual operation in the error handler of a Try/Catch statement to allow for manual intervention
