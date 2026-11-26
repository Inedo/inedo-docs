---
title: "Automated Checks"
order: 3
---


Automated checks are customizable and [extensible rules](/docs/buildmaster/reference/extensions) that must be satisfied before a build is allowed to deploy to a specific stage in a CI/CD pipeline. There are several built into BuildMaster.


For example, if you've created an Issues Closed automatic approval to verify that issues in a particular JIRA project are closed prior to promoting to a stage, BuildMaster won't be notified of a status change.


## Adding Automated Checks


You can view the automated check options by selecting "view pipeline" on the Build Overview tab and then selecting "Add a stage requirement" under the relevant pipeline stage.


![Pipeline Stages](/resources/docs/BuildMaster-PipelineStagesv2.png){height="" width="70%"}


You can then choose from a list of automated checks.


![Extensible Features](/resources/docs/BuildMaster-Pipeline-StageRequirement.png){height="" width="70%"}


Active automated checks can be viewed under the relevant pipeline stage on the Build Overview tab.


![Extensible Features](/resources/docs/BuildMaster-Pipeline-AutomatedCheck.png){height="" width="100%"}

## Extensible Features

The following extensible features are available within BuildMaster.

| Extensible Feature | Description |
| --- | --- |
| Manual Approval | Require a user or member of a group to approve and enter a comment, such as a release manager signing off before a build is deployed to Production. |
| Custom Tool | Run a custom tool via the command line on the BuildMaster server and check its exit code. |
| File in Artifact | Check that a file exists in an Artifact before promoting to the pipeline stage. |
| Git Branch | Make sure the build uses a specific branch, such as main. |
| Other Pipeline Status | Make sure a pipeline stage of another application has been completed. |
| Testing Requirements | Check that tests recorded in the previous pipeline stage either all passed or none failed. |
| Issues Status Requirements | Block builds from being deployed to a particular stage until all issues are closed. |
| Packages / Dependencies | Block builds from being deployed when Packages & Dependencies have open issues or unstable (pre-release) versions. |
| Required Variable Values | Make sure a specified variable value is set before promoting to the pipeline stage. |
| Deployment Window | Restrict deployments to specific days of the week and times. |

These automatic approvals are verified periodically because they can change over time.


## Automatic Approval Records


When an automatic check is verified, the following information is recorded:


* **State (Met, Not Met, Not Required)** - for example, if there are no issues associated with the release, the requirement would be Not Met, whereas the requirement would be Met only if all issues associated with the release were Resolved
* **Description** - a text description of the approval conditions; for example, "3/8 issues are resolved"
* **Date/Time** - a timestamp of when the approval was run

![Approval Record](/resources/docs/BuildMaster-ApprovalRecord.png){height="" width="70%"}
