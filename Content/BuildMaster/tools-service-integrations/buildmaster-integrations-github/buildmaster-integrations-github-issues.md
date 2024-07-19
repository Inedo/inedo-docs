---
title: "GitHub Issue Tracking"
order: 1
---


BuildMaster is designed to continuously deliver your applications and components from source code to production, helping everyone track changes to each release. By integrating BuildMaster with GitHub, you can help developers, testers, and business analysts work closely together by giving them shared visibility into the changes they're working on and by leveraging automation to enable instant feedback. Ultimately, this will help:

- reduce production errors by preventing deployments without full testing
- save time by eliminating tedious, manual status changes of issues
- provide testers with more information about where their changes are being deployed in order to
- identify the issues and changes that are blocking a release
- create fully automated deployments using issue statuses as checks and balances

## Connecting to GitHub 

Before connecting GitHub issues with BuildMaster, you must integrate GitHub with your BuildMaster instance. To integrate with GitHub, [install the GitHub extension](/docs/buildmaster/tools-service-integrations/buildmaster-integrations-github#extension) and, if necessary, [configure authentication](/docs/buildmaster/tools-service-integrations/buildmaster-integrations-github#authentication).

### Synchronizing Issues to BuildMaster

After connecting to your issue or project tracker, BuildMaster will periodically synchronize with its own built-in issue tracking system. You can view these by going to the Issues tab in the application's navigation bar.

This issue store tracks the following information about each issue:

- **Release** - a specific [Release](/docs/buildmaster/modeling-your-applications/buildmaster-releases) of a [BuildMaster Application](/docs/buildmaster/modeling-your-applications/buildmaster-applications-concepts)
- **Issue ID** - the number GitHub assigned to an issue, such as 1281
- **Type** - the *first* label on an issue, usually something like bug or feature
- **Status** - the issue's state, either open or closed
- **Closed** - true if the issue's status is a resolved status, false otherwise
- **Title** - a brief description of the issue, such as: FIX: Account Status may be incorrect
- **Description** - a longer description of the issue containing details
- **URL** - URL directly linking to the issue in the tool so that it can be clicked on from the BuildMaster user interface

### Example: Inedo Use Case

On our [public instance of BuildMaster](https://buildmaster.inedo.com/), we have dozens of nearly identical applications configured that are designed to build/deploy the various extensions to our products. We've configured just a single issue source as follows:

|Issue Source<br/>Field|Value|
|-|-|
|Name|`GitHub Issue Source`|
|Run for application|`Extensions` (application group)|
|Credentials|`InedoGitHub`|
|Repository Name|`inedox-$ToLower($ApplicationName)`|
|Milestone|`$ReleaseNumber`|

This issue source will only be active for applications within the [Extensions application group](https://buildmaster.inedo.com/all-applications?applicationGroupId=1) we've configured. Each of the applications in this group are named similarly to the repository; for example, there is a [Subversion application](https://buildmaster.inedo.com/applications/23/overview) in BuildMaster and an [inedox-subversion repository](https://github.com/inedo/inedox-subversion) in GitHub. When the issue source is synchronized, the expression `inedox-$ToLower($ApplicationName)` maps these together.

This issue source will periodically synchronize with GitHub. We've configured an automatic check that [prevents deployments until all issues are resolved](/docs/buildmaster/builds-continuous-integration/automated-testing-verification/buildmaster-ci-cd-testing-and-verification-unit-tests).

### Variables and Issue Sources

BuildMaster will automatically and periodically perform one synchronization for each active release in the system. This means that a release is always in context and you can use any of the [variable functions](/docs/buildmaster/reference/functions) that rely on a release to be in context: [$ApplicationName](/docs/buildmaster/reference/functions/applications/applicationname), [$ReleaseNumber](/docs/buildmaster/reference/functions/releases/releasenumber), [$ReleaseName](/docs/buildmaster/reference/functions/releases/releasename), [$ReleaseNumberPart()](/docs/buildmaster/reference/functions/releases/releasenumberpart), etc.

You can also use any [configuration variables](/docs/buildmaster/otterscript-execution-engine/buildmaster-variables) that are accessible from a release context: release, application, application group, or system.

## Creating Milestones

A milestone is one of the two mechanisms that can be used to associate version numbers in BuildMaster with a particular GitHub release, and is intended to be a type of version number. It does not have to match the version used by marketing, but it must be uniquely identifiable so that BuildMaster can use it to apply gating logic in the pipeline stages (i.e., all issues closed for *that* version). You can use the `GitHub::Ensure-Milestone` operation at an early stage to ensure that a milestone exists or could be created at the end of a previous release.

GitHub Projects can also be used as a method to link GitHub issues to BuildMaster releases, and may be easier to use if project management is primarily done through the GitHub interface.

## Closing Milestones 

Closing a milestone in GitHub means the version represented by that milestone was released, so no more issues should be associated with it. To close a milestone at the end of a pipeline, use the `GitHub::Ensure-Milestone` operation with the property `State: closed`.

## Blocking Deployments with Untested/Unresolved Changes 

Once you have BuildMaster connected to GitHub, the next step should be to ensure that releases with unresolved or uncompleted issues aren't deployed. This is an incredibly important part of pushing left, to ensure that issues are identified earlier in the CI/CD process, where they're cheaper and faster to fix and do less damage to the business.

This can be done by configuring one of two [automated checks](/docs/buildmaster/deployment-continuous-delivery/automated-testing-verification-automatic-manual-approvals/buildmaster-ci-cd-testing-and-verification-approvals-automated-checks) on your [release pipeline](/docs/buildmaster/deployment-continuous-delivery/buildmaster-pipelines) that will prevent builds from being deployed to a particular stage unless all issues are closed. You can also permit builds to continue when a certain status is indicated, such as Resolved. Prior to deployment, these approvals will evaluate any issue associated with the release and block deployment if the approval is not met or is unresolved. For example:

- **Issues in Status**; if you specified a status text (such as Resolved and Complete), any issue that doesn't have one of those statuses will cause the approval to be not met
- **Require Issues Closed**; issues still open (i.e., with any status other than closed) will fail to be approved and will be blocked from moving on

As with all automatic approvals, a build may be forced into a stage even if issues aren't closed or aren't in the appropriate status. However, this requires a special action and a special approval that must be manually granted by a human actor (i.e., forcing issues forward is *not* an automatic process).

## Creating and Updating Issues 

Many teams use issue and project tracking tools like GitHub for more than just code changes.

GitHub is flexible, so it can be used to model virtually any business process, such as deployment tracking. Unlike source code work, finding audit information (e.g., when something was deployed and by whom) does not require a high level of technical expertise because all the information is contained in the issue itself.

Teams are already familiar with the process of creating new issues, but want to automate it. BuildMaster allows you to model this workflow by automatically creating and linking to new Issues. This saves the manual effort of creating new issues and avoids human errors that are possible with manual processes.

To create issues for a particular GitHub milestone, use the `GitHub::Create-Issue` operation. To add a comment to an existing issue, use the `GitHub::Create-IssueComment` operation.

### Example: Creating a Deployment Tracking Issue  

Some companies use issues as a means of deployment during a release. This allows business and test teams to see the progress of the deployment, report issues on that issue, and be alerted to status changes via GitHub's notification features.

GitHub isn't really designed for this, but it's generally easier to automate an existing process than to force a new one. This is easy to do with BuildMaster by using an operation to create an issue. Here is an example of how you can do this for GitHub:

```
GitHub::Create-Issue
(
	Project: ProfitCalc,
	Title: QA Testing Required for $ApplicationName,
	Body: This issue was created by BuildMaster on $Date,
	Labels: ReadyForQA,
	Number => $GitHubIssueId
);

Set-BuildVariable GitHubIssueId
(
	Value: $GitHubIssueId
);
```

The second operation will then set `$GitHubIssueId ` as a build variable, meaning it will not only be visible on the build page, but you can also use it in all future deployments on that build.

You can then use `$GitHubIssueId` in later operations to automatically [change issue status](/docs/buildmaster/modeling-your-applications/buildmaster-applications-issue-tracking) or [add comments and notes](/docs/buildmaster/reference/operations/general/create-issuecomment).


### Tip: Use a Variable Renderer for User-friendly Display

You can do this in BuildMaster with [Variable Value Renderers](/docs/buildmaster/otterscript-execution-engine/buildmaster-variables/buildmaster-variables-customizing-display). These are essentially instructions for how to render variables in the UI that have certain names (such as `$GitHubIssueId`). Since you can specify HTML in a value renderer, you link directly to your issue tracker. This provides easy navigation for users. For example, adding a renderer with the following HTML will render a link back to GitHub:

```
<a href="https://github.com/Inedo/inedox-git/issues/$Value" class="ci-icon github">$Value</a>
```