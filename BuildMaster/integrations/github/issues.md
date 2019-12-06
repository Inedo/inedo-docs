---
title: Issue Tracking
subtitle: GitHub Issue Tracking
sequence: 400
keywords: buildmaster, github, git, issue-tracking
show-headings-in-nav: true
---

BuildMaster is designed to continuously deliver your applications and components from source code to production and to help everyone track the changes made to each release along the way. By integrating BuildMaster with GitHub, you can help developers, testers, and business analysts work closely together by providing shared visibility into changes they're working on and using automation to facilitate immediate feedback. Ultimately this will help to

{.docs}
- Reduce production errors by preventing deployments without fully-tested issues
- Save time by eliminating tedious, manual status changes on issues
- Give testers more information about where their changes are deployed to
- Quickly identify the issues and changes that are blocking a release
- Create fully-automated deployments using issue statuses as checks and balances

## Connecting to GitHub {#integrating data-title="Integrating GitHub with BuildMaster"}

Before connecting GitHub issues with BuildMaster, you need to integrate GitHub with your BuildMaster instance. To integrate with GitHub, [install the GitHub extension](/docs/buildmaster/integrations/github#extension), and if necessary, [configure authentication](/docs/buildmaster/integrations/github#authentication).

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

### Example: Inedo Use Case

On our [public instance of BuildMaster](https://buildmaster.inedo.com/), we have dozens of nearly identical applications configured that are designed to build/deploy the various extensions to our products. We've configured just a single issue source as follows:

|Issue Source<br/>Field|Value|
|-|-|
|Name|`GitHub Issue Source`|
|Run for application|`Extensions` (application group)|
|Credentials|`InedoGitHub`|
|Repository Name|`inedox-$ToLower($ApplicationName)`|
|Milestone|`$ReleaseNumber`|

This issue source will only be active for applications within the [Extensions application group](https://buildmaster.inedo.com/all-applications?applicationGroupId=1) we've configured. Each of the applications in this group are named similarly to the repository; for example, there is a [Subversion application](https://buildmaster.inedo.com/applications/23/) in BuildMaster and a [inedox-subversion repository](https://github.com/inedo/inedox-subversion) in GitHub. The expression `inedox-$ToLower($ApplicationName)` maps these together when the issue source is synchronized.

This issue source will periodically synchronize with GitHub, and we've configured an automatic check that [prevents deployments until all issues are resolved](/docs/buildmaster/builds/tests/unit-tests).

### Variables and Issue Sources

BuildMaster will automatically and periodically perform one synchronization for each active release in the system. This means that a release is always "in context," and you can use any of the [variable functions](/docs/buildmaster/reference/functions) that rely on a release to be in context: [$ApplicationName](/docs/buildmaster/reference/functions/applications/applicationname), [$ReleaseNumber](/docs/buildmaster/reference/functions/releases/releasenumber), [$ReleaseName](/docs.inedo.com/docs/buildmaster/reference/functions/releases/releasename), [$ReleaseNumberPart()](/docs/buildmaster/reference/functions/releases/releasenumberpart), etc.

You can also use any [configuration variables](/docs/buildmaster/administration/configuration-variables) that are accessible from a release context: release, application, application-group, or system.

## Creating Milestones {#creating-milestones data-title="Creating Milestones"}

A milestone is the mechanism used to associate release numbers in BuildMaster with a specific GitHub issue and is intended to be a version number of sorts; it does not need to match the version used by marketing but needs to be uniquely-identifiable so that BuildMaster can use it to apply gating logic in pipeline stages (i.e. all issues closed for *this* release). You can use the `GitHub::Ensure-Milestone` operation at an early stage to ensure that a milestone exists, or could be created at the end of a previous release.

## Closing Milestones {#closing-milestones data-title="Closing Milestones"}

Closing a milestone in GitHub means the version represented by that milestone was "released" so no more issues should be associated with it. To close a milestone at the end of a pipeline, use the `GitHub::Ensure-Milestone` operation with the property: `State: closed`.

## Blocking Deployments with Untested/Unresolved Changes {#blocking-deployments data-title="Blocking Deployments"}

Once you've connected BuildMaster to GitHub, the next step should be ensuring that releases with unclosed or unresolved issues aren't deployed. This is an incredibly important part of "pushing left," making sure that problems are caught earlier in the CI/CD process where they are cheaper and faster to repair and are less damaging to the business.

This can be done configuring one of two [automated checks](/docs/buildmaster/verification/pipelines/approvals-and-gates/automated-checks) on your [release pipeline](/docs/buildmaster/verification/pipelines) that will prevent builds from being deployed to a particular stage unless all issues are closed. You can also permit builds to move forward after a certain status such as "Resolved" appears. Prior to deployment, these approvals will evaluate each issue associated with the release and block the deployment if the approval is not met or is unresolved. For example:

{.docs}
- **Issues in Status**; if you specified a specific status texts (such as Resolved and Complete), any issue that doesn't have one of those statuses will cause the approval to be not met
- **Require Issues Closed**; issues still "open" (i.e., with any status other than "closed") will fail to be approved and will be blocked from moving on

Like all automatic approvals, a build may be forced into a stage even if issues aren't closed or in the appropriate status. However, this requires a special action and a specific permission to be manually taken by a human actor (i.e., forcing issues forward is *not* an automated process).

## Creating and Updating Issues {#creating data-title="Creating Issues"}

Many teams use issue and project tracking tools like GitHub for more than just code changes.

GitHub is flexible and can therefore be used to model virtually any business process, such as deployment tracking. Unlike working in the source code, locating auditing information (e.g., when something was deployed and by whom) does not require a high degree of technical know-how to use, because all information is within the issue itself.

Teams are already familiar with the process of creating new issues, but they want to automate it. BuildMaster lets you model this workflow by automatically creating and linking to issues. This saves the manual effort of creating new issues and also avoids the human errors that manual processes allow.

To create issues for a particular GitHub milestone, use the `GitHub::Create-Issue` operation. To add a comment to an existing issue, use the `GitHub::Create-IssueComment` operation.

### Example: Creating a "Deployment Tracking" Issue  

Some organizations use issues as a way to deployments throughout a release. This way, business and testing teams can see the progress of deployments, report problems on that issue, and be alerted of status changes using GitHub's notification features.

Although GitHub isn't really designed for this, it's generally easier to just automate your existing process instead of forcing a new one. This is easy to do with BuildMaster: you can use an operation to create an issue.

Each integration has different properties, but as an example, here's how you could do this in GitHub: [[ATTN: JOHN]]

````
JIRA::Create-Issue(
	Credentials: Jira7Local,
	Project: HDARS,
	Title: QA Testing Required for $ApplicationName,
	Description: This issue was created by BuildMaster on $Date,
	Type: ReadyForQA,
	JiraIssueId => $JiraIssueId
);

Set-BuildVariable DeploymentIssueId
(
	Value: $JiraIssueId
);
````

[[ATTN: JOHN]] The second operation will then set `$JiraIssueId ` as a build variable, which means it will be not only visible on the build page, but you can also use it in all future deployments on that build.

[[ATTN: JOHN]] You can then use the $JiraIssueId in later operations to automatically [change issue status](/docs/buildmaster/verification/issue-tracking) or [add comments and notes](/docs/buildmaster/reference/operations/issue-tracking/add-comment-to-issues).


### Tip: Use a Variable Renderer for User-friendly Display

You can do this in BuildMaster with [Variable Value Renderers](/docs/buildmaster/administration/value-renderers). These are essentially instructions for how to render variables in the UI that have certain names (such `$JiraIssueId `). Because you can specify HTML in a value renderer, you link directly to your issue tracker. This provides easy navigation for users.
