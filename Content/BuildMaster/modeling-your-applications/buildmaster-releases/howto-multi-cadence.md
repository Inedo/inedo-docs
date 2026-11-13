---
title: "HOWTO: Set Up Multi-Cadence Releases in BuildMaster"
order: 3
---

A Lean Platforms multi-release cadence splits your releases into two parallel tracks so that routine, low-risk changes can ship on a predictable schedule, independent of larger, riskier work:

* **Major Release Track**: Major releases cover high-risk changes such as large features or significant updates, and ship on a flexible schedule driven by readiness rather than the calendar.

* **Routine Release Track**: Routine releases cover low-risk changes such as bug fixes and minor updates, and ship on a predictable, fixed cadence (e.g. every two weeks).

Once you have adopted a multi-released cadence, delayed major releases no longer hold up the small changes stakeholders are waiting on, allowing you to ship regular updates more predictably. For a more detailed look at the concept of multi-cadence releases you can read our article [Eliminate Delays and Disruptions in Software Releases](https://blog.inedo.com/lean-platforms/releases).

This page will walk you through setting up a multi-cadence release in BuildMaster. It will cover how to connect your toolchain, create Routine and Major pipelines, automate routine release dates and track everything through BuildMaster's calendar.

## Prerequisites

This guide assumes that you have already:
* [Created your application](/docs/buildmaster/modeling-your-applications/buildmaster-applications-concepts/buildmaster-howto-build-and-deploy-an-aspnet-mvc-application) in BuildMaster and connected it to source control (e.g., GitHub), so that branches and commits are visible on each build.
* Connected the rest of your toolchain to the application, so BuildMaster can show what’s inside each build. For example, [connecting an issue tracker](/docs/buildmaster/modeling-your-applications/buildmaster-applications-issue-tracking) (e.g., Jira) lets issues be associated with builds and releases.

## Step 1: Create a Major Pipeline
In this first step we will create a Major Release pipeline. As major releases carry high risk, their pipelines typically include more stages, multiple manual approvals, and several date windows that map out the run-up to release. Builds are typically promoted manually once the required sign-offs are done. We will look at setting up each of these in this step.

### 1.1 Creating The Pipeline
Major releases are for high risk changes, so often move through more pipeline stages before reaching production. In this step we will add Integration, Testing, Staging, and Production stages. Integration confirms the build works with everything else, Testing covers QA, and Staging mirrors production before the final deployment. 

To [create a pipeline](/docs/buildmaster/deployment-continuous-delivery/buildmaster-pipelines#creating-pipelines) for your major releases, navigate to "Settings" > "Pipelines" then select "Create Pipeline". Then select "Show all" and choose "Main/Release Branch".

![](/resources/docs/buildmaster-pipeline-create-main.png){height="" width="50%"}

### 1.2 Setting Up Automated Gating
Automated gates stop a build from moving forward until it meets certain standards, without anyone having to remember to check. For Major releases we recommend blocking builds that contain noncompliant packages or prerelease dependencies, and requiring that all issues in the release are resolved in your issue tracker. This keeps unfinished work and compliance risks from reaching later stages. Here's how to add these requirements to your stages.

Pipeline stage requirements and gating can be set in a pipeline to control how a build is deployed. They can check that a build meets certain standards before it moves on to the next stage, or ask the person deploying to make certain manual checks.

To configure these, navigate to either your Major or Routine release pipeline, and scroll down to a pipeline stage (e.g. Integration) to configure the stage requirements or variables and prompts. 

For example, at the "Integration" stage, you might want to add a check for no noncompliant packages, so a build containing packages that break compliance rules is prevented from moving forward, or a check for no unstable (prerelease) dependencies. 

![](/resources/docs/buildmaster-pipeline-integration-gates.png){height="" width="50%"}

Or at the "Testing" stage", you might want to create prompts that let the tester choose whether to enable new features after deployment, so the same build can be tested with a feature off and then on without needing a separate build, or how services restart after deployment.

![](/resources/docs/buildmaster-pipeline-testing-gates.png){height="" width="50%"}

### 1.3 Setting Up Manual Gating 
Some checks need human judgment, so we also recommend manual gates for Major releases. Business sign-off at UAT confirms the change does what stakeholders expect, release manager or change approval before Production ensures someone accountable has agreed to the deployment, and prompts confirming the rollback plan and database migration review make sure the riskiest parts have been considered. Here's how to set these up as stage prompts.

### 1.4 Creating Date prompts
Major releases need a longer run-up, so we recommend using date prompts to map out the key milestones leading to release. A realistic set might include feature complete (21 days before the target date), code freeze (14 days before), a regression testing window (10 days before, lasting 7 days), a UAT window (5 days before, lasting 3 days), a go/no-go review (2 days before), and post-release hypercare (from the release date through 5 days after). These give everyone a shared view of what happens when. Here's how to add them.

Beyond the release target date, you can add extra dates to a pipeline, set relative to that target. These are useful for milestones that happen on the way to a release, such as a QA start, or a code freeze.

To set these up, navigate to a release pipeline, select "Edit Details" under "Pipeline Details", and then selecting "Manage Date Prompts".

![](/resources/docs/buildmaster-calendar-manage-date-prompts.png){height="" width="50%"}

Then select "Add Date Prompt". From here you will be able to name the date, how many days after the release target date it will be scheduled, (or before, by entering a negative number, e.g. `-3`) and the duration that this prompt will last.

![](/resources/docs/buildmaster-calendar-add-date-prompt.png){height="" width="50%"}

These will then be pre-populated when creating a new release.

![](/resources/docs/buildmaster-calendar-qa.png){height="" width="50%"}


### 1.5 Setting the Target Date
Finally, set the release target date. For a Major release this date is a target rather than a promise: it should move if the release isn't ready, instead of forcing a risky deployment. Here's how to set it when creating the release.

## Step 2: Create a Routine Pipeline
In this next step we will create a pipeline that will be used for your Routine releases. As routine releases carry low risk and ship on a fixed cadence, their pipelines typically include fewer stages, automated gates such as compliance checks instead of manual approvals, and a handful of date prompts. Because the cadence is fixed, builds can also have their promotions scheduled to land on the release date. This step will cover each of these.

### 2.1 Creating The Pipeline
Routine releases carry low risk, so we recommend keeping the pipeline short: Integration, Testing, and Production. Fewer stages means small changes reach users quickly without waiting at checkpoints they don't need. Here's how to create the pipeline and add these stages.

As with creating a Major release pipeline, start by selecting "Main/Release Branch", and make sure to set the release target date when creating a new routine release.

### 2.2 Setting Up Automated Gating
Because routine releases ship frequently, we recommend relying on automated gates rather than manual approvals. Use the same compliance and prerelease dependency checks as the Major pipeline, plus a "tests passed" requirement, so quality is enforced automatically and nobody becomes a bottleneck. Here's how to add them

### 2.3 Creating Variable Prompts
Variable Prompts can make quick, repeatable testing easier. For example, a prompt at the Testing stage can let the tester choose whether to enable new features after deployment, so the same build can be checked with a feature off and then on without needing a separate build. Here's how to add one.

### 2.3 Setting Target iteration Dates
To keep routine releases predictable, you can set Target Iteration Dates on a pipeline to define when its releases are meant to go out, setting a fixed cadence for your routine releases (e.g., every two weeks on a Friday). BuildMaster then projects upcoming release dates for you.

To set these up, navigate to your Routine pipeline and then select "edit details" under "Pipeline Details". From here set the target iteration dates (e.g., "14" would set a two week cadence for routine releases):

![](/resources/docs/buildmaster-release-edit-iteration.png){height="" width="50%"}

Once set, BuildMaster automatically projects the upcoming target dates for that pipeline and shows them on the calendar as dotted outlines.

![](/resources/docs/buildmaster-calendar-auto-dates.png){height="" width="50%"}

### 2.4 Setting Date Prompts
Routine releases need only a light touch here. We recommend a couple of short prompts, such as a code cutoff 2 days before the target date and a short QA window 1 day before. Here's how to add them.

### 2.5 Scheduling build promotion
Because routine releases follow a fixed cadence, you can schedule builds to be promoted automatically so they land on the release date. To schedule the promotion of individual pipeline stages, navigate to a build in your release, and select the current stage under "Pipeline Progress".

![](/resources/docs/buildmaster-pipeline-stage-select.png){height="" width="50%"}

From here select "schedule" and enter the date and time to promote the build. 

![](/resources/docs/buildmaster-promotion-schedule.png){height="" width="50%"}

## Step 3: Create a release in each pipeline
With both pipelines in place, we will now create a release in each one to see how the two tracks differ in practice. First we will create a Major release and set its target date. The date prompts you defined (such as feature complete, code freeze, and UAT) will pre-populate automatically, giving the release a long run-up with several milestones. Then we will create a Routine release, which will pre-populate with just a code cutoff and a short QA window. Putting the two side by side shows how much more planning a Major release involves compared with a Routine one. It also gives us something to look at in the next step, where we will view both tracks on the calendar.



When [creating a major release](/docs/buildmaster/modeling-your-applications/buildmaster-releases) in your pipeline, set the [release target date](/docs/buildmaster/modeling-your-applications/buildmaster-releases#target-dates) for the scheduled deployment: 

![](/resources/docs/buildmaster-release-create-date.png){height="" width="50%"}

## Step 4: View Target Release Dates on the Calendar
Once you have created releases and builds in your Major and Routine release pipelines, you can view them on the Calendar by selecting "Calendar" on the header. The default calendar will display all target dates for all applications in BuildMaster:

![](/resources/docs/buildmaster-default-calendar.png){height="" width="50%"}

#### Creating Custom Calendars (Optional)
Viewing all of your applications at once through the default calendar can be overwhelming, so Buildmaster lets you create custom Calendars to view only specified applications. 

To create a custom calendar, navigate to "Administration" > "Manage Calendars" (Global Components). Then select "Create Calendar". 

![](/resources/docs/buildmaster-calendar-create.png){height="" width="50%"}

For more details on the options available when creating a customized calendar, read [Creating Custom Calendars](/docs/buildmaster/modeling-your-applications/buildmaster-releases/buildmaster-applications-releases-calendars)