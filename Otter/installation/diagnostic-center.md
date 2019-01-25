---
title: Diagnostic Center
subtitle: Diagnostic Center
keywords: otter,diagnostics
---

The diagnostic center is intended to diagnose known issues in Otter and contains log messages that otherwise would not be captured by other subsystems. For example, infrastructure synchronization occurs in the context of an execution, which maintains its own logging for diagnostic purposes. However, if there were some reason an execution failed outside the scope of infrastructure synchronization, a message would be logged in the diagnostic center. At this time, only the latest 1000 messages are preserved and if more are written, the oldest messages are purged.

### Message Levels

By default, only messages with warning or error level are logged. If more verbosity is required, the level can be changed from the Diagnostic Center page, or by setting the value of the `Diagnostics.MinimumLogLevel` value to one of the following values:

- 0 - <code style="color: #bbb;">debug</code>
- 10 - <code>information</code>
- 20 - <code style="color: #ee9d66;">warning</code>
- 30 - <code style="color: #de5948;">error</code>

Any value supplied will implicitly include any higher levels. The debug setting should only be used when diagnosing a known issue as it can affect performance of the system.

:::attention {.technical}
Prior to Otter v2.0.8, the minimum log level was not configurable and was set to "warning".
:::
