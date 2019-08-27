---
title: Releases in BuildMaster
sequence: 100
show-headings-in-nav: true
---
A release is an event where a planned set of changes are tested and delivered to production, or more specifically, a final pipeline stage. Releases can vary in conceptual size, from a major product launch, to a single-line change rushed to production in an emergency. The deployment unit that is promoted through a pipeline in order to effectively deploy a release is referred to as a [build](/docs/buildmaster/builds/overview).

In addition to the application code you want to deploy, releases have several associated features available to automate and coordinate the software release process:

{.docs}
- Dates and milestones
- Issues and changes
- Configuration file changes
- Database change scripts
- Release notes

## Creating Releases {#creating-releases data-title="Creating Releases"}

After creating an [application](/docs/buildmaster/modeling-applications/applications) and a [pipeline](/docs/buildmaster/core-concepts/pipelines), a release may be created for an application. A release has the following initial properties:

{.docs}
- **Release template** - if one exists, a [release template](templates) may be selected to determine variable prompts, default pipeline, and more
- **Release number** - is a one-to-three part number (e.g. 88, 2.4, or 4.1.8) that uniquely identifies a release within an application
- **Release name** - is  an optional alias you can use to create a friendlier release identifier; it is not unique within an application
- **Pipeline** - is the sequence of stages and approvals that builds are promoted through

Once a release is created, you can add [configuration variables](/docs/buildmaster/administration/configuration-variables) that can be used by deployment plans at runtime, target dates that will be show on a [calendar](calendars), and so on.

## Status and Lifecycles {#status-lifestyles data-title="Status and Lifecycles"}

A release can be in one of three statuses that reflect the state of development:

{.docs}
- **Active** - builds are being created and deployed through pipelines
- **Deployed** - a build was deployed to the final stage of its pipeline
- **Canceled** - no build was deployed to the final stage, and builds are no longer being created or deployed; all builds are also rejected

## Target Dates {#target-dates data-title="Target Dates"}

Release target dates can be configured to specify a milestone or window of time during a release along with a description. These dates can be added to a [calendar](calendars) view for high-level visibility. Common examples include:

 - indicating the future release date
 - expected range of dates when UAT can expect a build that they can test
 - estimated date(s) when a pre-release version is shipped