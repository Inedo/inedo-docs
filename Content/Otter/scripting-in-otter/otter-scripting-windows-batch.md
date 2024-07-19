---
title: "Windows Batch (.bat) Scripting"
order: 5
---

Windows batch scripts can simplify routine or repetitive tasks using the Windows command line.  Batch is an old technology that has been superseded by PowerShell, but still tends to be easier to work with.  Because batch scripts have been around for so long, there are many old scripts that are still in use.  You have the option to [convert your batch scripts to PowerShell](https://blog.inedo.com/powershell/convert-batch), but that may not always be straightforward or worthwhile.

As of v3.0.20, Otter supports executing .bat files and has added [augmented help "comment headers"](/docs/otter/scripting-in-otter/otter-scripting-augmented-help) to display a UI around them.

## Prerequisites
Batch scripts only work against Windows Servers and require a Windows Server to be added in Otter.  

The only prerequisite is to have v1.14.0+ of the Scripting extension installed in Otter v3.0.20+.

## Uploading Batch scripts to Otter
The easiest way to execute a batch script in Otter is to start by uploading an existing script. Just click the "Add Script" button on the Scripts page and select "Upload". Your script will then be available to be used either directly or from an OtterScript using the `BATCall` operation.
 
### Parameters and Batch files
Batch scripts support the use of parameters (arguments) and environment variables.  

Parameters are passed in via positional command line arguments in the order the parameters are specified.  These arguments can then be accessed in the script using %1, %2, ...%n.

Environment variables can be accessed in the script using `%variable_name%`.

### Augmented Help with Otter
[Augmented help](/docs/otter/scripting-in-otter/otter-scripting-augmented-help) is an Inedo-specific commenting format that describes what the script does, how to use the script's arguments, and adds a custom UI around executing the batch script. 

Otter supports the following augmented help features:
- AhDescription 
   - A text description for the batch script
   -  e.g. `REM AhDescription: This script will do x, y, z`
- AhParameters
   - Describes one or more parameters to the script that will be passed as an argument when executing the script
   - e.g. `REM AhParameter: interface_name (text): name of the interface to update`
- AhArgsFormat
   - Describes how the parameters are passed as arguments to the script.
   - When AhArgsFormat isomitted, scripts will pass the parameters as arguments in the order the arguments are defined
   - e.g. `REM AhArgsFormat: $parameter1 $parameter2 $parameter3`

## Executing Scripts
Batch scripts can be ran directly, as a job template, or in OtterScript using the `BATCall` operation. If you use augmented help, you'll get descriptions for script arguments when running the script, creating a job template, and when editing OtterScript in visual mode.

### Example: Batch Script to release and renew a DHCP IP Address

```batch
REM AhDescription: Release and Renew the IP Address of a local computer

ipconfig /release
ipconfig /renew
```

You can then run it in your OtterScript using:
```otter
BATCall ReleaseRenewIpAddress.bat;
```

### Example: Batch Script to add a directory to the system PATH

```batch
REM AhDescription: Add directory to the system PATH
REM
REM AhParameter: dir: Directory to add the path

setx path "%PATH%;%1" /M
```

You can then run it in your OtterScript using:
```otter
Scripting::BATCall AddToSystemPath.bat
(
    Parameters: %(dir: c:\temp)
);
```

### Example: Batch Script to set a static IP Address

```batch
REM AhDescription: Sets the static IP address of "Local Area Connection"
REM 
REM AhParameter: ip_address
REM AhParameter: subnet_mask (default="255.255.255.0")
REM AhParameter: dns_address

netsh interface ip set address "Local Area Connection" static %1 %2 %3 1
```

You can then run it in your OtterScript using:
```otter
Scripting::BATCall SetStaticIpAddress.bat
(
    Parameters: %(ip_address: 192.168.0.1, subnet_mask: 255.255.255.0, dns_address: 192.168.0.1)
);
```

## Technical Implementation
Otter will first write your script to a temporary directory, then it will use the Windows command line to execute your scripts via `cmd.exe /c <<file-name>> <<arguments>>`, and will then remove the temporary file after. 

When using [augmented help](/docs/otter/scripting-in-otter/otter-scripting-augmented-help), the AhParameters will be passed in order via the command line arguments.  For example, if you have parameters `$dir1` and `$dir2`, these will be passed to the script as `cmd.exe /c example.bat $dir1 $dir2`.

## FAQ

### What about .cmd files?
Historically (Windows 9x and earlier) there were some important differences between .bat and .cmd, but now they are the same.  Both are run by cmd.exe, and it's entirely a naming preference.  Using .bat is more popular because it's closer to "batch file".  

Otter uses .bat as extension for these scripts and when you upload a .cmd script, it will automatically be saved as a .bat script.

### Why are my arguments out of order?
By default, Otter will pass the AhParameters in the order specified as arguments to the batch script.  If these arguments are passed in using the wrong order, you will need to specify a custom `AhArgumentFormat`.  

Example: `REM AhArgsFormat: $ip_address $subnet_mask dns_address`
