---
title: "Remediating Drift with PowerShell & PSEnsure"
order: 3
---

One of the major benefits of using Otter is that you can use your existing PowerShell scripts to verify that your servers are in a desired state of configuration. With just a bit of modification, Otter can use those same scripts to reconfigure your servers into your desired state.

Note that if you're already familiar with and using [PowerShell Desired State Configuration (DSC)](https://docs.microsoft.com/en-us/powershell/scripting/dsc/overview/overview), you can use the `PSDsc` operation to invoke those DSC resources. 

`PSEnsure` is a simpler alternative that lets you use basic PowerShell commands.

## Configuration as Code with Basic PowerShell
Many of your PowerShell scripts likely change the configuration of the server they run on, whether that means configuration network settings or installing operating system features.

When your scripts also verify that the configuration change is needed before making it, then you've got the essence of "configuration as code".

Consider this very basic script, `ensure-KB4532938.ps1`:
```PowerShell
if (-not(Get-Hotfix -Id $KB)) {
    Start-Process -FilePath "wusa.exe" -ArgumentList "KB4532938.msu /quiet /norestart" -Wait 
} 

else {
    Write-Information "HotFixID KB4532938 is already installed."
}
```
:::(Info) (NOTE:)
This is just a trivial example. A real configuration script would be dozens of lines long, with a lot more complicated logic, and do things like set registry keys, etc. 
:::
If you were to run it on your computer, this script would install HotFixID KB4532938 or give you a message saying that it’s installed.

You could also use an Orchestration Job to run this script against a server in Otter, and it would work in the same way. But the real power of Drift Detection and Remediation is identifying when the configuration changes (Drift) and having the flexibility of how to change it back (Remediation). See [Automatically Remediate Configuration Drift](/docs/otter/drift-remediation-configuration-as-code/otter-automatically-remediate-configuration-drift) to learn more.

## Converting Existing PowerShell to Detect & Remediate Drift
The primary change you'll need to make to your scripts is to have them run in two different execution modes: Collect and Configure.

This can be as simple as adding a single [Augmented Help Comment](/docs/otter/scripting-in-otter/otter-scripting-augmented-help) block (`AHEXECMODE`) and an if/then statement in your script:
```(PowerShell)
<# 
.AHEXECMODE 
$ExecutionMode
#>

if ($ExecutionMode -eq "Collect") {
# Return the Current Configuration State
} 

elseif ($ExecutionMode -eq "Configure") {     
# Set the Desired Configuration State
}    
```
The "Execution Mode" is s not all that dissimilar from PowerShell's WhatIf parameter.
* "Collect" means that Otter is running the script to gather the current configuration state from the server.
* "Configure" means that Otter is running the script the "Collect" script indicated that the current configuration is drifted.

See the [Compliance as Code with PowerShell & PSVerify](/docs/otter/collecting-verifying-configuration/otter-compliance-as-code-with-powershell-psverify) documentation to see all the options available for detecting drift. Essentially, the PSVerify Operation is only running in a "Collect" mode.

For example, here's what `ensure-KB4532938.ps1` would look like:
```PowerShell
<# 
.AHEXECMODE 
$ExecutionMode
#>

if ($ExecutionMode = "Collect") {
    if (-not(Get-Hotfix -Id $KB)) {
        Write-Information "HotFixID KB4532938 is not installed."
        return $false
    } 
    else {
        Write-Information "HotFixID KB4532938 is installed."
        return $true
        }
    } 
elseif ($ExecutionMode -eq "Configure") { 
    if (-not(Get-Hotfix -Id $KB)) {
        Start-Process -FilePath "wusa.exe" -ArgumentList "KB4532938.msu /quiet /norestart" -Wait 
    } 
    else {
        Write-Information "HotFixID KB4532938 is already installed."
    }
}
```
It's pretty easy from there:
1. Upload your script (`ensure-KB4532938.ps1`) to Otter
2. Create a Server Role named `ensure-KB4532938`
3. On that server role's Desired configuration, add PSEnsure ensure-KB4532938;`

From there, Otter will check the desired state against servers you assign, and report drift.
If you've configured the server to allow for remediation, you can automatically remediate - and Otter will then run the "Configuration" portion for you.

## Using parameters in your PSEnsure scripts
You probably would never create a role or script named `ensure-KB4532938`; instead, you'd want to add parameters that allow it to operate differently, depending on the input provided.

![Add Parameters](/resources/docs/otter-remediation-addparameters.png){height="" width="50%"}

This works exactly the same as other PowerShell scripts; see [Compliance as Code with PowerShell & PSVerify](/docs/otter/collecting-verifying-configuration/otter-compliance-as-code-with-powershell-psverify) to learn how Otter can read the Help-Based Comments for PARAMETER names.

## Controlling how configuration keys/types are stored and displayed
This also works exactly the same as PSVerify; see [Compliance as Code with PowerShell & PSVerify](/docs/otter/collecting-verifying-configuration/otter-compliance-as-code-with-powershell-psverify) to learn you can control the configuration type and keys.

The `AHCONFIGTYPE` and `AHCONFIGKEY` [Augmented Help Comments](/docs/otter/scripting-in-otter/otter-scripting-augmented-help) blocks, along with PARAMETER blocks, allow you to reuse scripts multiple times on the same server.

## Controlling desired, current, and drifted configuration
Although the `AHDESIREDVALUE`, `AHCURRENTVALUE`, and `AHVALUEDRIFTE`D Augmented-Help blocks work just like they do in PSVerify (see [Compliance as Code with PowerShell & PSVerify](/docs/otter/collecting-verifying-configuration/otter-compliance-as-code-with-powershell-psverify)), it's unlikely they won't add a lot of value in PSEnsure-style scripts.

The example we used in the PSVerify documentation was to report on whether a drive had ample free space:
```PowerShell
.AHDESIREDVALUE 
$MinimumFreeGB

.AHCURRENTVALUE 
$ActualFreeGB

.AHVALUEDRIFTED
$AmpleFreeSpace
````
The logic is really simple:  if `$ActualFreeGB` is greater than `$MinimumFreeGB` then `$AmpleFreeSpace` is true, and the configuration is not drifted. This lets you see the `$ActualFreeGB` instead of just `$true` or `$false`.

In a `PSEnsure` context, there's really nothing sensible you could do here. Even if the work was just to "clear temporary files", that's something you should consider doing with a [Scheduled Job](/docs/otter/orchestration-server-automation/otter-orchestration-scheduled-recurring-jobs) on a routine basis - not as part of configuration drift management.

## Using Multiple Configurations (Advanced Usage)
Just as a single PowerShell script can instruct Otter to store multiple pieces of configuration (see [Compliance as Code with PowerShell & PSVerify](/docs/otter/collecting-verifying-configuration/otter-compliance-as-code-with-powershell-psverify)), you can use multiple Augmented-Help blocks to re-configure multiple pieces of configuration. This starts making your PowerShell Scripts pretty complex, however... and isn't something we generally recommend with `PSEnsure`