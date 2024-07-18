---
title: "Augmented Help Reference"
order: 6
---

Script authors often use comment headers to describe what a script does and how to use it (i.e., the script's arguments). Many scripting languages have a convention for formatting these headers so that they can be read and used by various utilities.

For example, PowerShell has [Comment-Based Help](https://blog.inedo.com/powershell/what-is-comment-based-help) and provides information about parameters using this format:

```
.PARAMETER drive_letter
Drive letter to perform the operation on, such as "A:", 
"D:", etc.; this is optional, and "C:" will be used if none 
is specified
```

If your PowerShell script uses these conventions, BuildMaster can automatically create a GUI for scripts with input forms for your script within OtterScript's visual editor and display information and help text for users who will execute it.

BuildMaster can also read [Python Script Docstring](https://blog.inedo.com/python/8-ways-improve-python-scripts) and display a GUI using the provided parameter information.

## What is Augmented Help?
Conventions such as PowerShell's comment-based help and Python's docstrings, while a good start, rely on a free-text description to give the user instructions on how to use the script. This can often lead to confusion or errors when the script is run—especially if the description isn't entirely clear or if the person running the script has made a mistake.

*Augmented Help* is an Inedo-specific commenting format that you can use in your script's comment headers. It's similar to existing commenting conventions and, when used, allows BuildMaster to generate a better user interface with input validation on the script.

Following the `drive_letter` example from earlier, the *Augmented Help*-version of the PowerShell `.PARAMETER` block would look like this:

```
.AHPARAMETER drive_letter(default="C:",format="[A-Z]:")
Drive letter to perform the operation on
```
It is nearly the same as the PowerShell `.PARAMETER` block, but instead of relying on the user to read the description, BuildMaster will generate a textbox with
* default  value of `C:`
* restricted values of `[A-Z]:`, which is a regex that allows values like `A:`, `B:`, or `Z:`


## AHParameter: Augmented Help Parameters

An `AHParameter` (i.e., Augmented Help Parameter) describes a parameter to the script that will be used as an input variable, output variable, environment variable, or as part of the commandline arguments. 

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
| `input` | parameter value will be injected as a variable into the script, prior to being run |
| `output` | parameter value will be captured at the end of the script, and set in OtterScript |
| `environment` | parameter name/value should be set as an environment variable before running the script |
| `args` | parameter value will only be used to format the argument string |
| `sensitive` | display the input as a password box and avoid logging the value when possible |
| `text` | the parameter value should be text  |
| `bool` | the parameter value should be restricted to true or false |
| `switch` | same as `bool`, except when used in an argument string, the parameter value will be `--param_name` |
| `list` | the parameter value should be an OtterScript list |
| `map` | the parameter value should be an OtterScript map |

Usage notes:
* If you don't specify parameter usage (input, output, environment, args), input will be assued.
* You can't specify an unsupported parameter usage (e.g., `args` for PowerShell).
* You can only specify one type of restriction (bool, switch, list, map)
* You can't specify an unsupported type (e.g., `switch` for OtterScript)


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

If not specified, the default behavior is to take all Ah parameters marked as `input` and separate them with a space. Example:

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