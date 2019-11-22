---
title: YouTrack
subtitle: Integrating YouTrack with BuildMaster
sequence: 200 
keywords: buildmaster, builds, youtrack
show-headings-in-nav: true
---

BuildMaster is designed to continuously deliver your applications and components from source code to production and to help everyone track the changes made to each release along the way. Unlike monolithic CI/CD platforms like Azure DevOps or GitLab, BuildMaster works with any combination of issue and project tracking, including YouTrack.

[YouTrack](https://www.jetbrains.com/youtrack/) is an issue tracking and project management tool from JetBrains. It can be hosted by JetBrains in the cloud (known as InCloud), or installed and managed on-premise (Standalone). While the most common use-case is "bug tracking," YouTrack offers other features as well, including:

{.docs}
 - Agile boards, whether using Scrum or Kanban
 - Project planning and estimation

By integrating BuildMaster with YouTrack, you can help developers, testers, and business analysts work closely together by providing shared visibility into changes they're working on and using automation to facilitate immediate feedback. Ultimately this will help to:

{.docs}
- Reduce production errors by preventing deployments without fully tested issues
- Save time by eliminating tedious, manual status changes on issues
- Give testers more information about where their changes are deployed to
- Quickly identify the issues and changes that are blocking a release
- Create fully automated deployments using issue statuses as checks and balances

BuildMaster is designed to work especially well with YouTrack as an [issue tracker](/docs/buildmaster/verification/issue-tracking) tool. As a more concrete example, Inedo uses YouTrack internally to track internal product changes and also list product issues publicly. You can view our [BuildMaster issues list](https://inedo.myjetbrains.com/youtrack/issues/BM) as an example.

## Installing the YouTrack Extension {#extensions data-title="YouTrack Extension"}

Simply navigate to the *Admin* > *Extensions* page in your instance of BuildMaster and click on the YouTrack extension to install it.

If your instance doesn't have internet access, you can [manually install the YouTrack extension](/docs/buildmaster/reference/extensions#manual-install) after downloading the [YouTrack Extension Package](https://proget.inedo.com/feeds/Extensions/inedox/YouTrack).

## Connecting to YouTrack {#connecting data-title="Connecting to YouTrack"} 

A combination of Issue Sources and [Resource Credentials](/docs/buildmaster/administration/resource-credentials) are used to connect BuildMaster to your Issue or Project Tracking System. Each supported issue tracker has its own type of Issue Source, and that type is defined in the corresponding extension.

For example, installing the YouTrack extension will allow you to create a:

- [Resource Credential](/docs/buildmaster/administration/resource-credentials) that contains a server URL and an API key to connect to your YouTrack server
- Issue Source that identifies which YouTrack issues belong in which [BuildMaster release](/docs/buildmaster/releases/overview) as [outlined below](#issue-source)

Both Resource Credentials and Issue Sources can be configured at the application level, the application group level, and the system level. This means you can have multiple issue sources per application or use one issue source for multiple applications, and they can all use the same resource credential.

## Authentication with YouTrack {#authentication data-title="Authentication with YouTrack"}

After adding the YouTrack extension, you'll need to authenticate it. The YouTrack type [Resource Credential](/docs/buildmaster/administration/resource-credentials) credentials are either username/password or token-based. Since a token is associated with a user, whichever credentials method is chosen, we recommend the user account has the minimum privileges required only to read issues, or create/append to issues. To connect, the YouTrack server URL is required, which for hosted installations follows the format: `https://example.myjetbrains.com/youtrack`

Public servers (i.e., open-source) or internal servers that are firewalled off that don't require can simply use anonymous authentication.

## Associating Issues with BuildMaster Releases {#issue-source data-title="Associating Issues"}

To associate issues with YouTrack [issue source](/docs/buildmaster/verification/issue-tracking#issues-sources), the following fields need to be configured:

{.docs}
 - **Project name** - should be the key (e.g. PG for ProGet)
 - **Fix version** - used to associated Fix Versions in YouTrack with release numbers in BM (typically `$ReleaseNumber`)
 - **Search query** - for advanced users, you can use a [smart search query](https://www.jetbrains.com/help/youtrack/incloud/Search-and-Command-Attributes.html) to associate issues e.g. `project: BM MyCustomField: {$ReleaseNumber}`

### Variable and Issue Sources

BuildMaster will automatically and periodically perform one synchronization for each active release in the system. This means that a release is always "in context", and you can use any of the [variable functions](/docs/buildmaster/reference/functions) that rely on a release to be in context: [$ApplicationName](/docs/buildmaster/reference/functions/applications/applicationname), [$ReleaseNumber](/docs/buildmaster/reference/functions/releases/releasenumber), [$ReleaseName](/docs.inedo.com/docs/buildmaster/reference/functions/releases/releasename), [$ReleaseNumberPart()](/docs/buildmaster/reference/functions/releases/releasenumberpart), etc.

You can also use any [configuration variables](/docs/buildmaster/administration/configuration-variables) that are accessible from a release context: release, application, application-group, or system.

### Syncing Issues in BuildMaster

BuildMaster will also periodically synchronize to its own, built-in issue tracking system. Consider this a fail-over, an additional place to keep track of issues. You can view these by going to the "Issues" tab on the application navigation bar.

This issue store tracks the following information about each issue:

| | |
|--|--|
| Release |a specific [Release](/docs/buildmaster/releases/overview) of a [BuildMaster Application](/docs/buildmaster/administration/applications) |
| Issue Id | a human-readable identifier, such has HDARS-1281 |
| Type | the category of issue, such as Bug or Feature |
| Status | the issue's status, such as Open or Resolved |
| Closed | a true/false value that indicates the issue is "closed" |
| Title | a brief description of the issue, such as "FIX: Account Status may be incorrect" |
| Description | a longer description of the issue, containing details |
| URL | URL directly linking to the issue in the tool, so that it can be clicked on from the BuildMaster user interface |

### Example: Inedo Use Case

On our [public instance of BuildMaster](https://buildmaster.inedo.com/), we have dozens of nearly identical applications configured that are designed to build/deploy the various extensions to our products. We've configured just a single issue source as follows:

- **Name**: GitHub Issue Source
- **Run for application**:  [Extensions](https://buildmaster.inedo.com/all-applications?applicationGroupId=1)
- **Credentials**: InedoGitHub
- **Repository Name** : inedox-$ToLower($ApplicationName)
- **Milestone:** $ReleaseNumber

This issue source will periodically synchronize with (in our case) GitHub, and we've configured an automatic check that [prevents deployments until all issues are resolved](/docs/buildmaster/builds/tests/unit-tests). Integrating with YouTrack, while using a different extension and source, operates the same way.

### Troubleshooting: Issue Synchronization 

An issue synchronization will automatically occur when you visit the release or build overview page. This is mostly transparent: a "please wait" status image is displayed while the execution engine runs the synchronization with the issue source. Once complete, BuildMaster displays the freshly synchronized issues stored within internal issue storage.

### Manual Synchronization

When automatic issue synchronization behaves unexpectedly (either due to an error or misconfiguration), you can perform a manual refresh. This will provide detailed logging to help troubleshoot the behavior.

To perform a manual refresh, open the "edit issue source" dialog (either from the Issues tab under the application or under *Administration* > *Issue Sources*) and then click "Manually Refresh...". This will navigate to a page that allows you to specify:

{.docs}
- Issue Source: either a specific issue source, or all issue sources
- Application: either a specific application, or all applications
- Release: either a specific active release within the specified application, or all applications

Clicking the "Refresh Issues" button will initiate an execution and provide scoped logs.

## Blocking Deployments with Untested/Unresolved Changes {#blocking-deployments data-title="Blocking Deployments"}

Once you've [connected BuildMaster to YouTrack](#connecting), the next step should be ensuring that releases with unclosed or unresolved issues aren't deployed. This is an incredibly important part of "pushing left," making sure that problems are caught earlier in the CI/CD process where they are cheaper and faster to repair and are less damaging to the business.

This can be done configuring one of two automated checks on your [release pipeline](/docs/buildmaster/verification/pipelines) that will prevent builds from being deployed to a particular stage unless all issues are closed. You can also permit builds to move forward after a certain status such as "Resolved" appears. Prior to deployment, these approvals will evaluate each issue associated with the release and block the deployment if the approval is not met or is unresolved. 

BuildMaster supports two automated checks out of the box that can be used without further configuration once YouTrack is set up to [associate issues](#issue-source) using the following [automated checks](/docs/buildmaster/verification/pipelines/approvals-and-gates/automated-checks):

{.docs}
 - **All Issues Closed** - ensures that deployments are blocked to a certain pipeline stage unless all issues are marked as "resolved" in YouTrack; note that this is a separate flag (similar to "Archived") from the issue status which could contain the text "closed" or "resolved"
 - **All Issues in Status** - ensures that deployments are blocked to a certain pipeline stage unless the "state" field in YouTrack matches a specified status defined in a BuildMaster pipeline

Like all automatic approvals, a build may be forced into a stage even if issues aren't closed or in the appropriate status. However, this requires a special action and a specific permission to be manually taken by a human actor (i.e., forcing issues forward is _not_ an automated process).

## Automatically Changing Issue Status {#changing-issue-status data-title="Changing Issue Status"}

Updating an issue's status is commonly done to indicate that an issue was deployed to a new environment and "ready for testing," or "approved by QA" if an [automated check](/docs/buildmaster/verification/pipelines/approvals-and-gates/automated-checks) is configured in a BuildMaster pipeline.

After you've [connected BuildMaster to your Issue or Project Tracking System](/docs/buildmaster/verification/issue-tracking), you can use BuildMaster to automatically change issue statuses as part of a deployment using operations in OtterScript. Use the `YouTrack::Change-Issue-State` operation and supply the new state along with the issue ID obtained from the [find](#finding-issues) operation.

In addition to reducing the tedious labor of changing issue statuses after a deployment (e.g., moving all issues from "In Progress" to "Ready to Test"), BuildMaster lets you leverage the full power of custom statuses/workflows in your issue tracker to provide immediate status to everyone on the team.

### Using the Operations 

The specifics of issue status or state changing operations are documented in the corresponding *Tool & Service Integrations* pages, but they are all used similarly. Essentially, you specify various options using parameters.

### Example: Advanced Workflow to Provide Immediate Status
Business stakeholders want as much information as possible about the changes they've requested, while developers want to avoid as much tedious, manual labor as possible.

Instead of forcing business stakeholders to use a simplistic issue tracker (such as the one built-in to GitHub), or forcing developers to constantly change issue statuses by hand, BuildMaster lets you automatically transition issue statuses on deployment using a workflow as follows:

![](/resources/documentation/buildmaster/advanced-issue-flow.png)

By using these states, it's pretty easy to set up some basic processes and procedures:

{.docs}
- Unless all Issues are "Ready for Production," the release cannot be deployed to production.
- Immediately after a successful deployment to Integration, all issues in "Checked In" should be changed to "Deployed to Integration."

It would be incredibly bothersome to update all of these statuses by hand. With automation, it's trivial.

## Adding Issue Comments/Notes at Build/Deploy Time {#issue-comments data-title="Automatically Adding Comments"} 

Similar to [Automatically Changing Issue Statuses](#changing-issue-status), you can use BuildMaster to automatically add notes or comments to issues as part of a deployment. This can be used to communicate more information to testers or business analysts that rely on the issue or project tracker for status, such as:

- The server/URL a particular build was deployed to
- The name of the user that initiated the deployment and when
- Link back to build in BuildMaster
- A note entered in BuildMaster, at deployment time, by the user who deployed the build

The most common use-case for commenting on issues is to add "deployed by BuildMaster to $EnvironmentName", likely with links directly to the build or artifact, possibly using the [CI Badge API] to generate links with images.To add comments to an issue, use the `YouTrack::Add-Comment` operation along with the issue ID obtained from the [find](#finding-issues) operation.

### Example: Disposable Infrastructure

A common deployment pattern involves deploying software to "disposable environment", in other words, environments that are created and destroyed on an ad-hoc basis. These kinds of environments are typically handled this way in order to:

{.docs}
 - speed up creation of test environments
 - ensure new environments are "fresh", i.e. they might not have dependencies on data or configuration other than a default setup
 - allow multiple environments to exist at the same time
 
A common problem with these kinds of environments is simply knowing that they exist at all once they are created, or how to find the environments in the first place. This can create issues with testing teams, since they may not know which environment they should be testing.

BuildMaster helps solve this problem by integrating with issue tracking tools that are already helping the testing teams manage their processes. For example, you can add a comment to all issues in "Ready for QA" status instructing testers how to access the environments. For example, this OtterScript used in a plan will include a URL to an environment created earlier in a previous pipeline stage:

```
YouTrack::Find-Issues
(
    Credentials: YouTrack,
    Filter: Fix version: $ReleaseNumber State: Ready for QA,
    Output => @YouTrackIssueIDs
);

foreach $IssueId in @YouTrackIssueIDs
{
    YouTrack::Change-Issue-State
    (
        Credentials: YouTrack,
        IssueId: $IssueId,
        State: Deployed to QA
    );
        
    YouTrack::Add-Comment
    (
        Credentials: YouTrack,
        IssueId: $IssueId,
        Comment: >>The fix for this issue ($IssueId) was automatically deployed by BuildMaster on $Date to [$EnvironmentName](https://us-web-01.$ApplicationName-$ReleaseNumber-$BuildNumber.corp/) >>
    );
}          
```

In addition to reducing the tedious labor of changing issue statuses after a deployment (e.g., physically moving all issues from "In Progress" to "Ready to Test"), this lets you leverage the full power of custom statuses/workflows in your issue tracker to provide immediate status updates to everyone on the team.

## Creating New Issues Automatically {#create-new-issues-automatically data-title="Automatically Creating New Issues"}

Many teams use issue and project tracking tools like YouTrack for more than just code changes.

Most issue/project tracking tools available today are extremely flexible and can therefore be used to model virtually any business process, such as deployment tracking. For example, issue trackers have audit trails that, unlike working in the source code, do not require a high degree of technical know-how to use.

Teams are already familiar with the process of creating new issues, but they want to automate it. BuildMaster lets you model this workflow by automatically creating and linking to issues. This saves the manual effort of creating new issues and also avoids the human errors that manual processes allow.

### Example: Creating a "Deployment Tracking" Issue

Some organizations use issues as a way to deployments throughout a release. This way, business and testing teams can see the progress of deployments, report problems on that issue, and be alerted of status changes using the issue tracking tool's notification features.

Although issue tracking tools aren't really designed for this, it's generally easier to just automate your existing process instead of forcing a new process. This is easy to do with BuildMaster: you can use an operation to create an issue:

````
YouTrack::Create-Issue
(
    Credentials: YouTrack,
    Summary: ,
    Description: ,
    IssueId => $IssueId
);

Set-BuildVariable DeploymentIssueId
(
    Value: $YouTrackIssueId
);
````

The second operation will then set `$YouTrackIssueId` as a build variable, which means it will be not only visible on the build page, but you can also use it in all future deployments on that build.

You can then use the `$YouTrackIssueId` in later operations to automatically [change issue status](#changing-issue-status) or [add comments and notes](#issue-comments).


### Tip: Use a Variable Renderer for User-friendly Display

You can do this in BuildMaster with [Variable Value Renderers](/docs/buildmaster/administration/value-renderers). These are essentially instructions for how to render variables in the UI that have certain names (such as `$YouTrackIssueId`). Because you can specify HTML in a value renderer, you link directly to your issue tracker. This provides easy navigation for users.

## Issues and Builds {#issues-builds data-title="Issues & Builds"}

Issues are always associated with a release, but they may also be associated with up to two builds. Associating an issue with a build provides extra contextual information for end-to-end visibility. You can associate the issue with the "Opened" and "Closed" builds:

{.docs}
- Opened: Indicates in which build the issue was discovered or that it was opened for that build
- Closed: Indicates which build successfully resolved or closed an issue (like a patch)

These associations can be especially useful when there are multiple builds that are being tested at the same time.