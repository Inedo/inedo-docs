---
title: Service Administration
keywords: otter,service
---

The Otter Service is key component of Otter's [architecture](/docs/otter/installation/architecture), and is what actually runs your [plans](../../core-concepts/plans) using the [execution engine](/docs/otter/execution-engine/overview). It's a standard [Windows Service Application](https://msdn.microsoft.com/en-us/library/windows/desktop/ms685141(v=vs.85).aspx), and may be managed and [configured](#configuration-options) using the Windows Service Manager or `sc.exe` as you see fit.

### Managing the Service from the Otter Web Application

In addition to stopping and starting the Otter Service, you can see the service's live logs; this may be helpful when diagnosing problems or working with Inedo's support team to track down unexpected behavior or bugs.

![Otter Administration Services](/resources/documentation/otter/otter-service.png){.screenshot}

You can also manually trigger the task runners that the service would otherwise routinely execute. This should only be needed in debugging purposes, as the Web Application will trigger them in response to certain actions (such as editing a plan), and they are always executed on service start.

{.docs}
- **ServerCheckerTimedExecuter** - this runs every hour, performs a lightweight handshake on each remote agent, and upgrades the agent as needed
- **JobDispatcherTimedExecuter** - this runs every minute, checks for scheduled [jobs](/docs/otter/core-concepts/jobs), and executes those jobs as needed
- **RoutineConfigurationTimedExecuter** - this runs every hour (you can change this with `RoutineConfigurationExecutionThrottle` in All Settings), and executes configuration plans for all severs
- **UpdateCheckerTimedExecuter** - this runs every six hours (you disable this with `Updates.PerformUpdateChecks` in All Settings), and communicates with inedo.com for an updated version of the sofftware

### Otter Service Configuration {#configuration-options}

By default, the Otter Service is named `INEDOOTTERSVC`, runs under the [NetworkService](https://msdn.microsoft.com/en-us/library/windows/desktop/ms684272(v=vs.85).aspx) account, and is granted Read, Write, ListDirectory privileges to the following paths:

{.docs}
- ExecutionTempPath
- ExtensionsPath
- ExtensionsTempPath

If you choose to configure this as a [custom domain account](/support/kb/1077), it will be important to grant these same privileges.
