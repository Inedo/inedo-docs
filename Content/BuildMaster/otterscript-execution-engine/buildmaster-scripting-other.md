---
title: "Shell (Bash) & Other Scripting"
order: 5
---

:::(Internal) (TODO)
- Get screenshot/example for something other than Powershell (BAT?)
- improve intro
:::

In addition to [PowerShell (.ps1) Scripting](/docs/buildmaster/otterscript-execution-engine/buildmaster-scripting-powershell), BuildMaster supports Shell (Bash), Windows Batch (.bat), and Python (.py) scripts. 



## Language Support & Prerequisites
BuildMaster runs these scripts on a server that uses an agent. The requirements for this server depend on the script to be executed.

* **Shell (`.sh`)** can only be run against Linux servers
* **Windows Batch (`.bat`)** can only be run against Windows servers
* **Python (`.py`)** can be run against  Windows or Linux servers, provided that Python3 is installed

For Python scripts where BuildMaster cannot detect where Python3 is located, you must create a server-scoped variable in BuildMaster called `$PythonPath`  that contains the full path to Python (or python.exe on Windows).

## Adding Existing Scripts to BuildMaster

The easiest way to add your existing scripts to BuildMaster is to navigate to your application, then Settings > Scripts > Add Script. From there you can either create a new script or upload scripts.

##  Running Scripts in BuildMaster
Once you've added a script to BuildMaster, you can run that script in a [pipeline stage](/docs/buildmaster/deployment-continuous-delivery/buildmaster-pipelines) as a deployment target. 

You can also use [OtterScript](/docs/buildmaster/otterscript-execution-engine/buildmaster-otterscript-overview) to run those scripts on different servers, whether that means targeting servers sequentially, in parallel, or even with branching and interating (loops) logic.

### Using Call Operations to Run Scripts
:::(Internal) (TODO)
Get screenshot/example for something other than Powershell (BAT?)
:::
You can call your scripts using one of the "Call" operations:
* `SHCall` for Shell (Bash)
* `BATCall` for Windows Batch
* `PYCall` for Python

BuildMaster can parse your script for [parameters and inputs](#using-script-parameters-inputs), and provide descriptions for script arguments in the OtterScript visual editor:

### Example: Batch Script to release and renew a DHCP IP Address
If you were to add the following Windows Batch script to BuildMaster...
```batch
REM AhDescription: Release and Renew the IP Address of a local computer

ipconfig /release
ipconfig /renew
```

...you can then run it in your OtterScript using:
```otter
BATCall ReleaseRenewIpAddress.bat;
```

## Inline Execution of Shell and Python Code
The `SHExec` and `PYExec` operations, allow you to insert Shell or Python code directly into your OtterScript. Due to the limited capabilities of Windows batch scripts, there is no `BATExec` operation.

When you run scripts this way, BuildMaster automatically replaces [variables](/docs/buildmaster/otterscript-execution-engine/buildmaster-variables) within the script. In the example above, `$ApplicationName` would be replaced with the name of the application being built or deployed.

### Example: Single line create folder
```OtterScript
SHExec mkdir "/home/user/temp";
```

### Example: Single line create folder using an OtterScript variable
```OtterScript
set $temp_folder_path = /home/user/temp;
SHExec mkdir "$temp_folder_path";
```

### Example: Multi line create folder
```OtterScript
set $temp_folder_path = /home/user/temp;
SHExec  >>
echo "Creating directory $temp_folder_path"
mkdir $temp_folder_path
>>;
```

## Using Script Arguments (Inputs & Outputs)

BuildMaster uses [Augmented Help](/docs/buildmaster/otterscript-execution-engine/buildmaster-scripting-augmented-help) to generate descriptions and prompts for your scripts. 

By adding augmented help comment headers to your script, you can make it easier to find and use your scripts in OtterScript.

### Shell (.sh) Arguments (Inputs & Outputs)
To use Augmented Help with a Shell script, simply add comment lines directly under the shebang (`#!`) line. Each of the lines in this comment header should have the keyword (`AhDescription`, `AhParameter`, etc.), followed by a colon (`:`).

For example, to describe `DiskUsagePercentage.sh`, a script with both an input and output parameter, your script may look like this:

```bash
#!/bin/bash
# AhDescription: This script will find the disk usage percentage
# AhParameter: path_check
# AhParameter: percent_used (output)

echo "Finding disk usage percentage for $path_check..."
percent_used=$(df --output=pcent $path_check | awk 'NR==2{ print $1 }')
```

In this case, `path_check` will be passed as a variable to the script, capturing the value of `percent_used` once the script exits. In OtterScript, you could use this script as follows.

```otterscript
set $disk_path = /dev/sdb;

SHCall DiskUsagePercentage.sh
(
    Parameters: %(path_check: $disk_path, percent_used: percentUsed)
);

if $Compare($TrimEnd($percentUsed, `%), >, 85, true) == true
{
   Log-Error Cannot deploy to $disk_path because it's currently using $percentUsed of its available storage capacity;
   throw;
}
```
Note that, when mapping AhParameters as output variables to OtterScript variables, you must omit the `$` character of the OtterScript variable that is being mapped. In addition, Shell scripts cannot include `exit #` or `return #`.


