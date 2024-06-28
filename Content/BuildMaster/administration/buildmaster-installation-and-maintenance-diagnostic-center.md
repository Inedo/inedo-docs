---
title: "Diagnostic Center"
order: 8
---

The diagnostic center is designed to diagnose known issues in BuildMaster and contains log messages that would not otherwise be captured by other subsystems. For example, issue synchronization occurs in the context of an execution that maintains its own logging for diagnostic purposes. However, if for some reason an error occurs outside of the synchronization (e.g., a required BuildMaster extension was uninstalled), a message would be logged to the diagnostic center. At this point, only the last 1000 messages are kept, and if more are written, the oldest messages are deleted.

### Message Levels

By default, only messages with warning or error level are logged. If more verbosity is required, the level can be changed from the Diagnostic Center page, or by setting the `Diagnostics.MinimumLogLevel` value to one of the following:

*   0 - <span style="color:grey"> *debug*</span>
*   10 - <span style="color:black"> *information*</span>
*   20 - <span style="color:orange"> *warning*</span>
*   30 - <span style="color:red"> *error*</span>

Any specified value implicitly includes all higher levels. The debug setting should only be used when a known problem is diagnosed, as it can affect system performance.

::: (ERROR)
Prior to BuildMaster v6.0, the minimum log level was configured in the `Web_appSettings.config` and `App_appSettings.config` files by adding the following key value pair to the appSettings node: `<add key="Core.MinimumLogLevel" value="20" />`
:::