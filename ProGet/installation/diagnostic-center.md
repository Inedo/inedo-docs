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

### Feed Error Logging {#feeds data-title="Feed Errors"}

ProGet feed errors are logged by default at the <span style="color: #bbb;">`debug`</span> level. Feed errors can range from 404 Not Found responses to authentication errors to exceptions thrown during a feed endpoint request.

Starting with ProGet v5.2.22, the advanced configuration setting `Diagnostics.FeedErrorLogging` can be used to selectively change a feed's error logging level to <span style="color: #ee9d66;">`warning`</span>.

The value of this setting can take one of three forms:

{.docs}
- **Empty.** This is the default, and leaves the existing behavior of all feed error logging being at the <span style="color: #bbb;">`debug`</span> level.
- **`*` (a single asterisk).** This changes all feed error logging to the <span style="color: #ee9d66;">`warning`</span> level.
- **A comma-separated list of feed IDs.** This selectively changes the feed error logging level so that any listed feed uses the <span style="color: #ee9d66;">`warning`</span> level and all other feeds remain at <span style="color: #bbb;">`debug`</span>. Invalid feed IDs in this form are ignored.
