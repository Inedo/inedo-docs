---
title: YouTrack
subtitle: Integrating YouTrack with BuildMaster
sequence: 350 
keywords: buildmaster, builds, youtrack
show-headings-in-nav: true
---

BuildMaster is designed to continuously deliver your applications and components from source code to production and to help everyone track the changes made to each release along the way. BuildMaster works seamlessly with YouTrack.

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

A combination of Issue Sources and [Resource Credentials](/docs/buildmaster/administration/resource-credentials) are used to connect BuildMaster to YouTrack.

Installing the YouTrack extension will allow you to create a:

{.docs}
 - [Resource Credential](/docs/buildmaster/administration/resource-credentials) that contains a server URL and an API key to connect to your YouTrack server
   - These credentials are either username/password or token-based. Since a token is associated with a user, whichever credentials method is chosen, we recommend the user account has the minimum privileges required only to read issues, or create/append to issues.
   - To connect, the YouTrack server URL is required, which for hosted installations follows the format: `https://example.myjetbrains.com/youtrack`
 - Issue Source that identifies which YouTrack issues belong in which [BuildMaster release](/docs/buildmaster/releases/overview) as [outlined below](#issue-source)

Public servers (i.e. open-source projects) or internal servers that are firewalled off that don't require a username or password and can simply use anonymous authentication.

Both Resource Credentials and Issue Sources can be configured at the application level, the application group level, and the system level. This means you can have multiple issue sources per application or use one issue source for multiple applications, and they can all use the same resource credential.

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

BuildMaster will also periodically synchronize issues in YouTrack its own, built-in issue tracking system. Consider this a fail-over, an additional place to keep track of issues. You can view these by going to the "Issues" tab on the application navigation bar.

This issue source tracks the following information about each issue:

| BuildMaster Term | YouTrack Field | Description |
|--|--|--|
| Release | Fix version | a specific [release](/docs/buildmaster/releases/overview) of a [BuildMaster application](/docs/buildmaster/administration/applications) |
| Issue ID | ID | a human-readable identifier, such has HDARS-1281, sometimes referred to as the "visible ID" |
| Type | Type | the category of issue, such as Bug or Feature |
| Status | State | the issue's status, such as Open or Resolved |
| Closed | Resolved | a true/false value that indicates the issue is "closed" |
| Title | Summary | a brief description of the issue, such as "FIX: Account Status may be incorrect" |
| Description | Description | a longer description of the issue, containing details |
| URL | URL | URL directly linking to the issue in the tool, so that it can be clicked on from the BuildMaster user interface |

### Manual Synchronization

See [Manual Synchronization](/docs/buildmaster/verification/issue-tracking) for details on manually refreshing issues for an issue source.

## Blocking Deployments with Untested/Unresolved Changes {#blocking-deployments data-title="Blocking Deployments"}

Once you've [connected BuildMaster to YouTrack](#connecting), the next step should be ensuring that releases with unclosed or unresolved issues aren't deployed. This is an incredibly important part of "pushing left," making sure that problems are caught earlier in the CI/CD process where they are cheaper and faster to repair and are less damaging to the business.

This can be done configuring one of two automated checks on your [release pipeline](/docs/buildmaster/verification/pipelines) that will prevent builds from being deployed to a particular stage unless all issues are closed. You can also permit builds to move forward after a certain status such as "Resolved" appears. Prior to deployment, these approvals will evaluate each issue associated with the release and block the deployment if the approval is not met or is unresolved. 

BuildMaster supports two automated checks out of the box that can be used without further configuration once YouTrack is set up to [associate issues](#issue-source) using the following [automated checks](/docs/buildmaster/verification/pipelines/approvals-and-gates/automated-checks):

{.docs}
 - **All Issues Closed** - ensures that deployments are blocked to a certain pipeline stage unless all issues are marked as "resolved" in YouTrack; note that this is a separate flag (similar to "Archived") from the issue status which could contain the text "closed" or "resolved"
 - **All Issues in Status** - ensures that deployments are blocked to a certain pipeline stage unless the "state" field in YouTrack matches a specified status defined in a BuildMaster pipeline

Like all automatic approvals, a build may be forced into a stage even if issues aren't closed or in the appropriate status. However, this requires a special action and a specific permission to be manually taken by an actual person. In other words, forcing a build forward despite being blocked on issue status is _not_ an automated process.

## Automatically Changing Issue Status {#changing-issue-status data-title="Changing Issue Status"}

Updating an issue's status is commonly done to indicate that an issue was deployed to a new environment and "ready for testing," or "approved by QA" if an [automated check](/docs/buildmaster/verification/pipelines/approvals-and-gates/automated-checks) is configured in a BuildMaster pipeline.

After you've [connected BuildMaster to YouTrack](#connecting), you can use BuildMaster to automatically change issue statuses as part of a deployment using operations in OtterScript. In addition to reducing the tedious labor of changing issue statuses after a deployment (e.g., moving all issues from "In Progress" to "Ready to Test"), BuildMaster lets you leverage the full power of custom statuses in YouTrack to provide immediate status to everyone on the team.

### Example: `YouTrack::Change-Issue-State` Operation

Use the `YouTrack::Change-Issue-State` operation and supply the new state along with the issue ID obtained from the [find](#finding-issues) operation.

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
}
```

## Adding Issue Comments/Notes at Build/Deploy Time {#issue-comments data-title="Automatically Adding Comments"} 

Similar to [Automatically Changing Issue Statuses](#changing-issue-status), you can use BuildMaster to automatically add notes or comments to issues as part of a deployment. This can be used to communicate more information to testers or business analysts that rely on the issue or project tracker for status, such as:

{.docs}
- The server/URL a particular build was deployed to
- The name of the user that initiated the deployment and when
- Link back to build in BuildMaster
- A note entered in BuildMaster, at deployment time, by the user who deployed the build

### Example: `YouTrack::Add-Comment` Operation

The most common use-case for commenting on issues is to add "deployed by BuildMaster to $EnvironmentName", likely with links directly to the build or artifact, possibly using the [CI Badge API](/docs/buildmaster/reference/api/ci-badge) to generate links with images.To add comments to an issue, use the `YouTrack::Add-Comment` operation:

```
YouTrack::Find-Issues
(
    Credentials: YouTrack,
    Filter: Fix version: $ReleaseNumber State: Ready for QA,
    Output => @YouTrackIssueIDs
);
foreach $IssueId in @YouTrackIssueIDs
{
    YouTrack::Add-Comment
    (
        Credentials: YouTrack,
        IssueId: $IssueId,
        Comment: >>The fix for this issue ($IssueId) was automatically deployed by BuildMaster on $Date to [$EnvironmentName]([https://us-web-01.$ApplicationName-$ReleaseNumber-$BuildNumber.corp/](https://us-web-01.%24applicationname-%24releasenumber-%24buildnumber.corp/)) >>
    );
}
```

## Creating New Issues Automatically {#create-new-issues-automatically data-title="Automatically Creating New Issues"}

Many teams use YouTrack for more than just code changes. YouTrack's flexiblity means it can be used to model virtually any business process, such as deployment tracking. For example, unlike working in the source code, locating auditing information (e.g., when something was deployed and by whom) does not require a high degree of technical know-how to use, because all information is within the issue itself.

Teams are already familiar with the process of creating new issues, but they want to automate it. BuildMaster lets you model this workflow by automatically creating and linking to issues. This saves the manual effort of creating new issues and also avoids the human errors that manual processes allow.

### Example: `YouTrack::Create-Issue` Operation

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


#### Tip: Use a Variable Renderer for User-friendly Display

You can do this in BuildMaster with [Variable Value Renderers](/docs/buildmaster/administration/value-renderers). These are essentially instructions for how to render variables in the UI that have certain names (such as `$YouTrackIssueId`). Because you can specify HTML in a value renderer, you link directly to your issue tracker. This provides easy navigation for users. 

An example renderer HTML that links back to YouTrack would be:

```
<a href="https://corp.myjetbrains.com/youtrack/issue/$YouTrackIssueId">$YouTrackIssueId</a>
```
