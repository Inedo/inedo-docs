---
title: Build Plans
sequence: 300
keywords: buildmaster, plans
show-headings-in-nav: true
---
BuildMaster uses the OtterScript programming language to create build plans that accomplish all of the technical continuous integration (CI) details required to get code from source control, compile it, and build/package your applications. 

<tab-block>
<tab name= "Visual Mode">

![](/resources/documentation/otter/plan-blocks.png)

</tab>
<tab name="Text Mode (OtterScript)">

![](/resources/documentation/otter/plan-block-text.png)

</tab>
</tab-block>

## BuildMaster's Programmatic Build Plans 

Despite the need for organizations to create custom build plans, the functionality offered by solutions like Azure DevOps continues to require developers to conform to the platform's workflow, instead of the other way around. When it comes to these legacy solutions, the concept of code-based build plans is extremely rare, if not nonexistent.

There are several reasons for this:

First, monolithic tools, systems, and platforms often add functionality quickly—simply to keep pace with market expectations. The incentive with these solutions is to push out a feature as fast as possible—just so they can say it exists. For example, Azure DevOps’ build plans are based on a series of rigid forms that force you to conform your workflow to the platform. This is because build plans is an ‘add-on’ feature whose functionality hasn’t been integrated into the rest of the platform's functionality. The result is that build plans exist technically exist, but they lack the dynamism that maximizes their value.

Second, legacy functionality limits the extent of changes that can be made. Features are constantly 'bolted on' without regard to either how the pieces fit together, or the overall user experience. As explained in the 'creating build plans' section below, solutions like Jenkins/TeamCity require you to manually repeat steps across pipelines that could easily be automated and unified across the platforms' various features.

## Creating Build Plans

OtterScript offers two distinct advantages over the build plan creation functionality in solutions like Jenkins and TeamCity:

First, it is a programmatic language that lets any competent programmer customise your build plans using logic, including if/then statements, loops, and variables. Compare that to TeamCity's and Jenkins' linear approach that leads you through a series of pre-loaded questions such as, "what type of source control do you use?", and provides you pre-defined answers to choose from. If this one-size-fits-all approach works for you, it works great. But as soon as you want to do something that doesn't exactly follow the outline of the form fill-ins, you must use complicated scripting that requires platform specific expertise.

Second, OtterScript lets you save all of the logic required for your custom build plans in an OtterScript file that can automatically create builds with the same configurations across all of your applications. In Jenkins/TeamCity you must pick the source control, directory, etc. for every application you build. To do that, you must fill out the same form fields over and over and over again every time. And if you want to change source control, you are going to have to change it manually for every application.

## Association with Pipelines 

Once created, BuildMaster's build plans run in the context of a build and a 'pipeline'. Conceptually, pipelines are hierarchical entities that 'nest' inside one another in the following order:

    Pipeline: A sequence of stages 
    Stage: One or more targets 
    Targets: References a server and an OtterScript file

Build plans can be created to automate any of these entities. For example, you can create a build plan that will run exclusively as a stage in a pipeline. An example of this use is designing a repeatable process for creating the build.

Alternatively, you can create a build plan that will detect an event (such as a source control change, or web hook) that will create a whole build and place it into a pipeline. An example of this use would be when you want to do a hot fix.

## Modules

Modules are reusable OtterScript scripts that are executed by other OtterScript scripts. One common use case is if multiple scripts share a significant portion of code. You can modularize the common code and execute it in any script that requires it. This eliminates the need to manually recreate the code multiple times.

### Module Parameters

Similar to operations, modules can have input and output parameters. You can edit these by clicking on the Module Properties button in the Visual Plan Editor (when in visual mode), or by editing the OtterScript directly. These parameters are then accessible as variables within the module.

Modules are run within the scope of whatever block calls it, which means it will inherit all that block's variables. As a result, you should generally avoid relying on externally defined variables in modules.

**Note:** *modules* are called *templates* in BuildMaster v5.

## PowerShell and Shell Scripting

OtterScript was designed to seamlessly integrate with PowerShell and Bash/Sh, and can run inline script blocks, evaluate scripting expressions into variable values, and call externally stored script files called assets.

Script assets can be added to BuildMaster at the application- or global-level. Once added, your scripts will appear in the statement list in the visual plan editor, and the parameters to those scripts will be parsed and displayed as textboxes when editing in visual mode.

Visit the shared [PowerShell and Shell Scripting documentation](/docs/executionengine/components/powershell-and-shell) for more information.

## Plan Version Control and Rollback

BuildMaster 5.8 and later maintains a history of edits and allows rollback to any plan version, similar to a "revert" in source control. Version history is maintained for both build plans and modules.

### Comparing Versions

On any plan's listing page, select the version number to display a list of all versions of the plan. From the Plan Version Listing page, there are options to compare any previous versions with the current. On the Plan Version Comparison page, any global plan or plan from any application visible to a user may be compared side-by-side.

### Plan Rollback

To roll back to a previous version of a build plan, select the "edit" option of the desired version from the Plan Version Listing page, and then save the plan. This will create a new version of the plan with the same contents as the rollback target.
