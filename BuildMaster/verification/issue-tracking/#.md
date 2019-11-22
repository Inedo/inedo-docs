---
title: Issue and Project Tracking
show-headings-in-nav: true
---

BuildMaster is designed to continuously deliver your applications and components from source code to production and to help everyone track the changes made to each release along the way. Unlike monolithic CI/CD platforms like Azure DevOps or GitLab, BuildMaster works with any combination of issue and project tracking tools like JIRA or YouTrack.

By integrating BuildMaster with your issue and project tracking tools, you can help developers, testers, and business analysts work closely together by providing shared visibility into changes they're working on and using automation to facilitate immediate feedback. Ultimately this will help to

- Reduce production errors by preventing deployments without fully-tested issues
- Save time by eliminating tedious, manual status changes on issues
- Give testers more information about where their changes are deployed to
- Quickly identify the issues and changes that are blocking a release
- Create fully-automated deployments using issue statuses as checks and balances

## Connecting to Your Issue or Project Tracking System {#connecting data-title="Connecting to Issue/Project Trackers"}

A combination of Issue Sources and [Resource Credentials](/docs/buildmaster/administration/resource-credentials) are used to connect BuildMaster to your Issue or Project Tracking System, whether that's a public issue tracker on a GitHub repository or a self-managed JIRA instance. Each supported issue tracker has its own type of Issue Source, and that type is defined in the corresponding extension.

