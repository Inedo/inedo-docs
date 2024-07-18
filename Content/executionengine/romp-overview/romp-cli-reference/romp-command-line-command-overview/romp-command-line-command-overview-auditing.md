---
title: "Auditing Local Machine History"
order: 7
---

Romp is invoked as follows:

```
romp «command»
```

The **«command»** is one of the following:

*   [`packages list`](#packages-list) - Displays a list of installed packages.
*   [`jobs list`](#jobs-list) - Displays a list of recent Romp job executions that have been recorded on the server, including their status.
*   [`jobs logs`](#jobs-logs) - Displays the execution logs for a specified job.
*   [`jobs purge`](#jobs-purge) - Purges jobs older than the specified number of days.

### Packages List

```
romp packages list
```

Displays a list of installed packages.

### Jobs List

```
romp jobs list
```

Displays a list of recent Romp job executions that have been recorded on the server, including their status.

### Jobs Logs

```
romp jobs logs «jobId»
```

Displays the execution logs for a specified job. If «jobId» is not specified, the logs for the most recent job are displayed.

### Jobs Purge

```
romp jobs purge «days»
```

Purges jobs older than the specified number of days.