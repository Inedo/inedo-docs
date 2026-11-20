---
title: "Automated Checks"
order: 3
---

Automated checks are customizable and [extensible rules](/docs/buildmaster/reference/extensions) that must be satisfied before a build is allowed to deploy to a specific stage in a CI/CD pipeline. There are several built into BuildMaster.

| Extensible Feature | Description |
| --- | --- |
| Manual Approval | Requires a user or member of a group to approve and enter a comment. |
| Custom Tool | Executes a custom tool via the command line on the BuildMaster server and checks the exit code. |
| File in Artifact | Verifies that a file exists in an artifact before a promotion to the pipeline stage. |
| Git Branch | Ensures the build is using a specific branch or pattern. |
| Other Pipeline Status | Ensures a pipeline stage of another application has been completed. |
| Testing Requirements | Validates that tests recorded in the previous pipeline stage either all passed or none failed. |
| Issues Status Requirements | Prevents builds from being deployed to a particular stage unless all issues are closed. |
| Packages / Dependencies | Prevents builds from being deployed when there are open issues or unstable (pre-release) versions on Packages & Dependencies. |
| Required Variable Values | Ensures a specified variable value is set before promotion to the pipeline stage. |
| Deployment Window | Restricts deployments to specific days of the week and times. |

All automated checks configured for a stage must be met before a build can be deployed to it. If the pipeline is set to automatically advance, the build will move to the next stage as soon as its checks are satisfied. A Manual Approval check will pause automatic promotion until someone approves it.

For example, before deploying to a production stage you might require:

| Check | Example |
| --- | --- |
| Manual Approval | A tester signs off on the build |
| Git Branch | The build was created from a `release/*` branch |
| Deployment Window | Deployment occurs early Sunday morning, during off-hours |
| Packages / Dependencies | No unstable (pre-release) or vulnerable packages were used |

Users with the appropriate permissions can force-deploy a build that hasn't met its checks.

## Configuring Automated Checks

You can view the automated check options by selecting "view pipeline" on the Build Overview tab and then selecting "Add a stage requirement" under the relevant pipeline stage.

![Pipeline Stages](/resources/docs/BuildMaster-PipelineStagesv2.png){height="" width="70%"}

You can then choose the desired automated check.

![Extensible Features](/resources/docs/BuildMaster-Pipeline-StageRequirement.png){height="" width="70%"}

Automated checks can be viewed under the relevant pipeline stage on the Build Overview tab.

![Extensible Features](/resources/docs/BuildMaster-Pipeline-AutomatedCheck.png){height="" width="70%"}

## Creating Custom Automated Checks

If the built-in checks don't cover your requirements, you can create your own using the [Inedo SDK](/docs/buildmaster/reference/extensions). For simpler cases, the Custom Tool check lets you run any command-line program and use its exit code as the result.

## Automatic Approval Records

When an automatic check is verified, the following information is recorded:

* **State (Met, Not Met, Not Required)** - for example, if there are no issues associated with the release, the requirement would be Not Met, whereas the requirement would be Met only if all issues associated with the release were Resolved
* **Description** - a text description of the approval conditions; for example, "3/8 issues are resolved"
* **Date/Time** - a timestamp of when the approval was run

