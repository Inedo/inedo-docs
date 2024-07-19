---
title: "Automated Checks"
order: 3
---

You can define automatic checks that must be met before a build may be deployed to a stage. These are an [extensible feature](/docs/buildmaster/reference/extensions), and there are several built into BuildMaster.


| Extensible Feature | Description |
| --- | --- |
| Command Line | Executes a program via command line on the BuildMaster server. |
| File in Artifact | Verifies that a file exists in an Artifact before a promotion to the pipeline stage. |
| Issues Closed | Verifies that all issues are closed before a promotion to the pipeline stage. |
| Issues in Status | Verifies that issues are in a certain status before a promotion to the pipeline stage. |
| Pipeline Stage Complete | Ensures a pipeline stage of another application has been completed. |
| Change Controls Performed | Verifies that change controls have been performed. |
| Change Scripts Run | 	Verifies that all required change scripts have been run successfully. |
| United Tests Passed | Verifies that unit tests have passed in the last execution. |
| Variable | Ensures a specified variable value before promotion to the pipeline stage. |
| Drift Status | Verifies that a specified Otter server or role is in a specified drift status. |

These automatic approvals are verified periodically because they can change over time.

:::(Info)
For example, if you've configured an Issues Closed automatic approval to verify that issues in a particular JIRA project are closed prior to promoting to a stage, BuildMaster won't be notified of a status change.
:::

When an automatic approval is verified, the following information is recorded:

* **State (Met, Not Met, Not Required)** - for example, if there are no issues associated with the release, the requirement would be Not Met, whereas the requirement would be Met only if all issues associated with the release were Resolved
* **Description** - a text description of the approval conditions; for example, "3/8 issues are resolved"
* **Date/Time** - a timestamp of when the approval was run