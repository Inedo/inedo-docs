---
title: "Deployment Windows"
order: 2
---

Deployment windows allow you to restrict the days and times that builds may be deployed. They can be used together with manual deployments to enforce existing policies, or as part of a continuous delivery pipeline to ensure deployments occur within specified times. 

When multiple deployment windows are specified, a build will be deployed as long as it’s within one of the specified windows.

Combining windows lets you cover different needs at once. For instance, to avoid deploying during peak traffic, you might create a weeknight production window from 22:00 to 02:00 on Monday through Thursday, which carries over into the following morning. You could also restrict risky releases to weekends by creating separate windows for Saturday and Sunday. A build can be deployed in any of these windows, and will wait if it arrives outside all of them.

## Configuring Deployment Windows

You can view the Deployment Windows function by navigating to the release's pipeline, then selecting "Add a stage requirement" under a pipeline stage. 

![Pipeline Stages](/resources/docs/BuildMaster-PipelineStagesv2.png){height="" width="70%"}

Select "Add Deployment Window" from the Stage Requirement Check window. From here, you can configure your deployment window.

![Configure Deployment](/resources/docs/BuildMaster-Pipeline-StageRequirement-CreateDeployment.png){height="" width="70%"}

Afterwards, a commit prompt will appear at the top of the page. Select "Commit Changes" to create the deployment window. Now, when a deployment window is part of a stage in your build's pipeline, it can be seen under "Pre-Deployment Checks". 

![View Deployment](/resources/docs/BuildMaster-Pipeline-StageRequirement-force-step2.png){height="" width="70%"}

Note that if you set the end time earlier than the start time, the window will carry over to the following day. For example, specifying “Sundays after 11:00pm and before 2:00am” will create a three-hour window from Sunday at 11:00pm until Monday at 2:00am.

## Deploying Outside a Deployment Window
Like [user approvals](/docs/buildmaster/deployment-continuous-delivery/automated-testing-verification-automatic-manual-approvals/buildmaster-ci-cd-testing-and-verification-approvals-user-approvals), a build may be forced to a stage even if it's not within the specified window. You can "force" the deployment by navigating to the build, selecting the relevant build, and then selecting "force".

![View Deployment](/resources/docs/BuildMaster-Pipeline-ForceDeployment.png){height="" width="70%"}

You can also leave a comment when forcing the deployment.
 

