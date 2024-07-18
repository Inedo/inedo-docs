---
title: "YouTrack"
order: 7
---


BuildMaster is designed to continuously deliver your applications and components from source code to production, helping everyone track changes made to each version. BuildMaster works seamlessly with YouTrack.

[YouTrack](https://www.jetbrains.com/youtrack/) is an issue tracking and project management tool from JetBrains. It can be hosted by JetBrains in the cloud (known as InCloud) or installed and managed on-premises (Standalone). While the most common use case is issue tracking, YouTrack also offers other features including:

- Agile committees, whether with Scrum or Kanban
 - Project planning and estimation

By integrating BuildMaster with YouTrack, you can help developers, testers, and business analysts work closely together by providing shared insight into the changes they're working on and using automation to enable instant feedback. Ultimately, this will help:

- Reduce production errors by preventing deployments without fully tested issues
- Save time by avoiding tedious, manual status changes to issues
- Give testers more information about where their changes are deployed to
- Quickly identify the issues and changes that are blocking a release
- Create fully automated deployments using issue status as a check and balance

BuildMaster is designed to work especially well with YouTrack as an [issue tracker](/docs/buildmaster/modeling-your-applications/buildmaster-applications-issue-tracking) tool. As a more concrete example, Inedo uses YouTrack internally to track internal product changes and also list product issues publicly. You can view our [BuildMaster issues list](https://issues.inedo.com/youtrack/issues/BM) as an example.

## Installing the YouTrack Extension 

Simply navigate to the *Admin* > *Extensions* page in your instance of BuildMaster and click on the YouTrack extension to install it.

If your instance doesn't have Internet access, you can [manually install the YouTrack extension](/docs/buildmaster/reference/extensions#manual-install) after downloading the [YouTrack Extension Package](https://proget.inedo.com/feeds/Extensions/inedox/YouTrack).

## Connecting to YouTrack

A combination of Issue Sources and [Credentials & Resources](/docs/buildmaster/configuring-for-your-team/buildmaster-administration-resource-credentials) are used to connect BuildMaster to YouTrack.

Installing the YouTrack extension allows you to:
 - YouTrack Instance Secure Resource, which contains a server URL to your YouTrack server
 - YouTrack Permanent Token Secure Credential, which contains a token you created in your YouTrack instance
   - This token is associated with a YouTrack user, and requires minimum privileges required only to read issues, or create/append to issues.
   - To connect, the YouTrack server URL is required, which for hosted installations follows the format: `https://example.myjetbrains.com/youtrack`
 - Issue Source that identifies which YouTrack issues belong in which [BuildMaster release](/docs/buildmaster/modeling-your-applications/buildmaster-releases) as [outlined below](#issue-source)

Public servers (e.g., open source projects) or internal servers that are firewalled off and don't require a username or password, but can simply use anonymous authentication.

Both Resource Credentials and Issue Sources can be configured at the application level, the application group level, and the system level. This means that you can have multiple issue sources per application or use one issue source for multiple applications that can all use the same resource credential.

## Associating Issues with BuildMaster Releases

To associate issues with YouTrack [issue source](/docs/buildmaster/modeling-your-applications/buildmaster-applications-issue-tracking#issues-sources), the following fields need to be configured:

 - **Project name** - should be the name or the key (for example: PG or ProGet)
 - **Fix version** - used to associated Fix Versions in YouTrack with release numbers in BM (typically `$ReleaseNumber`)
 - **Search query** - for advanced users, you can use a [smart search query](https://www.jetbrains.com/help/youtrack/incloud/Search-and-Command-Attributes.html) to associate issues e.g., `project: BM MyCustomField: {$ReleaseNumber}`

### Variable and Issue Sources

BuildMaster will automatically and periodically perform one synchronization for each active release in the system. This means that a release is always "in context", and you can use any of the [variable functions](/docs/buildmaster/reference/functions) that rely on a release to be in context: [$ApplicationName](/docs/buildmaster/reference/functions/applications/applicationname), [$ReleaseNumber](/docs/buildmaster/reference/functions/releases/releasenumber), [$ReleaseName](/docs/buildmaster/reference/functions/releases/releasename), [$ReleaseNumberPart()](/docs/buildmaster/reference/functions/releases/releasenumberpart), etc.

You can also use any [configuration variables](/docs/buildmaster/otterscript-execution-engine/buildmaster-variables) that are accessible from a release context: release, application, application-group, or system.

### Syncing Issues in BuildMaster

BuildMaster also regularly syncs issues in YouTrack, its own built-in issue tracking system. Consider this a fallback option, an additional place to track issues. You can view these by going to the Issues tab in the application's navigation bar.

This issue source tracks the following information about each issue:

| BuildMaster Term | YouTrack Field | Description |
|--|--|--|
| Release | Fix version | a specific [release](/docs/buildmaster/modeling-your-applications/buildmaster-releases) of a [BuildMaster application](/docs/buildmaster/modeling-your-applications/buildmaster-applications-concepts) |
| Issue ID | ID | a human readable identifier, e.g. HDARS-1281, sometimes referred to as visible ID |
| Type | Type |the category of the issue, such as Bug or Feature |
| Status | State | the problem status, e.g. Open or Resolved |
| Closed | Resolved | a true/false value indicating that the issue is closed |
| Title | Summary | a short description of the problem, e.g. FIX: Account Status may be incorrect |
| Description | Description | a longer description of the problem, containing details |
| URL | URL | URL directly to the problem in the tool, so that it can be clicked from the BuildMaster user interface |

### Manual Synchronization

See [Manual Synchronization](/docs/buildmaster/modeling-your-applications/buildmaster-applications-issue-tracking) for details on manually refreshing issues for an issue source.

## Blocking Deployments with Untested/Unresolved Changes 

Once you've [connected BuildMaster to YouTrack](#connecting), the next step should be to ensure that versions with unresolved or uncompleted issues aren't deployed. This is an incredibly important part of "pushing left", ensuring that issues are caught earlier in the CI/CD process where they're cheaper and faster to fix, and less damaging to the business.

This can be done configuring one of two automatic checks in your [release pipeline](/docs/buildmaster/deployment-continuous-delivery/buildmaster-pipelines) that prevent builds from being deployed at a particular stage until all issues are closed. You can also allow builds to be deployed only after a certain status, such as Resolved, is indicated. Before deployment, these approvals will evaluate any issue associated with the release and block deployment if the approval is not met or unresolved.

BuildMaster natively supports two automated checks that can be used without further configuration once YouTrack is set up to [associate issues](#issue-source) with the following [automated checks](/docs/buildmaster/deployment-continuous-delivery/automated-testing-verification-automatic-manual-approvals/buildmaster-ci-cd-testing-and-verification-approvals-automated-checks):

 - **All Issues Closed** - ensures that deployments are blocked to a certain pipeline stage until all issues are marked as Resolved in YouTrack; note that this is a separate flag (similar to "Archived") from the issue status which could contain the text "closed" or "resolved"
 - **All Issues in Status** - ensures that deployments are blocked to a certain pipeline stage unless the "state" field in YouTrack matches a specified status defined in a BuildMaster pipeline

As with all automatic approvals, a build may be forced into a phase even if issues aren't closed or aren't in the appropriate status. However, this requires a special action and a specific permission to be manually given by an actual person. In other words, forcing a build despite a blocked issue status is _not_ an automatic process.

## Automatically Changing Issue Status 

Updating the status of an issue is typically done to indicate that an issue has been deployed to a new environment and is "ready for testing" or "approved by QA" if an [automated check](/docs/buildmaster/deployment-continuous-delivery/automated-testing-verification-automatic-manual-approvals/buildmaster-ci-cd-testing-and-verification-approvals-automated-checks) is configured in a BuildMaster pipeline.

After you've [connected BuildMaster to YouTrack](#connecting), you can use BuildMaster to automatically change the status of an issue as part of a deployment using operations in OtterScript. BuildMaster not only reduces the tedious labor of changing issue statuses after a deployment (for example, moving all issues from "In Progress" to "Ready to Test"), but also allows you to use the full power of custom statuses in YouTrack to provide instant status to all team members.

### Example: `YouTrack::Change-IssueStates` Operation

Use the `YouTrack::Change-IssueStates` operation and supply the new state for the issues matching the specified YouTrack query.

```
YouTrack::Change-IssueStates
(
    Credentials: YouTrack,
    Query: Project: {$ApplicationName} Fix version: $ReleaseNumber State: {Ready for QA}
    State: Deployed to QA
);
```

## Adding Issue Comments/Notes at Build/Deploy Time 

Similar to [Automatically Changing Issue Statuses](#changing-issue-status), you can use BuildMaster to automatically add notes or comments to issues as part of a deployment. This can be used to provide additional information to testers or business analysts who rely on the issue or project tracker for status, such as:

- The server/URL where a particular build was deployed
- The name of the user who initiated the deployment and when
- Link back to the build in BuildMaster
- A note entered into BuildMaster at the time of deployment by the user who deployed the build

### Example: `YouTrack::Add-Comment` Operation

The most common use-case for commenting on issues is to add "deployed by BuildMaster to $EnvironmentName", likely with links directly to the build or artifact, possibly using the [CI Badge API](/docs/buildmaster/reference/api/buildmaster-ci-cd-continuous-integration-server-badges) to generate links with images. To add comments to an issue, use the `YouTrack::Add-Comment` operation:

```
YouTrack::Add-Comment
(
    Credentials: YouTrack,
    Query: Project: {$ApplicationName} Fix version: $ReleaseNumber State: Ready for QA,
    Comment: >>The fix for this issue ($IssueId) was automatically deployed by BuildMaster on $Date to [$EnvironmentName]([https://us-web-01.$ApplicationName-$ReleaseNumber-$BuildNumber.corp/](https://us-web-01.%24applicationname-%24releasenumber-%24buildnumber.corp/)) >>
);
```

## Creating New Issues Automatically

Many teams use YouTrack for more than just code changes. YouTrack's flexibility means it can be used to model virtually any business process, such as deployment tracking. Unlike source code work, finding audit information (e.g., when something was deployed and by whom) does not require a high level of technical expertise because all the information is contained in the issue itself.

Teams are already familiar with the process of creating new issues, but they want to automate it. BuildMaster lets you model this workflow by automatically creating and linking to new issues. This saves the manual effort of creating new issues and avoids human errors that are possible with manual processes.

### Example: `YouTrack::Create-Issue` Operation

Some organizations use issues as a means of deployment during a release. This allows business and testing teams to see the progress of deployments, report issues to that issue, and be alerted to status changes through the issue tracking tool's notification features.

Although issue tracking tools aren't really designed for this, it's generally easier to automate an existing process rather than force a new one. This is easy to do with BuildMaster: you can use an operation to create an issue:

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

The second operation then sets `$YouTrackIssueId` as a build variable, which means that it is not only visible on the build page, but can also be used in all future deployments on that build.

You can then use the `$YouTrackIssueId` in later operations to automatically [change issue status](#changing-issue-status) or [add comments and notes](#issue-comments).


#### Tip: Use Custom Variable Rendering for User-friendly Display

You can do this in BuildMaster with [Custom Variable Rendering](/docs/buildmaster/otterscript-execution-engine/buildmaster-variables/buildmaster-variables-customizing-display). These are essentially instructions for rendering variables in UI. Since you can specify HTML in a value renderer, you link directly to your issue tracker. This makes it easier for users to navigate.

An example of a renderer HTML code that links to YouTrack would be:

```
<a href="https://corp.myjetbrains.com/youtrack/issue/$YouTrackIssueId">$YouTrackIssueId</a>
```