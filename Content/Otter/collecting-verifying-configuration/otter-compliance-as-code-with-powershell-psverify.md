---
title: "Compliance as Code with PowerShell & PSVerify"
order: 4
---

Otter can use your existing PowerShell script to verify server configuration.

## Compliance as Code with Basic PowerShell
PowerShell is already widely used to test for compliance of a system. It doesn’t require Otter, or any tool – just a basic script that operators can run and inspect the output.
For example, consider this very basic script, `verify-KB4532938-installed.ps1`:

```PowerShell
if (!(Get-HotFix -Id  KB4532938) 2> $null) {
    Write-Warning "HotFixID KB4532938 is not installed."
} 

else {
    Write-Information "HotFixID KB4532938 is installed."
}
```

If you were to run it on your computer, you would see a bright, yellow warning saying that HotFixID KB4532938 is not installed.

:::(Info)
This is just a trivial example. A real compliance verification script would be dozens of lines long, with a lot more complicated logic, including checking whether various hotfixes are installed, and registry keys are set. 
:::

## Converting Existing PowerShell to Detect Drift
The easiest option is just a slight modification to your script to return `$true` (if configuration is correct) or `$false` (if it's drifted).

Taking that trivial sample script, we just need to do this:
```PowerShell
if (!(Get-HotFix -Id  KB4532938) 2> $null) {
    Write-Warning "HotFixID KB4532938 is not installed.“
    return $false
} 

else {
    Write-Information "HotFixID KB4532938 is installed."
    return $true
}
```

Otter can now routinely and automatically scan for compliance using this script. When your script detects drift on a server, Otter will report it as such.

![](/resources/docs/Converting-Existing-PowerShell-to-Detect-Drift.png){height="" width=""}

**Output:**
![](/resources/docs/Converting-Existing-PowerShell-to-Detect-Drift-output.png){height="" width=""}

## Step-by-step: PSVerify Basic Usage
1. Just “return $false” if drift is detected, and “return $true” if not
2. Upload your script (`verify-KB4532938-installed.ps1`) to Otter
3. Create a Server Role named `verify-KB4532938`
4. On that server role's Desired configuration, add `PSVerify verify-KB4532938-installed;`

From there, Otter will check the desired state against servers you assign, and report drift.

## Using parameters in your PSVerify scripts
Compliance scripts often have parameters that allow them to operate differently, depending on the input provided.

Going back to our trivial example, you could parameterize "HotFixID" so that the verification script could be used to detect if any hotfix is installed, instead of KB4532938.

```PowerShell
<# 
.PARAMETER HotFixID
ID of the HotFix to check, such as "KB4562830"
#>

param ([string]$HotFixID)

if (!(Get-HotFix -Id  $HotFixID) 2> $null){
    Write-Warning "HotFixID $HotFixID is not installed"
    return $false;
}
return $true;
```
When you use Comment-based help, Otter will automatically detect these parameters when you build your OtterScript Configurations.

![](/resources/docs/Using-parameters-in-your-PSVerify-scripts.png){height="" width=""}

Otter uses PowerShell's Comment-based help and will pass the inputs as variables to your scripts.

## Controlling how configuration keys/types are stored and displayed
A *configuration type* describes a general category of configuration on a server. For example, “File”, “Directory, “IIS AppPool”, etc.

A *configuration key* describes a specific piece of configuration on a server. For example, “C:\files\hello.txt” and “KB4532938”.

By default, Otter will use the "PSVerify" as the *configuration type*, and the script name (e.g., `verify-KB4532938-installed`) as the *configuration key*. This is fine for the most part, especially if you have long verification scripts that do a lot of things.
As you write shorter and more general-purpose verification scripts, you will want to change the default type and keys.

You can change this default behavior with the use of [Augmented Help Comments](/docs/otter/scripting-in-otter/otter-scripting-augmented-help) in your script. These work just like ordinary comment-based help, with `AHCONFIGTYPE` and `AHCONFIGKEY` as the comment-block names.

Using our trivial HotFix verification script, we can control how Otter records the configuration's type and key as follows:
```PowerShell
<# 
.PARAMETER HotFixID
ID of the HotFix to check, such as "KB4562830"

.AHCONFIGTYPE
HotFix

.AHCONFIGKEY 
$HotFixID
#>
param ([string]$HotFixID)

if (!(Get-HotFix -Id  $HotFixID) 2> $null){
    Write-Warning "HotFixID $HotFixID is not installed"
    return $false;
}
return $true;
```

This will allow you to reuse verification scripts multiple times on the same server, and help clarify what configuration is collected.
```OtterScript
PSVerify verify-hotfix-installed
(
  HotFixID: KB4532938
);

PSVerify verify-hotfix-installed
(
  HotFixID: KB4483843
);

PSVerify verify-hotfix-installed
(
  HotFixID: KB8842155
);
```

![](/resources/docs/Controlling-how-configuration-keys-types-are-stored-and-displayed.png){height="" width=""}

## Controlling desired, current, and drifted configuration
You can further control how PSVerify collects and compares configuration through the use of `AHDESIREDVALUE`, `AHCURRENTVALUE`, and `AHVALUEDRIFTED` [Augmented Help Comments](/docs/otter/scripting-in-otter/otter-scripting-augmented-help) in your script.

For example, your desired configuration may be that the "C" drive has at least 20 gigabytes free. It doesn't matter how much free space (21GB  or 210GB free), just that it's at least 20GB.

This is a relatively simple PowerShell statement, and with some Augmented-Help blocks, Otter can interpret the results better than a simple $true/$false.
```PowerShell
<# 
.DESCRIPTION
Verifies a drive has sufficient free space

.PARAMETER DriveToVerify
The drive letter (such as "C") for what to verify.

.PARAMETER MinimumFreeGB
The minimum free space required on the drive, in gigabytes.

.AHDESIREDVALUE 
$MinimumFreeGB

.AHCURRENTVALUE 
$ActualFreeGB

.AHVALUEDRIFTED
$AmpleFreeSpace
#>
param ([int]$MinimumFreeGB, [string]$DriveToVerify)

$ActualFreeGB = [int]((Get-PSDrive $DriveToVerify).Free / (1024 * 1024 * 1024))
$AmpleFr
```
In this example, the script requires the parameters $MinimumFreeGB, $DriveToVerify, and you could use the following OtterScript to call it and verify that "C" has 20 gigabytes free.
```OtterScript
PSVerify VerifyDiskSpace
(
   DriveToVerify: C,
   MinimumFreeGB: 20
);
```
Because the PowerShell script has the [Augmented Help Comments](/docs/otter/scripting-in-otter/otter-scripting-augmented-help) blocks of `AHDESIREDVALUE`, `AHCURRENTVALUE`, and `AHVALUEDRIFTED`, Otter can interpret values differently.
* Desired Value, which in our example is "at least 20GB"
* Current Value, which might be "25GB"
* Drift Dectected, which would be making sure that 25 > 20

Like the single-hot fix example, checking drives may be a bit over simplified for ordinary usage.

## Collecting Multiple Configurations (Advanced Usage)
A single PowerShell script can instruct Otter to store multiple pieces of configuration that were collected. This can be accomplished by declaring multiple [Augmented Help](/docs/otter/scripting-in-otter/otter-scripting-augmented-help) blocks.

You can further control how PSVerify collects and compares configuration through the use of `AHDESIREDVALUE`,` AHCURRENTVALUE`, and `AHVALUEDRIFTED` Augmented-Help comments.

For example, your desired configuration may be that the "C" drive has at least 20 gigabytes free. It doesn't matter *how much* free space (21GB or 210GB free), just that it's at least 20GB. 

This is a relatively simple PowerShell statement, and with some Augmented-Help blocks, Otter can interpret the results better than a simple $true/$false.
```PowerShell
<# 
.DESCRIPTION
Verifies that an hdars server has the required and expected configuration

.AHCONFIGKEY 
FreeSpace

.AHDESIREDVALUE 
$MinimumFreeGB

.AHCURRENTVALUE 
$ActualFreeGB

.AHVALUEDRIFTED 
$InsufficientFreeSpace

.AHCONFIGTYPE
HotFix

.AHCONFIGKEY 
$HotFixIDToCheck

.AHCURRENTVALUE 
$HotFixInstalled

#>

# An hdars-server needs to have at least 20GB of free space on the "C" drive
$MinimumFreeGB = 20
$DriveToVerify = "C"
$ActualFreeGB = [int]((Get-PSDrive $DriveToVerify).Free / (1024 * 1024 * 1024))
$InsufficientFreeSpace = $MinimumFreeGB -gt $ActualFreeGB

# The HotFix KB4532938 must be installed as well
$HotFixIDToCheck = "KB4532938"
$HotFixInstalled = !!(Get-HotFix -Id  $HotFixIDToCheck) 2> $null

if (!$InsufficientFreeSpace) {
    Write-Warning "Free space is less than $MinimumFreeGB"
}
if (!$HotFixInstalled) {
    Write-Warning "HotFix $HotFixIDToCheck Not installed"
}
```
This script could then show two different configuration items in Otter.

![](/resources/docs/Collecting-Multiple-Configurations.png){height="" width=""}