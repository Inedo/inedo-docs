---
title: "Creating and Publishing Packages"
order: 7
---

Romp is invoked as follows:

```
romp «command»
```

The **«command»** is one of the following:

*   [`pack`](#pack) - Creates a Romp package from the specified directory.
*   [`publish`](#publish) - Publishes the specified package to a package source.

### Pack

```
romp pack «directory-name» \[«output-file-name»\] \[--overwrite\]
```

If the directory does not represent a valid Romp package, an error is raised and the package is not created. This behavior may be overridden with the `--force` flag.

The package will be written to the specified `fileName` or one constructed by the `packagename`

#### Arguments and Flags

|  |  | | 
| --- | --- | --- |
| `directory-nameR` | positional | An absolute or relative path of a directory to be created. This can be the current working directory, specified with a single dot. |
| `output-file-name` | positional | The name of the output file. This can be a relative or absolute path. If it does not end in `.upack`, an error will be raised unless `--force` is specified.  <p> If nothing is specified, a file is placed in the current working directory with a name created from the metadata based on the package name and version. |
| `overwrite` | flag | If specified, the output file name is overwritten if it already exists. Otherwise an error is raised. |


An "`R`" denotes a required property. When an object is not specified for a variable, a string indicates a required text variable, while a null represents a required text variable without a value.

### Publish

```
romp publish «file-name» \[--source=«feedurl»\] \[--authentication=«api-key-or-username-password»\]
```

#### Arguments and Flags


|  |  |  |
| --- | --- | --- |
| `file-nameR` | positional | The relative or absolute path to a Romp package file. |
| `source` | value | See [Common Package Source Arguments](/docs/executionengine/romp-overview/romp-cli-reference/romp-command-line-common-configuration). |
| `authentication` | value | See [Common Package Source Arguments](/docs/executionengine/romp-overview/romp-cli-reference/romp-command-line-common-configuration). |


An "`R`" denotes a required property. When an object is not specified for a variable, a string indicates a required text variable, while a null represents a required text variable without a value.