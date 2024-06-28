---
title: "Augmented Help Reference"
order: 8
---

Script authors will often use "comment headers" to describe what a script does and how to use the script (i.e. the script's arguments). Many scripting languages have a  convention for formatting these headers so that they can be read and used by various help tools.

For example, PowerShell has [Comment-Based Help](https://blog.inedo.com/powershell/what-is-comment-based-help) and provides information about parameters using this format:

```
.PARAMETER drive_letter
Drive letter to perform the operation on, such as "A:", 
"D:", etc.; this is optional, and "C:" will be used if none 
is specified
```

If your PowerShell script uses these conventions, Otter can automatically [Create a GUI for Scripts with Input Forms](/docs/otter/orchestration-server-automation/otter-create-a-gui-for-scripts-with-input-forms) for your script and display information and help text for users who need to execute it.

Otter also can read [Python Script Docstring](https://blog.inedo.com/python/8-ways-improve-python-scripts), and display a GUI using the provided parameter information.

## What is Augmented Help?
While conventions like PowerShell's Comment-based Help and Python's Docstrings are a great start, they rely on a free-text description to provide instructions to the user on how to use the script. This can often lead to confusion or errors when the script is run - especially if the description isn't perfectly clear, or if the person running the script made a mistake.

*Augmented Help* is an Inedo-specific commenting format that you can use in your script's comment headers. It's similar to existing commenting conventions and, when used, Otter can generate a better user interface with input validation on the script.

Following the `drive_letter` example from earlier, the *Augmented Help*-version of the PowerShell `.PARAMETER` block would look like this:

```
.AHPARAMETER drive_letter(default="C:",format="[A-Z]:")
Drive letter to perform the operation on
```
It is nearly the same as the PowerShell `.PARAMETER` block, but instead of relying on the user to read the description, Otter will generate a textbox with
* default  value of `C:`
* restricted values of `[A-Z]:`, which is a regex that allows values like `A:`, `B:`, or `Z:`


## AHParameter: Augmented Help Parameters

An `AHParameter` (i.e., Augmented Help Parameter) describes a parameter to the script that will be used as an input variable, an output variable, an environment variable, or as part of the commandline arguments. 

Using `AHParameter` is optional, and when a script's comment header defines one or more `AHParameter` items, Otter will use those instead of the convention-based parameters.

### AHParameter Formatting

An `AHParameter` is formatted the same way across scripting languages:

```
param_name(category=..., default=..., optional, args/input/output/environment, sensitive, values="a,b,c,...", text/bool/switch/list/map): description
```

There's a lot of options, and only `param_name` is required.

| Option | Notes |
|-- | -- |
| `optional` | indicates that the parameter is not required |
| `values=...` | a list of acceptable or suggested values for the parameter as a comma-separated list |
| `default=...` | specified the default value of the parameter |
| `category=...` | displays the parameter in its own tab |
| `input` | parameter value will be "injected" as a variable into the script, prior to being run |
| `output` | parameter value will be captured at the end of the script, and set in OtterScript |
| `environment` | parameter name/value should be set as an environment variable before running the script |
| `args` | parameter value will only be used to format the argument string |
| `sensitive` | display the input as a password box and avoid logging the value when possible |
| `text` | the parameter value should be text  |
| `bool` | the parameter value should be restricted to "true" or "false" |
| `switch` | same as `bool`, except when used in an argument string, the parameter value will be `--param_name` |
| `list` | the parameter value should be an OtterScript list |
| `map` | the parameter value should be an OtterScript map |

Usage notes:
* If you don't specify parameter usage (input, output, environment, args), then input will be assumed.
* You can't specify an unsupported parameter usage (e.g. `args` for PowerShell).
* You can only specify one type restriction (bool, switch, list, map)
* You can't specify an unsupported type (e.g. `switch` for OtterScript)

:::(Internal) (Private notes)
## AhArgsFormat : Commandline Arguments
Some scripting languages, like Python or Windows batch, often receive parameters as commandline arguments. 

For example:

```
$ py restconf_setip.py -interface "GigabitEthernet1" -ip "172.23.4.28"
```

This is where `AhArgsFormat` comes in. You set it like this:

```
AhArgsFormat:
    -interface "$interface_name" -ip "$ip_address"
```

If not specified, then the default behavior is to take all Ah parameters marked as `input` and separate them with a space. Example:

```
AhParameters:
    interface_name (string, args): name of the interface to update
    ip_address (string, args): new IP address
    ip_netmask (string, args): new IP netmask
    mock (switch, args): indicates whether the RESTCONF calls will be executed
    XEHOST (string,optional, environment): the host + port for the IOS XE management interface
    XEUSER (string,optional, environment): the username for XE management
    XEPASS (string,optional, environment): the password for XE management
```

Then the default string would be `$interface_name $ip_address $ip_netmask $mock`

:::

## 
## Drift Detection & Remediation
If you want to use your scripts for drift detection and/or remediation, you can specify additional help items that will instruct Otter on how to use your script and record the results.

| Item | Description |
| --- | --- |
| **AHExecMode** | The name of a variable that Otter will set to either `Collect` or `Configure` to help determine which execution mode to use; this is always `Collect` in verify operations (e.g. `PSVerify`)<br/><br/>*Required when [Remediating Drift with PowerShell & PSEnsure](/docs/otter/drift-remediation-configuration-as-code/otter-remediating-drift-with-powershell-psensure)* |
| **AHConfigType** | This is the "configuration type" used by the script, which is a string that identifies the type of configuration collected, such as a file or an IIS Application pool. <br /><br/>*Optional: when not specified, the operation name is used (e.g. PSVerify, PyVerify, etc.) is used.* |
| **AHConfigKey** | This is the "configuration key" used by the script, which is a string that uniquely identifies a specific type of configuration collected. It's like a file name (a file is uniquely identified by its name), or an IIS Application name pool (an application pool is unqiuely identified by its name). <br /><br /> *Optional: When not specified, the name of the script is used.* |
| **AHDesiredValue** | This is what you wish the configuration value to be. <br/><br/>*Optional: When not specified, the true value (e.g. `$true` in PowerShell) is used* |
| **AHCurrentValue** | This is the actual value of the configuration. <br /><br />*Optional: When not specified, the script's return (output) is used.* |
| **AHValueDrifted** | This is an indicator as to whether the value is considered drifted. <br /><br />*Optional: When not specified, it's a basic comparison of the desired and current values.* |