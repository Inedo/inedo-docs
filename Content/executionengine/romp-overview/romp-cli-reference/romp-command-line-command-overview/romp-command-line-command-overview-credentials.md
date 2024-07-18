---
title: "Credentials"
order: 7
---

Romp is invoked as follows:

```
romp «command»
```

The **«command»** is one of the following:

*   [`credentials list`](#list) - Displays all credential names and types.
*   [`credentials display`](#display) - Displays all properties of the specified credential name.
*   [`credentials store`](#store) - Store encrypted resource credentials in Romp's database that can be used from plans.
*   [`credentials delete`](#delete) - Deletes the specified credential.

### Credentials List

```
romp credentials list
```

Lists all stored credentials.

### Credentials Display

```
romp credentials display «name» [--show-hidden]
```

#### Arguments and Flags


|  |  |  |
| --- | --- | --- |
| `show-hidden` | flag | If `--show-hidden` is specified, the hidden values are displayed  <p><p> If `secureCredentials` is configured, using `--show-hidden` will result in an error |


### Credentials Store

```
Romp credentials store «qualifiedName»
```

The «qualifiedName» argument is the same format used by Otter. If `secureCredentials` is configured, this command must be executed interactively.

### Credentials Delete

```
Romp credentials delete «qualifiedName»
```

If `secureCredentials` is configured, this command must be executed interactively.