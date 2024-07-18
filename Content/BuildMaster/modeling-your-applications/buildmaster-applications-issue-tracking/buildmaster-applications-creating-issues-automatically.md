---
title: "[WIP] Creating New Issues Automatically"
order: 2
hidden: true
---

:::(Internal) (Private notes)
rough outline
 - creating a deployment tracking issue
   - create at testing stage
 - creating issues on deployment
   - create in production stage
   - option1: try/catch
    -option2: event listener

:::
Many teams use issue and project tracking tools like JIRA for more than just code changes.

Most issue/project tracking tools available today are extremely flexible and can therefore be used to model virtually any business process, such as deployment tracking. For example, issue trackers have audit trails that do not require a high level of technical expertise to use, unlike source code work.

Teams are already familiar with the process of creating new issues, but they want to automate it. BuildMaster lets you model this workflow by automatically creating and linking to new issues. This saves the manual effort of creating new issues and also avoids human errors that are possible with manual processes.

### Example: Creating a Deployment Tracking Issue

Some companies use issues as a means of deployment throughout a release. This allows the business and test teams to track the progress of the deployment, report issues on that issue, and be notified of status changes through the issue tracking tool's notification features.

Although issue tracking tools aren't really designed for this, it's generally easier to automate an existing process rather than force a new one. This is easy to do with BuildMaster. You can use an operation to create an issue.

Each integration has different features, but here is an example of how you can do this in JIRA:

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

The second operation will then set `$JiraIssueId ` as a build variable, which means it will be not only visible on the build page but you can also use it in all future deployments on that build.

You can then use the $JiraIssueId in later operations to automatically [change issue status](/docs/buildmaster/modeling-your-applications/buildmaster-applications-issue-tracking) or [add comments and notes](/docs/buildmaster/reference/operations/general/create-issuecomment).


### Tip: Use a Variable Renderer for User-friendly Display

You can do this in BuildMaster with [Variable Value Renderers](/docs/buildmaster/otterscript-execution-engine/buildmaster-variables/buildmaster-variables-customizing-display). These are essentially instructions for how to render variables in the UI that have certain names (such `$JiraIssueId `). Because you can specify HTML in a value renderer, you link directly to your issue tracker. This provides easy navigation for users.

## Troubleshooting: Issue Synchronization 

An issue synchronization happens automatically when you visit the release or build overview page. This is mostly transparent: a "please wait" status screen is displayed while the execution engine runs the synchronization with the issue source. Once the synchronization is complete, BuildMaster displays the freshly synchronized issues stored in the internal issue storage.

### Manual Synchronization

If the automatic issue synchronization behaves unexpectedly (either due to an error or incorrect configuration), you can perform a manual update. This will give you a detailed log that will help you troubleshoot the problem.

To perform a manual refresh, open the "edit issue source" dialog box (either on the Issues tab in the application or at Administration > Issue Sources), and then Manually. This will take you to a page where you can enter the desired information:

- Issue Source: either a specific issue source or all issue sources
- Application: either a specific application or all applications
- Release: either a specific active release within the specified application or all applications

Clicking the Refresh Issues button will initiate an execution and provide scoped logs.

## Using BuildMaster's Internal Issue Tracker 

You can use BuildMaster's Issue Store as a lightweight issue tracker that allows you to add issues and associate them with specific releases and builds. While this is certainly not as advanced as a dedicated issue tracking tool, it can be perfectly adequate for small applications and components with few changes.

You can add and edit issues on the Issues tab, the Release Overview, and the Build Overview page. The internal issue tracker can be enabled or disabled at Application > Settings > Advanced.

## Issues and Builds 

Issues are always associated with a release, but can also be associated with up to two builds. Associating an issue to a build provides additional contextual information for end-to-end visibility. You can associate the issue to Opened and Closed builds:

- Opened: Indicates in which build the issue was discovered or that it was opened for that build
- Closed: Indicates in which build an issue was successfully fixed or closed (like a patch)

These associations can be especially useful when testing multiple builds at once.