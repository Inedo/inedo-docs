---
title: "Romp Configuration"
order: 7
---

Romp is invoked as follows:

```
romp «command»
```

The **«command»** is one of the following:

*   [`config list`](#list) - Displays all configuration values after cascading all configuration files.
*   [`config export`](#export) - Exports the current configuration to a configuration file.
*   [`config set`](#set) - Sets a configuration value in the local configuration file to the specified value.
*   [`config delete`](#delete) - Deletes a configuration value in the local configuration file.

### Config List

```
romp config list
```

#### Arguments and Flags

This command has no additional arguments or flags.

### Config Export

```
romp config export [«file-name»] [--overwrite]
```

This will be a valid JSON file; see [configuration parameters](/docs/executionengine/romp-overview/installing-configuring-and-maintaining/romp-installation-configuration) for the format.

#### Arguments and Flags


|  |  |  |
| --- | --- | --- |
| `file-name` | positional | The relative or absolute path to a file to export. The default value is romp.json in the current working directory. |
| `overwrite` | flag | If specified, the output file name will be overwritten if it already exists. Otherwise, an error is raised. |



### Config Set

```
romp config set «key» «value»
```

See [configuration parameters](/docs/executionengine/romp-overview/installing-configuring-and-maintaining/romp-installation-configuration) for a list of valid keys and values.

#### Arguments and Flags


|  |  |  |
| --- | --- | --- |
| `key` | positional | The name of the key to set. |
| `value` | positional | The value to set.  <p><p> When specifying a value other than a string, such as the rafts argument, it must be a valid JavaScript object (either JSON or Array) |



### Config Delete

```
Romp config delete «key»
```

See [configuration parameters](/docs/executionengine/romp-overview/installing-configuring-and-maintaining/romp-installation-configuration) for a list of valid keys and values.

#### Arguments and Flags


|  |  |  |
| --- | --- | --- |
| `key` | positional | The name of the key to set. |
