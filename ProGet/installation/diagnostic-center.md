---
title: Diagnostic Center
subtitle: Diagnostic Center
sequence: 300
keywords: proget,diagnostics
---

The diagnostic center is intended to diagnose known issues in ProGet ranging from system errors to feed errors that otherwise would not be captured by other subsystems. For example, feed replication occurs in the context of an execution, which maintains its own logging for diagnostic purposes. However, if there were some reason an execution failed outside the scope of replication, a message would be logged in the diagnostic center. At this time, only the latest 1000 messages are preserved and if more are written, the oldest messages are purged.

### Message Levels {#message data-title="Message Levels"}

By default, only messages with warning or error level are logged. If more verbosity is required, the level can be changed from the
Diagnostic Center page, or by setting the value of the `Diagnostics.MinimumLogLevel` value to one of the following values:

{.docs}
- 0 - <span style="color: #bbb;">`debug`</span>
- 10 - `information`
- 20 - <span style="color: #ee9d66;">`warning`</span>
- 30 - <span style="color: #de5948;">`error`</span>

Any value supplied will implicitly include any higher levels. The debug setting should only be used when diagnosing a known issue as it can affect performance of the system.

:::attention {.technical}
Prior to ProGet v5.0.11, the minimum log level was configured in the `Web_appSettings.config` and `App_appSettings.config` files by adding the following key value pair to the appSettings node:
`&lt;add key="MinimumLogLevel" value="20" /&gt;`
:::
