---
title: "Azure DevOps Issue Tracking"
order: 1
---


BuildMaster is designed to continuously deliver your applications and components from source code to production, helping all stakeholders track changes to each release. By integrating BuildMaster with Azure DevOps, you can help developers, testers, and business analysts work closely together by giving them shared visibility into the changes they're working on and using automation to provide instant feedback. Ultimately, this will help

- Reduce production errors by avoiding deployments without full testing
- Save time by eliminating tedious, manual status changes as issues arise
- Provide testers with more information about where their changes are being deployed
- Quickly identify the issues and changes that are blocking a release
- Create fully automated deployments using defect status as a check and balance

## Connecting to Azure DevOps 

Before you connect Azure DevOps to BuildMaster, you must integrate Azure DevOps with your BuildMaster instance. To integrate Azure DevOps, [install the Azure DevOps extension](/docs/buildmaster/tools-service-integrations/buildmaster-integrations-azure-devops#extension) and, if necessary, [configure authentication](/docs/buildmaster/tools-service-integrations/buildmaster-integrations-azure-devops#authentication).

## Associating Issues with BuildMaster Releases

To associate issues with an Azure DevOps [issue source](/docs/buildmaster/modeling-your-applications/buildmaster-applications-issue-tracking#issues-sources), the following fields need to be configured:

 - **Project** - should be the name of the project
 - **Iteration path** - used to associated work item versions/sprints with release numbers in BM (commonly something like `Sprint $ReleaseNumber`)
 - **Closed states** - denotes the comma-separated work item statuses or board lanes that indicate to BuildMaster that an issue is closed, with the default set to `Resolved,Closed,Done`
 - **Custom WIQL** - for advanced users, you can use [Work Item Query Language](https://docs.microsoft.com/en-us/azure/devops/boards/queries/wiql-syntax) to associate issues e.g. `SELECT [System.Id] FROM WorkItems WHERE [System.TeamProject] = '$ApplicationName' AND [System.IterationPath] UNDER '$ReleaseNumber'`

### Work Item vs. Boards View

All issues stored in Azure Boards are implemented as work items under the hood. This means that BuildMaster can assign issues whether your organization uses the work item view or the Boards view in Azure DevOps. For example, in a kanban-like workflow that uses cards and columns, an Azure DevOps work item is dragged/dropped into another lane. This lane is virtually the same as the state and can be referenced in the custom WIQL by `[System.State]`.

### Variable and Issue Sources

BuildMaster will automatically and periodically perform one synchronization for each active release in the system. This means that a release is always in context and you can use any of the [variable functions](/docs/buildmaster/reference/functions) that rely on a release to be in context: [`$ApplicationName`](/docs/buildmaster/reference/functions/applications/applicationname), [`$ReleaseNumber`](/docs/buildmaster/reference/functions/releases/releasenumber), [$ReleaseName](/docs/buildmaster/reference/functions/releases/releasename), [`$ReleaseNumberPart()`](/docs/buildmaster/reference/functions/releases/releasenumberpart), etc.

You can also use any [configuration variables](/docs/buildmaster/otterscript-execution-engine/buildmaster-variables) that are accessible from a release context: release, application, application-group, or system.

### Syncing Issues in BuildMaster

BuildMaster will also periodically sync issues from Azure Boards to its own built-in issue tracking system. Think of this as failover, an additional place to store issues. You can view these by going to the Issues tab in the application's navigation bar.

This issue source tracks the following information about each issue:

| BuildMaster Term | Azure Boards Field | Description |
|--|--|--|
| Release | Iteration Path | a specific [release](/docs/buildmaster/modeling-your-applications/buildmaster-releases) of a [BuildMaster application](/docs/buildmaster/modeling-your-applications/buildmaster-applications-concepts) |
| Issue ID | ID | a human-readable identifier, such has HDARS-1281, sometimes referred to as the visible ID |
| Type | Type | the kind of work item (e.g. Epic, Issue, Task) |
| Status | State | the issue's status (e.g. To Do, Doing, Done) |
| Closed | - | a true/false value that indicates the issue is closed, since Azure Boards does not distinguish open vs. closed, use the closed states property of the issue source |
| Title | Summary | a brief description of the issue, such as "FIX: Account Status may be incorrect" |
| Description | Description | a longer description of the issue, containing details |
| URL | URL | URL directly linking to the issue in the tool, so that it can be clicked on from the BuildMaster user interface |

### Manual Synchronization

See [Manual Synchronization](/docs/buildmaster/modeling-your-applications/buildmaster-applications-issue-tracking) for details on manually refreshing issues for an issue source.

## Blocking Deployments with Untested/Unresolved Changes

Once you have BuildMaster connected to Azure DevOps, the next step should be to ensure that releases with open or unresolved work items aren't deployed. This is an incredibly important part of "pushing left", ensuring that issues are identified earlier in the CI/CD process where they're cheaper and faster to fix, and less damaging to the business.

This can be done configuring one of two [automated checks](/docs/buildmaster/deployment-continuous-delivery/automated-testing-verification-automatic-manual-approvals/buildmaster-ci-cd-testing-and-verification-approvals-automated-checks) on your [release pipeline](/docs/buildmaster/deployment-continuous-delivery/buildmaster-pipelines) that will prevent builds from being deployed to a particular stage unless all issues are closed or in a specific board lane. You can also allow builds to continue after a certain status, such as Resolved, is displayed. Before deployment, these approvals will evaluate any issue associated with the release and block deployment if the approval is not met or unresolved. For example:

- ** Issues in Status**; if you have specified a specific status text (such as Resolved or Complete), any work item that doesn't have one of these statuses or exists in the specified track will result in the approval not being fulfilled
- **Require Issues Closed**; issues that are still open (i.e. with a status other than closed ) cannot be approved and the build will be locked for the next pipeline stage

Like all automatic approvals, a build can be forced into a phase even if the work items aren't closed or in the appropriate status. However, this requires a special action and a specific permission to be manually granted by a human actor (i.e. forcing issues forward is *not* an automatic process).