---
title: "Configuration Parameters"
order: 4
---


In addition to command-line arguments, Romp uses a json-based configuration file to define runtime behavior. See below for all the items you can configure.

You can control the behavior of the Romp client through the use of configuration parameters. These can be specified in both a `rompconfig.json` configuration file or as a command-line argument.

Because parameters can be defined in multiple places, the values cascade, and is resolved in the following order:

1.    Command-line argument
2.    `rompconfig.json` file in current working directory
3.    `rompconfig.json` file in local data store

This means the command-line argument will override all other levels. For example, to enable verbose output for a package installation, use:

```
> romp install somePackage --log-level=debug
```

## Parameter Reference



| Name | Description |
| --- | --- |
| `cache-packages` | A _Boolean_ that indicates whether packages will be cached. This defaults to _true_.   <p>Note that disabling the package cache may result in a package that cannot be uninstalled, since the cached package is used for the uninstall script and package content manifest. |
| `user-mode` | A _Boolean_ that, when true, indicates whether the local datastore will default to `%user%\.romp` and the local package registry will default as a user-package registry. This defaults to _false_. |
| `default-source` | A _string_ that, when set, will be used to determine which package source is used when no package source is specified. |
| `secure-credentials` | A _Boolean_ which indicates, when true, that credentials must be entered interactively when using the [store](/docs/executionengine/romp-overview/romp-cli-reference/romp-command-line-command-overview/romp-command-line-command-overview-credentials#store) command and may not be displayed with the [display](/docs/executionengine/romp-overview/romp-cli-reference/romp-command-line-command-overview/romp-command-line-command-overview-credentials#display) command. This defaults to _false_. |
| `extensions-path` | A _string_ that contains the full path of a directory containing .upack extension files to load. This defaults to `«localDataStore»\extensions`. |
| `extensions-temp-path` | A _string_ that contains the full path of a directory which contains unpacked cached extensions. This defaults to `«localDataStore»\temp\extensions`. |
| `log-level` | A _string_ that describes the verbosity of Romp's console output. May be: `debug`, `info`, `warning`, or `error`. The default value is `warning`. |
| `proxy` | A string that contains the URL of a proxy server to use for any HTTP requests. |
| `rafts` | An object of rafts that Romp has access to, with each property name containing the raft name, and the value being a string that contains either the directory of a file system raft, or the path to a zip file raft. <p> Note that this property will not cascade.  |

















  
