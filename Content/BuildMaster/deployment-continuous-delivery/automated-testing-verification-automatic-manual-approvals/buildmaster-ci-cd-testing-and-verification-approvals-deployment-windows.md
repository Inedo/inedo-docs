---
title: "Deployment Windows"
order: 2
---

Deployment windows allow you to restrict the days and times that builds may be deployed. They can be used together with manual deployments to enforce existing policies, or as part of a continuous delivery pipeline to ensure deployments occur within specified times. 

A deployment window consists of day of the week, as well as a start and end time. When there are multiple deployment windows specified, as long as the build is within one of the deployment windows, it will be deployed to the next stage.

For example, you might create a weeknight production window from 22:00 to 02:00 on Monday through Thursday, which carries over into the following morning. You could also restrict deployments to weekends by creating separate windows for Saturday and Sunday, since multiple windows are treated as an “or” condition.

## Configuring Deployment Windows

You can create a Deployment Window by selecting "view pipeline" on the Build Overview tab, selecting "Add a stage requirement" under any pipeline stage and then selecting "Add Deployment Window". 

![Stage Requirement](/resources/docs/BuildMaster-Pipeline-StageRequirement.png){height="" width="70%"}

From here, you can configure your deployment window.

![Configure Deployment](/resources/docs/BuildMaster-Pipeline-StageRequirement-CreateDeployment.png){height="" width="70%"}

Afterwards, a commit prompt will populate at the top of the page. Select "Commit Changes" to implement the deployment window. 

The deployment window will now be part of the pre-deployment checks when the build reaches that stage of the pipeline.

![View Deployment](/resources/docs/BuildMaster-Pipeline-StageRequirement-force-step2.png){height="" width="70%"}

## Deploying Outside a Deployment Window
Like [user approvals](/docs/buildmaster/deployment-continuous-delivery/automated-testing-verification-automatic-manual-approvals/buildmaster-ci-cd-testing-and-verification-approvals-user-approvals), a build may be forced to the next stage in the pipeline outside of a configured deployment window. To manually promote the build to the next stage, select the build in the pipeline and select "force" under Integration.


You can also leave a comment when forcing the deployment.
