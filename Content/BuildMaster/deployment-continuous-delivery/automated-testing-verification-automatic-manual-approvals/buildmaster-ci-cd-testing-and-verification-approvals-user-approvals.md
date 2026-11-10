---
title: "User Approvals"
order: 1
---

User approvals require one or more people to approve a build before it can be deployed to a stage. They are added to a pipeline stage as a stage requirement.

There are two types of manual approvals:

* **User approvals:** A user-based approval can only be entered by the specified user.
* **Group approvals:** A group-based approval can be entered by any members of the specified group. You can also specify that more than one member of a particular group must approve a build before deploying to a stage.

## Configuring User Approvals


You can view the User Approval function by selecting "view pipeline" on the Build Overview tab.

Next, select "Add a stage requirement" under Build Stage.

![Pipeline Stages](/resources/docs/BuildMaster-PipelineStagesv2.png){height="" width="70%"}

The Stage Requirement Check window will populate. Select "Add Manual Approval".


![Stage Requirement](/resources/docs/BuildMaster-Pipeline-StageRequirement.png){height="" width="65%"}
From here, you can configure your user approval.


![User Approval](/resources/docs/BuildMaster-Pipeline-StageRequirement-UserApproval.png){height="" width="70%"}


Afterwards, a commit prompt will populate at the top of the page. Select "Commit Changes" to implement the user approval.


After the user approval is committed, you can view it by clicking on the relevant stage in the pipeline.


![User Approval](/resources/docs/BuildMaster-Pipeline-DeployBuild-Approval.png){height="" width="65%"}

## Forcing a Build Past an Approval

A build may be forced to a stage even if it has not received the required approval. However, this requires a special action and a specific permission.

When a build requires an approval, the build pipeline will look like the image below.

To force a build past an approval, navigate to the Build Pipeline page and select "force" under the relevant pipeline stage.

![Force Approval](/resources/docs/BuildMaster-Pipeline-DeployBuild-force.png){height="" width="70%"}

You will also be able to leave a comment when forcing the build.

## Approval Records

When a user approves a build, the following is recorded:


* User's Name
* Group name (if group-based)
* Stage name
* Approval Description
* User Comments
* Date/Time

## Revoking Approvals
Users may revoke a manual approval they've given, provided that the build wasn't already deployed to the target stage.