### Windows Batch (.bat) Arguments (Input Only)
To use Augmented Help with a Windows Batch script, simply add comment lines (i.e., lines starting with `REM`) at the top of the script. Each of the lines in this comment header should have the keyword (`AhDescription`, `AhParameter`, etc.), followed by a colon (`:`).

For example, to describe `SetIP.bat`, you might add the following comment header:
```
REM AhDescription: Sets the static IP address of "Local Area Connection"
REM 
REM AhParameter: ip_address
REM AhParameter: subnet_mask (default="255.255.255.0")
REM AhParameter: dns_address

netsh interface ip set address "Local Area Connection" static %1 %2 %3 1
```
Parameters will be passed in via positional command line arguments in the order the parameters are specified, unless you specify a custom string using `AhArgsFormat`. In Windows Batch script, command line arguments are accessed in script using `%1`, `%2`, ...`%n`.

If your Windows Batch script uses environment variables for input, you can also specify that as follows:
```
REM AhParameter: hdars_path (environment)
```

### Python (.py) Arguments (Inputs & Outputs)

If your Python script has a [Docstring comment header](https://blog.inedo.com/python/8-ways-improve-python-scripts), BuildMaster will use that for parameter inputs.

For example:

```python
"""
This script leverages RESTCONF to updates the IP address on the interface.
 
 Args:
   interface_name (string): name of the interface to update
   ip_address (string): new IP address
   ip_netmask (string): new IP netmask
"""
```
This would then be called in BuildMaster as follows:
```
PYCall UpdateIP.py
(
    Parameters: %(interface_name: HDARIF, ip_address: $NewIP, ip_netmask: 255.255.255.0)
);
```
The three parameters (`interface_name`, `ip_address`, and `ip_netmask`) would be available as variables in the Python script. If you use `AhParameter` insead of `Args`, you can use any of the augmented help options, including output variables.

## Technical Implementation
:::(Internal) (COPY/PASTA NOTE)
This is shared with Otter. Make sure they are in sync
:::
## Shell Scripts
BuildMaster will first write your script to a temporary directory, then it will use the Linux command line to execute your scripts via `./<<file-name>> <<arguments>>`, and will then remove the temporary file after. 

When using augmented help, there are a few differences in the exectution.  
- AhParameters are defaulted to an "input" usage and will be prepended to the begining of the temporary script so they can be used by name (e.g. `$dir1` in the script).
- AhParameters with the "args" usage will be passed in order via the command line arguments. For example, if you have parameters `$dir1` and `$dir2`, these will be passed to the script as `./example.sh $dir1 $dir2`.  
- When including AhParameters with the "output" usage type, the Shell script will be wrapped in a function named `AhScriptWrapper` so variables can be exported. This prevents the use of `return` and `exit` from within the script. Instead, use another "output" AhParameter and handle it using OtterScript.

### Python Scripts
BuildMaster uses the standard Python3 interpreter to execute your scripts. The script is executed using Python's `exec`, allowing it to access variables declared as inputs in the `AhParameters` section of the docstring, and BuildMaster also hooks into Python's logging system, allowing `logging.info/warning/etc` calls in Python to be captured with the appropriate log levels in Otter. Output values are read after `exec` returns.


## Windows Batch
BuildMaster will first write your script to a temporary directory, then use the Windows command line to execute your scripts via `cmd.exe /c <<file-name>> <<arguments>>`, and then remove the temporary file. 

When using augmented help, the AhParameters will be passed in order via the command line arguments.  For example, if you have parameters `$dir1` and `$dir2`, these will be passed to the script as `cmd.exe /c example.bat $dir1 $dir2`.