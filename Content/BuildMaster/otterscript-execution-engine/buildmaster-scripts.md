---
title: "What are Scripts in BuildMaster?"
order: 1
---

:::(Internal) (TODO)
- talk about text templates
- link to deploy script templates
:::


BuildMaster uses scripts that you create to build and deploy your applications and perform other automation tasks that may be required. [OtterScript](/docs/buildmaster/otterscript-execution-engine/buildmaster-otterscript-overview) is the primary scripting language, but since OtterScript can be edited with a low-code, drag-and-drop editor, it may not feel like you're writing scripts at all.

BuildMaster also supports other scripting languages—PowerShell, Bash (Shell), Windows Batch and Python—which means you can reuse your existing scripts and use them in the same way. Regardless of the type of script or how you edit it, scripts are versioned in BuildMaster and can even be stored in an external Git repository. See [Rafts & Git Storage](/docs/otter/scripting-in-otter/otter-rafts-and-git-storage) to learn more.

Scripts are typically run in a [pipeline](/docs/buildmaster/deployment-continuous-delivery/buildmaster-pipelines), and are targeted against one or more servers. But BuildMaster can run scripts in other contexts as well, such as:
* Create or deploy builds without a pipeline
* After a commit is detected in a Git or other source control repository
* On a regular basis, as a scheduled job

## Adding Scripts to BuildMaster
You can add scripts to an application by navigating to Settings > Scripts > Add Script, and choosing one of the options.

![buildmaster-build-script-add](/resources/docs/buildmaster-build-script-add.png){height="" width="50%"}

There are two options when adding scripts: Script Templates and Blank Scripts.

## Script Templates
Script Templates use a GUI to edit pre-defined [build scripts](/docs/buildmaster/builds-continuous-integration/buildmaster-build-scripts) and [deployment scripts](/docs/buildmaster/deployment-continuous-delivery/buildmaster-deployment-scripts) that can handle most projects built on popular development platforms and deployed to popular targets, such as:

* [Build .NET Project](/docs/buildmaster/builds-continuous-integration/buildmaster-build-scripts); compiles a .NET project/solution, runs test, tracks NuGet package usage, captures build artifacts for later deployment
* Deploy IIS Site; deploys a build artifact to a server as an IIS website.
* Deploy Windows Service; deploys a build artifact to a server as a Windows service.

![buildmaster-deploy-script-template](/resources/docs/buildmaster-deploy-script-template.png){height="" width="50%"}

Although they do not look like a script, they are called "script templates" because they use OtterScript behind the scenes. The script template simply modifying certain properties of various operations in a predefined OtterScript.

### Converting Script Templates to OtterScript
To see or edit the underlying OtterScript in your script template, click the "Edit as OtterScript" button.

![buildmaster-scripts-convert-to-otterscript](/resources/docs/buildmaster-scripts-convert-to-otterscript.png){height="" width="50%"}

After clicking Edit as OtterScript, you can use the OtterScript Editor and switch between text mode and low-code visual editor mode to drag and drop statements and blocks.

![buildmaster-build-script-otterscript](/resources/docs/buildmaster-build-script-otterscript.png){height="" width="50%"}

After you save the OtterScript (i.e. click Commit ), you cannot edit it as a template.

### Undoing the Edit as OtterScript Button
If you didn't click the Commit button to save the changes, you can simply click "Review Changes" and then "Undo Changes" on the comparison page:

![buildmaster-script-diffs](/resources/docs/buildmaster-script-diffs.png){height="" width="50%"}

## Orchestrating Scripts with OtterScript
OtterScript is also designed to run *other* scripts with ease, so you can reuse scripts for common automation tasks. After you add your PowerShell, Bash (Shell), Windows Batch, or Python scripts to BuildMaster, you can run them like any other operation in OtterScript.

For example, if you added the PowerShell script `Abl2Scan.ps1`, BuildMaster will detect the script parameters and allow you to set them as needed.

![buildmaster-powershell-pscall](/resources/docs/buildmaster-powershell-pscall.png){height="" width="50%"}

BuildMaster is able to do this if you use Comment-Based Help, or other Augmented Headers with your scripts. For example, in PowerShell you can specify parameters like this.
```
<#
    .SYNOPSIS
    Executes the Abl2 scanner with the target parameters

    .PARAMETER ScanTarget
    The file name to target for the scan

    .PARAMETER ScanLevel
    The level of scanning (1 - 10)

    .PARAMETER Debug
    Indicates whether to run in debug or not
#>
param ($ScanTarget, $ScanLevel, $Debug)

Write-Host "Initializing Abl2 scanner  tool..."
```

Scripts you call in OtterScript will be run against whatever server you've targeted in the pipeline, but you can just as easily use OtterScript to target different servers:
```
for server MyTargetServer
{
    PSCall Abl2Scan.ps1
    (
        Parameters: %(ScanTarget: HdarProj.asm, Debug: False, ScanLevel: 4)
    );
}
```

You can also execute other scripts in-line through the use of operations like `PSExec`, `SHExec`, and `PyExec`. For example, a PowerShell script would be run with `PSExec` as follows.

```
PSExec >>
  # delete all but the latest 3 logs in the log directory
  Get-ChildItem "E:\Site\Logs\$ApplicationName" |
     Sort-Object $.CreatedDate -descending |
     Select-Object -skip 3 |
     Remove-Item
>>;
````

BuildMaster seamlessly substitutes variables within this script string, and in the above example $ApplicationName would refer to the current application being built or deployed.



:::(Internal) (Private notes)
## Text Templates
- Create a blank Text Template that utilizes BuildMaster's variable and variable functions
- We don't have great documentation for this
- :::
