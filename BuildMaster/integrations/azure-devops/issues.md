---
title: Issue Tracking
subtitle: Azure DevOps (Boards) Issue Tracking
sequence: 400
keywords: buildmaster, azure-devops, issue-tracking
show-headings-in-nav: true
---

BuildMaster is designed to continuously deliver your applications and components from source code to production and to help everyone track the changes made to each release along the way. By integrating BuildMaster with Azure DevOps, you can help developers, testers, and business analysts work closely together by providing shared visibility into changes they're working on and using automation to facilitate immediate feedback. Ultimately this will help to

{.docs}
- Reduce production errors by preventing deployments without fully-tested issues
- Save time by eliminating tedious, manual status changes on issues
- Give testers more information about where their changes are deployed to
- Quickly identify the issues and changes that are blocking a release
- Create fully-automated deployments using issue statuses as checks and balances

## Connecting to Azure DevOps {#integrating data-title="Integrating Azure DevOps with BuildMaster"}

Before connecting Azure DevOps issues with BuildMaster, you need to integrate Azure DevOps with your BuildMaster instance. To integrate with Azure DevOps, [install the Azure DevOps extension](/docs/buildmaster/integrations/azure-devops#extension), and if necessary, [configure authentication](/docs/buildmaster/integrations/azure-devops#authentication).

### Synchronizing Issues to BuildMaster

After connecting to your issue or project tracker, BuildMaster will periodically synchronize to its own, built-in issue tracking system. You can view these by going to the "Issues" tab on the application navigation bar.

This issue store tracks the following information about each issue:

{.docs}
- **Release** - a specific [Release](/docs/buildmaster/releases/overview) of a [BuildMaster Application](/docs/buildmaster/administration/applications)
- **Issue Id** - a human-readable identifier, such has HDARS-1281
- **Type** - the category of issue, such as Bug or Feature
- **Status** - the issue's status, such as Open or Resolved
- **Closed** - a true/false value that indicates the issue is "closed"
- **Title** - a brief description of the issue, such as "FIX: Account Status may be incorrect"
- **Description** - a longer description of the issue, containing details
- **URL** - URL directly linking to the issue in the tool, so that it can be clicked on from the BuildMaster user interface

### Variables and Issue Sources

BuildMaster will automatically and periodically perform one synchronization for each active release in the system. This means that a release is always "in context," and you can use any of the [variable functions](/docs/buildmaster/reference/functions) that rely on a release to be in context: [$ApplicationName](/docs/buildmaster/reference/functions/applications/applicationname), [$ReleaseNumber](/docs/buildmaster/reference/functions/releases/releasenumber), [$ReleaseName](/docs.inedo.com/docs/buildmaster/reference/functions/releases/releasename), [$ReleaseNumberPart()](/docs/buildmaster/reference/functions/releases/releasenumberpart), etc.

You can also use any [configuration variables](/docs/buildmaster/administration/configuration-variables) that are accessible from a release context: release, application, application-group, or system.

## Blocking Deployments with Untested/Unresolved Changes {#blocking-deployments data-title="Blocking Deployments"}

Once you've connected BuildMaster to Azure DevOps, the next step should be ensuring that releases with unclosed or unresolved issues aren't deployed. This is an incredibly important part of "pushing left," making sure that problems are caught earlier in the CI/CD process where they are cheaper and faster to repair and are less damaging to the business.

This can be done configuring one of two [automated checks](/docs/buildmaster/verification/pipelines/approvals-and-gates/automated-checks) on your [release pipeline](/docs/buildmaster/verification/pipelines) that will prevent builds from being deployed to a particular stage unless all issues are closed. You can also permit builds to move forward after a certain status such as "Resolved" appears. Prior to deployment, these approvals will evaluate each issue associated with the release and block the deployment if the approval is not met or is unresolved. For example:

{.docs}
- **Issues in Status**; if you specified a specific status texts (such as Resolved and Complete), any issue that doesn't have one of those statuses will cause the approval to be not met
- **Require Issues Closed**; issues still "open" (i.e., with any status other than "closed") will fail to be approved and will be blocked from moving on

Like all automatic approvals, a build may be forced into a stage even if issues aren't closed or in the appropriate status. However, this requires a special action and a specific permission to be manually taken by a human actor (i.e., forcing issues forward is *not* an automated process).