For example, installing the [JIRA extension](https://github.com/Inedo/inedox-jira) will allow you to create a

- [Resource Credential](/docs/buildmaster/administration/resource-credentials) that contains a server URL and an API key to connect to your JIRA server
- Issue Source that identifies which JIRA issues belong in which [BuildMaster release](/docs/buildmaster/releases/overview); in JIRA, this is done in one of two ways
	- Field mapping: choosing a project in JIRA, and then picking a field in that project (like `fixFor`) that BuildMaster will use to search for issues associated with the release
	- Custom mapping: using JIRA's powerful JQL language to search using any number of fields (such as `fixFor`, `issueType`, etc.), or even across multiple JIRA projects

Both Resource Credentials and Issue Sources can be configured at the application level, the application group level, and the system level. This means you can have multiple issue sources per application or use one issue source for multiple applications, and they can all use the same resource credential.

### Synchronizing Issues to BuildMaster

After connecting to your issue or project tracker, BuildMaster will periodically synchronize to its own, built-in issue tracking system. You can view these by going to the "Issues" tab on the application navigation bar.

This issue store tracks the following information about each issue:

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

This issue source will only be active for applications within the [Extensions application group](https://buildmaster.inedo.com/all-applications?applicationGroupId=1) we've configured. Each of the applications in this group are named similarly to the repository; for example, there is a [Subversion application](https://buildmaster.inedo.com/applications/23/) in BuildMaster  and a [inedox-subversion repository](https://github.com/inedo/inedox-subversion) in GitHub. The expression `inedox-$ToLower($ApplicationName)` maps these together when the issue source is synchronized.

This issue source will periodically synchronize with GitHub, and we've configured an automatic check that [prevents deployments until all issues are resolved](/docs/buildmaster/builds/tests/unit-tests).

### Variable and Issue Sources

BuildMaster will automatically and periodically perform one synchronization for each active release in the system. This means that a release is always "in context", and you can use any of the [variable functions](/docs/buildmaster/reference/functions) that rely on a release to be in context: [$ApplicationName](/docs/buildmaster/reference/functions/applications/applicationname), [$ReleaseNumber](/docs/buildmaster/reference/functions/releases/releasenumber), [$ReleaseName](/docs.inedo.com/docs/buildmaster/reference/functions/releases/releasename), [$ReleaseNumberPart()](/docs/buildmaster/reference/functions/releases/releasenumberpart), etc.

You can also use any [configuration variables](/docs/buildmaster/administration/configuration-variables) that are accessible from a release context: release, application, application-group, or system.

## Blocking Deployments with Untested/Unresolved Changes {#blocking-deployments data-title="Blocking Deployments"}

Once you've [connected BuildMaster to your Issue or Project Tracking System](/docs/buildmaster/verification/issue-tracking), the next step should be ensuring that releases with unclosed or unresolved issues aren't deployed. This is an incredibly important part of "pushing left," making sure that problems are caught earlier in the CI/CD process where they are cheaper and faster to repair and are less damaging to the business.

This can be done configuring one of two [automated checks](/docs/buildmaster/verification/pipelines/approvals-and-gates/automated-checks) on your [release pipeline](/docs/buildmaster/verification/pipelines) that will prevent builds from being deployed to a particular stage unless all issues are closed. You can also permit builds to move forward after a certain status such as "Resolved" appears. Prior to deployment, these approvals will evaluate each issue associated with the release and block the deployment if the approval is not met or is unresolved. For example:

- **Issues in Status**;  if you specified a specific status texts (such as Resolved and Complete), any issue that doesn't have one of those statuses will cause the approval to be not met
- **Require Issues Closed**;  issues still "open" (i.e., with any status other than "closed") will fail to be approved and will be blocked from moving on

Like all automatic approvals, a build may be forced into a stage even if issues aren't closed or in the appropriate status. However, this requires a special action and a specific permission to be manually taken by a human actor (i.e., forcing issues forward is *not* an automated process).

## Automatically Changing Issue Status {#changing-issue-status data-title="Changing Issue Status"}

After you've [connected BuildMaster to your Issue or Project Tracking System](/docs/buildmaster/verification/issue-tracking), you can use BuildMaster to automatically change issue statuses as part of a deployment using operations in OtterScript.

In addition to reducing the tedious labor of changing issue statuses after a deployment (e.g., moving all issues from "In Progress" to "Ready to Test"), BuildMaster lets you leverage the full power of custom statuses/workflows in your issue tracker to provide immediate status to everyone on the team.

### Using the Operations

The specifics of issue status or state changing operations are documented in the corresponding *Tool & Service Integrations* pages, but they are all used similarly. Essentially, you specify various options using parameters.

### Example: Basic JIRA Workflow Transition

For example, JIRA uses "workflow transitions" to change an issue's status and helps define a business process instead of just an arbitrary list of status fields. If you defined a workflow for the `ProfitCalc` project in your JIRA instance that allowed issues to be transitioned from "Ready for Release" to "Released," you could accomplish this automatically with the following OtterScript:

````
Jira::Transition-Issues
(
    Credentials: Jira,
    Project: ProfitCalc,
    From: Ready for Release,
    To: Released,
    FixFor: $ReleaseNumber
);
````

### Example: Advanced Workflow to Provide Immediate Status
Business stakeholders want as much information as possible about the changes they've requested, while developers want to avoid as much tedious, manual labor as possible.

Instead of forcing business stakeholders to use a simplistic issue tracker (such as the one built-in to GitHub), or forcing developers to constantly change issue statuses by hand, BuildMaster lets you automatically transition issue statuses on deployment using a workflow as follows:

<img src="/resources/documentation/buildmaster/advanced-issue-flow.png" width="100%" alt="Advanced Issue Flow" />

By using these states, it's pretty easy to set up some basic processes and procedures:

- Unless all Issues are "Ready for Production," the release cannot be deployed to production.
-   Immediately after a successful deployment to Integration, all issues in "Checked In" should be changed to "Deployed to Integration."

It would be incredibly bothersome to update all of these statuses by hand. With automation, it's trivial.

## Adding Comments/Notes During Build/ Deploy Time {#adding-comments-notes data-title="Automatically Adding Comments"}

Similar to [Automatically Changing Issue Statuses](#changing-issue-status), you can use BuildMaster to automatically add notes or comments to issues as part of a deployment. This can be used to communicate more information to testers or business analysts that rely on the issue or project tracker for status, such as:

- The server/URL a particular build was deployed to
- The name of the user that initiated the deployment and when
- Link back to build in BuildMaster
- A note entered in BuildMaster, at deployment time, by the user who deployed the build

### Example: Disposable Infrastructure

 A common deployment pattern involves deploying software to "disposable environment", in other words, environments that are created and destroyed on an ad-hoc basis. These kinds of environments are typically handled this way in order to:

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
        Comment: >>The fix for this issue ($IssueId) was automatically deployed by BuildMaster on $Date to [$EnvironmentName]([https://us-web-01.$ApplicationName-$ReleaseNumber-$BuildNumber.corp/](https://us-web-01.%24applicationname-%24releasenumber-%24buildnumber.corp/)) >>
    );
}
```

## Creating New Issues Automatically {#create-new-issues data-title="Automatically Creating New Issues"}

Many teams use issue and project tracking tools like JIRA for more than just code changes.

Most issue/project tracking tools available today are extremely flexible and can therefore be used to model virtually any business process, such as deployment tracking. For example, issue trackers have audit trails that, unlike working in the source code, do not require a high degree of technical know-how to use.

Teams are already familiar with the process of creating new issues, but they want to automate it. BuildMaster lets you model this workflow by automatically creating and linking to issues. This saves the manual effort of creating new issues and also avoids the human errors that manual processes allow.

### Example: Creating a "Deployment Tracking" Issue

Some organizations use issues as a way to deployments throughout a release. This way, business and testing teams can see the progress of deployments, report problems on that issue, and be alerted of status changes using the issue tracking tool's notification features.

Although issue tracking tools aren't really designed for this, it's generally easier to just automate your existing process instead of forcing a new process. This is easy to do with BuildMaster: you can use an operation to create an issue.

Each integration has different properties, but as an example, here's how you could do this in JIRA:

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

The second operation will then set `$JiraIssueId ` as a build variable, which means it will be not only visible on the build page, but you can also use it in all future deployments on that build.

You can then use the $JiraIssueId in later operations to automatically [change issue status](/docs/buildmaster/verification/issue-tracking) or [add comments and notes](/docs/buildmaster/reference/operations/issue-tracking/add-comment-to-issues).


### Tip: Use a Variable Renderer for User-friendly Display

You can do this in BuildMaster with [Variable Value Renderers](/docs/buildmaster/administration/value-renderers).  These are essentially instructions for how to render variables in the UI that have certain names (such `$JiraIssueId `). Because you can specify HTML in a value renderer, you link directly to your issue tracker. This provides easy navigation for users.

## Troubleshooting: Issue Synchronization {#troubleshooting-issue data-title="Troubleshooting Synchronization"}

An issue synchronization will automatically occur when you visit the release or build overview page. This is mostly transparent: a "please wait" status image is displayed while the execution engine runs the synchronization with the issue source. Once complete, BuildMaster displays the freshly synchronized issues stored within internal issue storage.

### Manual Synchronization

When automatic issue synchronization behaves unexpectedly (either due to an error or misconfiguration), you can perform a manual refresh. This will provide detailed logging to help troubleshoot the behavior.

To perform a manual refresh, open the "edit issue source" dialog (either from the Issues tab under the application or under Administration > Issue Sources) and then click "Manually Refresh…". This will navigate to a page that allows you to specify:

- Issue Source: either a specific issue source, or all issue sources
- Application: either a specific application, or all applications
- Release: either a specific active release within the specified application, or all applications

Clicking the "Refresh Issues" button will initiate an execution and provide scoped logs.

## Using BuildMaster's Internal Issue Tracker {#internal-issue-tracker data-title="Internal Issue Tracker"}

You can use BuildMaster's issue store as a lightweight issue tracker, allowing you to add issues and associate them with specific releases and builds. While this certainly isn't as advanced as a dedicated issue tracking tool, it may be perfectly adequate for small applications and components with few changes.

You can add and edit issues from the Issues tab, the Release Overview, and the Build Overview pages. The internal issue tracker can be enabled or disabled under Application > Settings > Advanced.

## Issues and Builds {#issues-builds data-title="Issues & Builds"}

Issues are always associated with a release, but they may also be associated with up to two builds. Associating an issue with a build provides extra contextual information for end-to-end visibility. You can associate the issue with the "Opened" and "Closed" builds:

- Opened: Indicates in which build the issue was discovered or that it was opened for that build
- Closed: Indicates which build successfully resolved or closed an issue (like a patch)

These associations can be especially useful when there are multiple builds that are being tested at the same time.