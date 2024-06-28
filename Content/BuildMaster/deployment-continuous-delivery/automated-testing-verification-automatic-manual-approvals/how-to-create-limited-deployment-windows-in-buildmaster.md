---
title: "How To: Create Limited Deployment Windows in BuildMaster"
order: 4
hidden: true
---

When working with a deployment [pipeline](/docs/buildmaster/deployment-continuous-delivery/buildmaster-pipelines), creating restricted deployment windows allow you to control when stages of the pipeline are deployed. These windows are typically scheduled during periods of lower user activity or business impact, such as weekends or late-night hours.

This guide shows how to add a restricted deployment window in BuildMaster, in a scenario where we are ShopExpress, an e-commerce business who wish to implement a restricted deployment window for weekly routine maintenance at the production stage from 3am to 4am on a Wednesday when customer traffic is at a minimum.

## Adding the deployment window

We start by navigating to our "Pipelines" page by selecting "Settings" > "Pipelines", and selecting "edit".

![Pipeline Edit](/resources/docs/buildmaster-pipeline-edit-1.png){height="" width="50%"}

We then navigate to the "Production Stage" of the pipeline, and click "add".

![Production Stage Add](/resources/docs/buildmaster-pipeline-production-stage-add.png){height="" width="50%"}

Next, we will select [Add a Deployment Window].

![Add Deployment Window](/resources/docs/buildmaster-select-stage-requirement-check.png){height="" width="50%"}

We set our deployment window as 03:00AM to 04:00AM on a Wednesday, and then select [Save].

![Create Deployment Window](/resources/docs/buildmaster-create-deployment-window.png){height="" width="50%"}

We now see the window added at the "Production Stage".

![Window Added](/resources/docs/buildmaster-pipeline-production-stage-window-added.png){height="" width="50%"}

Finally we commit our changes at the top of the pipeline page by selecting [Commit Changes].

![Commit Changes](/resources/docs/buildmaster-pipeline-commit-changes.png){height="" width="50%"}

Optionally, we have the choice of scheduling multiple days by specifying this in the "Create Deployment Window" dialog box.

![Multiple Windows](/resources/docs/buildmaster-create-deployment-window-multiple.png){height="" width="50%"}

## Restricted Deployment Windows in Action

We return to our "Build Overview" where the pipeline progression has reached the "Production Stage" but is now paused due to the reason "Not in deployment window", as indicated.

![Pipeline Paused](/resources/docs/buildmaster-build-overview-production-stop.png){height="" width="50%"}

However, in a scenario where the "Testing Stage" has completed earlier than expected, and we are currently in a time frame appropriate for deployment at the "Production Stage", we decide to force the pipeline to progress. We select the "Production Stage", and then select "force".

![Pipeline Halted](/resources/docs/buildmaster-deployment-window-force.png){height="" width="50%"}

We state our reason for deploying now, and select [Force Promotion].

![Force Promotion](/resources/docs/buildmaster-force-build-promotion.png){height="" width="50%"}

This progresses the pipeline on to the "Production Stage", thereby deploying the build entirely.

![Deployed](/resources/docs/buildmaster-pipeline-deployed.png){height="" width="50%"}

### Optional: Scheduling with Additional Requirements

In a similar scenario with additional requirements, we can schedule the "Production Stage" deployment by selecting the "Production Stage" and choosing the "schedule" option.

![Schedule Promotion](/resources/docs/buildmaster-deployment-window-schedule.png){height="" width="50%"}

We can schedule the deployment based on various criteria. In this case, we've set a requirement for manual approval by the lead developer. To meet this requirement, we choose "Immediately when all approval requirements are met" and then select [Schedule Promotion].

![Promotion Scheduled](/resources/docs/buildmaster-pipeline-schedule-promotion.png){height="" width="50%"}

As demonstrated, restricted deployment windows are an effective way of ensuring smooth progression of a pipeline while minimizing impact to your business.

While this article covered one possible scenario, there are many others in which restricted deployment windows would be effective, for example:

- A manufacturing company updating its systems outside of factory operating hours.
- A healthcare facility installing new client record software overnight outside of opening hours.
- A financial organization applying a security fix to it's system while the markets are closed.
