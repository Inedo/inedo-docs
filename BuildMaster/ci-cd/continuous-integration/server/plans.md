---
title: Build Plans
sequence: 300
keywords: buildmaster, plans
show-headings-in-nav: true
---
  Build plans are the instructions that tell BuildMaster exactly how to build and package your application. They are written in language called OtterScript, and can be developed using the drag-and-drop editor. This saves you from having to learn the syntax before you can start building a plan. You can switch back-and-forth between visual and text modes to get a feel for the syntax and structure of the language pretty quickly, and master it in no time.

<tab-block>
<tab name= "Visual Mode">

![](/resources/documentation/otter/plan-blocks.png)

</tab>
<tab name="Text Mode (OtterScript)">

![](/resources/documentation/otter/plan-block-text.png)

</tab>
</tab-block>

### Creating Build Plans {#creating-plans data-title="Creating Build Plans"}

You can create a build plan at the application or global level. Global plans behave in exactly the same manner, except you may only reference global modules.

You may associate a build plan with an environment after you create it, which will allow you to restrict certain users from viewing its contents by defining an environment-scoped [access control](/docs/buildmaster/administration/users-and-security). There's no good reason to do this, as you shouldn't be putting sensitive information in your build plans… but sometimes it can't be helped.

### Association with Pipelines {#pipelines data-title="Association with Pipelines"}

All build plans are run under the context of a build and a pipeline, except those defined as the target of a Repository Hook/Monitor plan. Visit the [pipelines](/docs/buildmaster/verification/pipelines) documentation for more information on how to configure pipeline stages and targets.

### OtterScript Basics {#otterscript-basics data-title="OtterScript Basics"}

OtterScript is a Domain-Specific Language that was designed for high-level orchestration and automation across servers, and is used to represent build plans in BuildMaster.

To learn more, check out the [OtterScript Guide](/docs/various/execution-engine/otterscript) in the Inedo Execution Engine documentation.

### PowerShell and Shell Scripting {#power-and-shell data-title="PowerShell and Shell Scripting"}

OtterScript was designed to seamlessly integrate with PowerShell and Bash/Sh, and can run inline script blocks, evaluate scripting expressions into variable values, and call externally -stored script files called assets.

Script assets can be added to BuildMaster at the application- or global-level. Once added, your scripts will appear in the statement list in the visual plan editor, and the parameters to those scripts will be parsed and displayed as textboxes when editing in visual mode.

Visit the shared [PowerShell and Shell Scripting documentation](/docs/executionengine/components/powershell-and-shell) for more information.

### Modules {#modules data-title="Modules"}

Modules are reusable plans that can be called from a build plan or other modules, as if it were an operation. Like build plans, modules can be created at the application- or global-level, and are edited using the visual plan editor.

To create or edit modules, go to the Modules tab on the plans listing page. Modules are available in the statements list in the visual plan editor.   

#### Module Parameters {#module-parameters data-title="Module Parameters"}

Similar to operations, modules can have input- and output parameters. You can edit these by clicking on the Module Properties button in the Visual Plan Editor (when in visual mode), or by editing the OtterScript directly. These parameters are then accessible as variables within the module.   

:::attention {.best-practice}
Modules are run within the scope of whatever block calls it, which means it will inherit all of that block's variables. As a result, you should generally avoid relying on externally-defined variables in modules.
:::

**Note:** *modules* are called *templates* in BuildMaster v5.

## Plan Version Control and Rollback {#plan-version-control data-title="Plan Version Control and Rollback"}

BuildMaster 5.8 and later maintains a history of edits and allows rollback to any plan version, similar to a "revert" in source control. Version history is maintained for both build plans and modules.    

### Comparing Versions {#comparing-versions data-title="Comparing Versions"}

On any plan's listing page, select the version number to display a list of all versions of the plan. From the Plan Version Listing page, there are options to compare any previous versions with the current. On the Plan Version Comparison page, any global plan or plan from any application visible to a user may be compared side-by-side.

### Plan Rollback {#plan-rollback data-title="Plan Rollback"}

To roll back to a previous version of a build plan, select the "edit" option of the desired version from the Plan Version Listing page, and then save the plan. This will create a new version of the plan with the same contents as the rollback target.
