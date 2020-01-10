---
title: Issue Tracking
subtitle: Azure DevOps Issue Tracking (Azure Boards)
sequence: 400
keywords: buildmaster, azure-devops, issue-tracking
show-headings-in-nav: true
---

BuildMaster is designed to continuously deliver your applications and components from source code to production and to help everyone track the changes made to each release along the way. By integrating BuildMaster with Azure DevOps, you can help developers, testers, and business analysts work closely together by providing shared visibility into changes they're working on and using automation to facilitate immediate feedback. Ultimately this will help to

{.docs}
- Reduce production errors by preventing deployments without fully tested issues
- Save time by eliminating tedious, manual status changes on issues
- Give testers more information about where their changes are deployed to
- Quickly identify the issues and changes that are blocking a release
- Create fully automated deployments using issue statuses as checks and balances

## Connecting to Azure DevOps {#integrating data-title="Integrating Azure DevOps with BuildMaster"}

Before connecting Azure DevOps issues with BuildMaster, you need to integrate Azure DevOps with your BuildMaster instance. To integrate with Azure DevOps, [install the Azure DevOps extension](/docs/buildmaster/integrations/azure-devops#extension), and if necessary, [configure authentication](/docs/buildmaster/integrations/azure-devops#authentication).

## Associating Issues with BuildMaster Releases {#issue-source data-title="Associating Issues"}

To associate issues with an Azure DevOps [issue source](/docs/buildmaster/verification/issue-tracking#issues-sources), the following fields need to be configured:

{.docs}
 - **Project** - should be the name of the project
 - **Iteration path** - used to associated work item versions/sprints with release numbers in BM (commonly something like  `Sprint $ReleaseNumber`)
 - **Closed states** - denotes the comma-separated work item statuses or board lanes that indicate to BuildMaster that an issue is closed, with the default set to `Resolved,Closed,Done`
 - **Custom WIQL** - for advanced users, you can use [Work Item Query Language](https://docs.microsoft.com/en-us/azure/devops/boards/queries/wiql-syntax) to associate issues e.g. `SELECT [System.Id] FROM WorkItems WHERE [System.TeamProject] = '$ApplicationName' AND [System.IterationPath] UNDER '$ReleaseNumber'`

### Work Item vs. Boards View

All "issues" stored within Azure Boards are implemented as work items under-the-hood. This means that BuildMaster can associated issues whether your organization is using the work item view or the Boards view in Azure DevOps. For example, in a Kanban style workflow using cards and columns, an Azure DevOps work item is drag/dropped into a different lane. This lane is effectively the same as the "state", and can be referenced in custom WIQL by `[System.State]`.

### Variable and Issue Sources

BuildMaster will automatically and periodically perform one synchronization for each active release in the system. This means that a release is always "in context", and you can use any of the [variable functions](/docs/buildmaster/reference/functions) that rely on a release to be in context: [`$ApplicationName`](/docs/buildmaster/reference/functions/applications/applicationname), [`$ReleaseNumber`](/docs/buildmaster/reference/functions/releases/releasenumber), [$ReleaseName](/docs/buildmaster/reference/functions/releases/releasename), [`$ReleaseNumberPart()`](/docs/buildmaster/reference/functions/releases/releasenumberpart), etc.

You can also use any [configuration variables](/docs/buildmaster/administration/configuration-variables) that are accessible from a release context: release, application, application-group, or system.

### Syncing Issues in BuildMaster

BuildMaster will also periodically synchronize issues from Azure Boards to its own, built-in issue tracking system. Consider this a fail-over, an additional place to store issues. You can view these by going to the "Issues" tab on the application navigation bar.

This issue source tracks the following information about each issue:

| BuildMaster Term | Azure Boards Field | Description |
|--|--|--|
| Release | Iteration Path | a specific [release](/docs/buildmaster/releases/overview) of a [BuildMaster application](/docs/buildmaster/administration/applications) |
| Issue ID | ID | a human-readable identifier, such has HDARS-1281, sometimes referred to as the "visible ID" |
| Type | Type | the kind of work item (e.g. Epic, Issue, Task) |
| Status | State | the issue's status (e.g. To Do, Doing, Done) |
| Closed | - | a true/false value that indicates the issue is "closed", since Azure Boards does not distinguish open vs. closed, use the "closed states" property of the issue source |
| Title | Summary | a brief description of the issue, such as "FIX: Account Status may be incorrect" |
| Description | Description | a longer description of the issue, containing details |
| URL | URL | URL directly linking to the issue in the tool, so that it can be clicked on from the BuildMaster user interface |

### Manual Synchronization

See [Manual Synchronization](/docs/buildmaster/verification/issue-tracking) for details on manually refreshing issues for an issue source.

## Blocking Deployments with Untested/Unresolved Changes {#blocking-deployments data-title="Blocking Deployments"}

Once you've connected BuildMaster to Azure DevOps, the next step should be ensuring that releases with open or unresolved work items aren't deployed. This is an incredibly important part of "pushing left," making sure that problems are caught earlier in the CI/CD process where they are cheaper and faster to repair and are less damaging to the business.

This can be done configuring one of two [automated checks](/docs/buildmaster/verification/pipelines/approvals-and-gates/automated-checks) on your [release pipeline](/docs/buildmaster/verification/pipelines) that will prevent builds from being deployed to a particular stage unless all issues are closed or in a specific board lane. You can also permit builds to move forward after a certain status such as "Resolved" appears. Prior to deployment, these approvals will evaluate each issue associated with the release and block the deployment if the approval is not met or is unresolved. For example:

{.docs}
- **Issues in Status**; if you specified a specific status text (such as Resolved or Complete), any work item that doesn't have one of those statuses or exist in that specified lane will cause the approval to be not met
- **Require Issues Closed**; issues still "open" (i.e., with any status other than "closed") will fail to be approved and the build will be blocked from advancing to its next pipeline stage

Like all automatic approvals, a build may be forced into a stage even if work items aren't closed or in the appropriate status. However, this requires a special action and a specific permission to be manually taken by a human actor (i.e., forcing issues forward is *not* an automated process).
