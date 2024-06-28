---
title: "Package Sources"
order: 7
---

Romp is invoked as follows:

```
romp «command»
```

The **«command»** is one of the following:

*   [`sources list`](#list) - Displays all package sources.
*   [`sources display`](#display) - Displays all properties of the package source name.
*   [`sources create`](#create) - Store encrypted resource credentials in Romp's database that can be used from plans.
*   [`sources delete`](#delete) - Deletes the specified source.
*   [`sources default`](#default) - Sets the specified source as the default.

### Sources List

Romp sources list

### Sources Display

Romp sources display «name» \[--show-hidden\]

#### Arguments and Flags


|  |  |  |
| --- | --- | --- |
| `show-hidden` | flag | If `--show-hidden` is specified, the hidden values are displayed  <p><p> If `secureCredentials` is configured, using `--show-hidden` will result in an error |


### Sources Create

```
Romp sources create «name» «url»
```

The `«qualifiedName»` argument is the same format used by Otter.

If `secureCredentials` is configured, and a password or API key is used, this command must be executed interactively.

### Sources Delete

```
Romp sources delete «name»
```

Deletes the specified package source.

### Sources Default

```
Romp sources default «name»
```

Sets the default package source by writing to the "default-source" value of the current configuration file.