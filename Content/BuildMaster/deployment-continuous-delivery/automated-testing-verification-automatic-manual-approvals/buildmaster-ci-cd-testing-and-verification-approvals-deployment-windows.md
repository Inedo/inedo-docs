---
title: "Deployment Windows"
order: 2
---

Deployment windows allow you to restrict the days and times that builds may be deployed. They can be used in conjunction with manual deployments to enforce existing policies, or as part of a continuous delivery pipeline to ensure deployments occur within known times. Like [user approvals](/docs/buildmaster/deployment-continuous-delivery/automated-testing-verification-automatic-manual-approvals/buildmaster-ci-cd-testing-and-verification-approvals-user-approvals), a build may be forced to a stage even if it's not within the specified window. However, this requires a special action and a specific permission.

#### Configuring Deployment Windows

A deployment window consists of day of the week, as well as a start and end time. 

You can view the Deployment Windows function by selecting "view pipeline" on the Build Overview tab 

![Build Overview](/resources/docs/BuildMaster-BuildOverviewv2.png){height="" width="70%"}

Next, select "Add a stage requirement" under Build Stage. 

![Pipeline Stages](/resources/docs/BuildMaster-PipelineStagesv2.png){height="" width="70%"}

The Stage Requirement Check window will populate. Select "Add Deployment Window". 

![Stage Requirement](/resources/docs/BuildMaster-Pipeline-StageRequirement.png){height="" width="70%"}

From here, you can configure your deployment window. Time must be entered in military time. When the end time is earlier than the start time, the window will carry over to the following day. For example, specifying "Sundays after 11:00pm and before 2:00am" will create a three-hour window from Sunday at 11:00pm until Monday at 2:00am. 

![Configure Deployment](/resources/docs/BuildMaster-Pipeline-StageRequirement-CreateDeployment.png){height="" width="70%"}

Afterwards, a commit prompt will populate at the top of the page. Select "Commit Changes" to implement the deployment window. 

![Commit Change](/resources/docs/BuildMaster-Pipeline-Commit.png){height="" width="70%"}

You can now view the deployment window under Pipeline Stages.

![View Deployment](/resources/docs/BuildMaster-Pipeline-StageRequirement-Deployment-final.png){height="" width="70%"}

When there are multiple deployment windows specified, they are treated like an "or" condition; that is, as long as one of the deployment windows is valid, a deployment may proceed.