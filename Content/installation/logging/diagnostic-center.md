---
title: "Diagnostic Center"
---

:::(Internal)
/docs/proget/administration/proget-installation-diagnostic-center
/docs/buildmaster/administration/buildmaster-installation-and-maintenance-diagnostic-center
/docs/otter/administration-maintenance/otter-installation-diagnostic-center
/docs/installation/logging/various-ceip-overview

:::

The Diagnostic Center can be found under the Administration section in BuildMaster, ProGet, and Otter. It displays system messages that may help you troubleshoot a recent problem that you've encountered in the product, such as a "500 Server Error" or other unexpected messages.

:::(Info)
This feature is not intended for proactive health monitoring. When you see "error" or "warning" messages listed, it doesn't necessarily mean that there's a problem or anything to worry about.
:::


Many of the messages will contain additional diagnostic information that may help our support engineers troubleshoot a problem, and you may be asked to copy/paste a "stack trace" that's displayed.


## Deleting & Purging Messages

The Diagnostic Center is intended to help troubleshoot *recent* or *ongoing* problems. As such, older messages are automatically purged on a nightly basis using the System Cleanup task. There should no more than a thousand or so messages available. 

### Manually Purging the Messages

In the unlikely event that you see an extraordinary number of messages (i.e. tens or hundreds of thousands, or more), your system cleanup job many not be running. If you restart the service, this should solve the problem.

In some cases (in particular, older versions of the products) you may see a SQL timeout error in the cleanup job's error logs. You can just run the `TRUNCATE TABLE [LogMessages]` against the database to clear these.


<a name="ceip"></a>

## Log Collection & Transmission

:::(Warning)
We do not recommend enabling this feature unless you're actively working with one of our support engineers
:::

BuildMaster, ProGet, and Otter can collect and transmit detailed diagnostic data to our servers so that we can diagnose/troubleshoot a specific problem with your instance.


Although no secure data (keys, passwords, user-generated logs, etc) will be transmitted, some information that's readily displayed in the UI (application names, build numbers, etc) is included The data is mostly system load (memory, CPU, etc), performance metrics (web requests, database calls, etc), log messages, and error stack traces.

**This feature used to be called Customer Experience Improvement Program (CEIP)**, as it was originally intended to connect usage telemetry. However, we only use the collected data for troubleshooting specific problems and thus renamed the feature to "Log Collection & Transmission"; it may still be called CEIP in your product.



## Changing Message Level

:::(Warning)
We do not recommend changing this value unless you are directed to by a support engineer.
:::

By default, only messages with warning or error level are logged. If more verbosity is required, the level can be changed from the Diagnostic Center page, or by setting the value of the `Diagnostics.MinimumLogLevel` value to one of the following values:

- 0 - <span style="color: #bbb;">*debug*</span>
- 10 - *information*
- 20 - <span style="color: #ee9d66;">*warning*</span>
- 30 - <span style="color: #de5948;">*error*</span>

Any value supplied will implicitly include any higher levels. The debug setting should only be used when diagnosing a known issue as it can affect performance of the system.


### Feed Message Logging (ProGet Only)

ProGet feed errors are logged by default at the <span style="color: #bbb;">`debug`</span> level. Feed errors can range from 404 Not Found responses to authentication errors to exceptions thrown during a feed endpoint request. You can use the advanced configuration setting `Diagnostics.FeedErrorLogging` can be used to selectively change a feed's error logging level to <span style="color: #ee9d66;">*warning*</span>.

The value of this setting can take one of three forms:
- **Empty.** This is the default, and leaves the existing behavior of all feed error logging being at the <span style="color: #bbb;">*debug*</span> level.
- **`*` (a single asterisk).** This changes all feed error logging to the <span style="color: #ee9d66;">*warning*</span> level.
- **A comma-separated list of feed IDs.** This selectively changes the feed error logging level so that any listed feed uses the <span style="color: #ee9d66;">*warning*</span> level and all other feeds remain at <span style="color: #bbb;">*debug*</span>. Invalid feed IDs in this form are ignored.