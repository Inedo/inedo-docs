---
title: YouTrack
subtitle: Integrating YouTrack with BuildMaster
sequence: 200 
keywords: buildmaster, builds, youtrack
show-headings-in-nav: true
---

[YouTrack](https://www.jetbrains.com/youtrack/) is an issue tracking and project management tool from JetBrains. It can be hosted by JetBrains in the cloud (known as InCloud), or installed and managed on-premise (Standalone). While the most common use-case is "bug tracking", YouTrack offers other features as well:

{.docs}
 - agile boards, whether using Scrum or Kanban
 - project planning and estimation

## Authentication with YouTrack {#authentication data-title="Authentication with YouTrack"}

Authentication with YouTrack is handled with [Resource Credentials](/docs/buildmaster/administration/resource-credentials), using the YouTrack type. These credentials are either username/password or token-based. Since a token is associated with a user, whichever credentials method is chosen, we recommend the user account has the minimum privileges required only to read issues, or create/append to issues. To connect, the YouTrack server URL is required, which for hosted installations follows the format: `https://example.myjetbrains.com/youtrack`

Public servers (i.e. open-source) or internal servers that are firewalled off that don't require can simply use anonymous authentication.

## Issue Tracking Integration {#issue-tracking data-title="Issue Tracking Integration"}

BuildMaster is designed to work with YouTrack as an [issue tracker](/docs/buildmaster/verification/issue-tracking) tool. As a more concrete example, Inedo uses YouTrack internally to track internal product changes and also list product issues publicly. You can view our [BuildMaster issues list](https://inedo.myjetbrains.com/youtrack/issues/BM) as an example.

## Associating Issues with BuildMaster Releases {#issue-source data-title="Associating Issues"}

To associate issues with YouTrack [issue source](/docs/buildmaster/verification/issue-tracking#issues-sources), the following fields need to be configured:

{.docs}
 - **Project name** - should be the key (e.g. PG for ProGet)
 - **Fix version** - used to associated Fix Versions in YouTrack with release numbers in BM (typically `$ReleaseNumber`)
 - **Search query** - for advanced users, you can use a [smart search query](https://www.jetbrains.com/help/youtrack/incloud/Search-and-Command-Attributes.html) to associate issues e.g. `project: BM MyCustomField: {$ReleaseNumber}`

## Creating Issues in YouTrack {#create-issue data-title="Creating Issues"}

BuildMaster supports creating issues in YouTrack. This functionality could be useful to add tasks or work items for non-technical or non-BuildMaster users indicating that something must be done once a build is deployed to a certain environment. To create issues in YouTrack, use the `YouTrack::Create-Issue` operation in a build or deployment plan, supplying a title and description.

## Finding YouTrack Issues {#finding-issues data-title="Finding Issues in a Plan"}

To manipulate issues, BuildMaster must reference them in an OtterScript plan. To do this, use the `YouTrack::Find-Issues` operation to query a bulk list of issue IDs as an [OtterScript list](https://docs.inedo.com/docs/executionengine/otterscript/strings-and-literals#list-map) that can be looped using a [`foreach` statement](/docs/executionengine/otterscript/statements-and-blocks/loop).

## Updating Issues Statuses {#updating-issues data-title="Updating Issues"}

Updating an issues status is commonly done to indicate that an issue was deployed to a new environment and "ready for testing", or "approved by QA" if an [automated check](/docs/buildmaster/verification/pipelines/approvals-and-gates/automated-checks) is configured in a BuildMaster pipeline. Use the `YouTrack::Change-Issue-State` operation and supply the new state along with the issue ID obtained from the [find](#finding-issues) operation.

Another example is our public issue tracker. For example, [ProGet's issues](https://inedo.myjetbrains.com/youtrack/issues/PG) associated with a specific release managed in BuildMaster are automatically changed from "Awaiting Release" to "Completed" when deployment occurs to our Release stage.

## Adding Comments to Issues {#issue-comments data-title="Commenting on Issues"}

The most common use-case for commenting on issues is to add "deployed by BuildMaster to $EnvironmentName", likely with links directly to the build or artifact, possibly using the [CI Badge API] to generate links with images.To add comments to an issue, use the `YouTrack::Add-Comment` operation along with the issue ID obtained from the [find](#finding-issues) operation.

## Preventing Deployment Unless All Issues are Resolved {#automated-check data-title="Automated Checks"}

BuildMaster supports two automated checks out of the box that can be used without further configuration once YouTrack is set up to [associate issues](#issue-source) using the following [automated checks](/docs/buildmaster/verification/pipelines/approvals-and-gates/automated-checks):

{.docs}
 - **All Issues Closed** - ensures that deployments are blocked to a certain pipeline stage unless all issues are marked as "resolved" in YouTrack; note that this is a separate flag (similar to "Archived") from the issue status which could contain the text "closed" or "resolved"
 - **All Issues in Status** - ensures that deployments are blocked to a certain pipeline stage unless the "state" field in YouTrack matches a specified status defined in a BuildMaster pipeline
