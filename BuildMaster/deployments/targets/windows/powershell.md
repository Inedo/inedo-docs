---
title: PowerShell and BuildMaster
sequence: 800
keywords: buildmaster, powershell
show-headings-in-nav: true
---

[PowerShell](https://docs.microsoft.com/en-us/powershell/scripting/overview) is an [open-source](https://github.com/powershell/powershell), cross-platform command line client that enables power users to rapidly automate system tasks. BuildMaster is designed to seamlessly integrate its pipelines and plans with PowerShell scripts, and can store PowerShell scripts as script assets to be re-used across your application deployment portfolio.

## Storing PowerShell Scripts in BuildMaster {#assets data-title="Storing PowerShell Scripts"}

PowerShell scripts can be stored in BuildMaster as script assets. If a script is intended to be shared throughout multiple applications in the system, it should be stored as a global script asset. For application-specific scripts, they should be stored as a script asset in the context of an application.

::: {.upcoming}
Starting in BuildMaster v6.2, script assets will be stored in rafts, which will enable plans and scripts to be defined directly in Git. Before v6.2, you can simply use the [`Invoke-Plan` operation](/docs/buildmaster/reference/operations/buildmaster/invoke-plan) to run plans that you store in Git, or use one of the various [source control operations](/docs/buildmaster/builds/continuous-integration/source-control) to pull the script first and run it from disk.
:::

### PowerShell Script Asset Documentation Header

Script assets in BuildMaster take advantage of the PowerShell [Comment-based Help Keywords](https://docs.microsoft.com/en-us/powershell/scripting/developer/help/comment-based-help-keywords) format when generating input fields in the Plan Editor. The following fields are automatically parsed by BuildMaster:

{.docs}
 - `.Synopsis` - appended with `.Description` headings to create a brief description of the PowerShell script
 - `.Parameter <Parameter-Name>` - a brief description of the parameter to the script

The synopsis is used as a subtitle in the plan editor, and the parameter names and descriptions are used as well to populate help text when using `PSCall`. An example script header is as follows:

```
<#
    .SYNOPSIS
    Writes a message to the BuildMaster log

    .PARAMETER Message
    Specifies the message to write to the BuildMaster log.
#>
param ([string]$Message)

function Write-Message { 
    Write-Host $Message
}
```

## Running PowerShell Scripts {#running data-title="Running PowerShell Scripts"}

After writing or uploading scripts as a script asset, you can invoke them with the [`PSCall` operation](/docs/buildmaster/reference/operations/powershell/pscall) in a build or deployment plan as follows:

```
PSCall CreateUserInDirectory(
  domain: hdars.local,
  user: $UserName
);
```

PowerShell scripts, regardless of whether they are invoked as direct text or script assets, are run directly on the server in the context of the build or deployment plan.

## Inline PowerShell Statements {#inline data-title="Inline PowerShell"}

With the `PSExec` operation, you can incorporate scripts directly into build or deployment plans. For example:

```
psexec >>
  # delete all but the latest 3 logs in the log directory
  Get-ChildItem "E:\Site\Logs\$ApplicationName" |
     Sort-Object $.CreatedDate -descending |
     Select-Object -skip 3 |
     Remove-Item
>>;
```

BuildMaster will automatically replace variables within the script with either variables defined in the plan or [configuration variables](/docs/buildmaster/administration/configuration-variables). In the above example, `$ApplicationName` would be replaced with the name of the application in context as the plan is run.

## PowerShell vs. OtterScript {#comparison data-title="PowerShell vs. OtterScript"}

Because a script can do "anything", there's an obvious overlap between BuildMaster's built-in operations, a PowerShell Cmdlet, or custom script that you might write. In general, you should use a built-in operation when possible. Operations are designed to be massively parallel, cancellation-aware, and highly performant, and also provide descriptions when viewed outside of code.

Many scripts, on the other hand, might be written to run interactively (i.e. in a command line REPL), and may prompt for user input if an argument is missing, or do other things not suitable in non-interactive, scalable environments.

## Advanced PowerShell {#advanced data-title="Advanced PowerShell"}

### Evaluating PowerShell Literals

OtterScript is not a general-purpose programming language (although technically it is Turing complete), and thus doesn't have built-in support for things like arithmetic. You could, of course, write a [custom variable function](/docs/buildmaster/execution-engine/runtime-variables#variable-functions) to support this, but using the variable function [`$PSEval`](/docs/buildmaster/reference/functions/powershell/pseval) is much simpler. For example, if you wanted to convert the value stored in one variable (`$minutes`) to milliseconds. You could `$PSEval` the simple expression `$minutes * 60 * 1000`:

```
set $milliseconds = $PSEval($minutes * 60 * 1000);
```

`$PSEval` runs the expression on the server currently in context, so use it inside of an if/else block to perform different operations depending on the results of the expression on that server.

### Process Isolation 

PowerShell operations can also be run in a separate, isolated process. Normally, PowerShell operations run on an agent are run in the context of the agent process using a custom PowerShell host, which gives performance gains, but may lead to memory leaks or worse in improperly designed PowerShell scripts. 

By running in an isolated context instead, a new process will be created to run the script. Here is an example of an OtterScript plan that runs the same PowerShell script in different processes despite being in the same plan and run on the same agent:

```
for server targetsv
{
    with isolation
    {
        PsExec >>Write-Host "Process ID: $pid">>;
    }
    with isolation
    {
        PsExec >>Write-Host "Process ID: $pid">>;
    }
}
```
