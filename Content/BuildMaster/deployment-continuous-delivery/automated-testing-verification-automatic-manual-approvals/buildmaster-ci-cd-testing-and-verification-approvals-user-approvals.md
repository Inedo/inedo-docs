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

Next, select "Add a stage requirement" under the relevant pipeline stage.


![Pipeline Stages](/resources/docs/BuildMaster-PipelineStagesv2.png){height="" width="70%"}

Select "Add Manual Approval" under the Stage Requirement Check window.

![Stage Requirement](/resources/docs/BuildMaster-Pipeline-StageRequirement.png){height="" width="65%"}

From here, you can create your user approval.

![User Approval](/resources/docs/BuildMaster-Pipeline-StageRequirement-UserApproval.png){height="" width="70%"}

Afterwards, a commit prompt will appear at the top of the page. Select "Commit Changes" to create the user approval.

After the user approval is committed, you can view it by navigating to Builds > Build Overview and selecting the relevant pipeline stage.

![User Approval](/resources/docs/BuildMaster-Pipeline-DeployBuild-force.png){height="" width="70%"}


## Forcing a Build Past an Approval

A build may be forced to a stage even if it has not received the required approval. This requires a special action and a specific permission.


When a build requires an approval, deployment will show up as blocked, with the option to force the deployment and leave a comment if needed.

To force a build past an approval, navigate to the Build Pipeline page and select "force" under the relevant pipeline stage.

![Force User Approval](/resources/docs/BuildMaster-Pipeline-DeployBuild-forcedeployment.png){height="" width="70%"}

## Approval Records and Revocation

When a user approves a build, the following is recorded:

* User's Name
* Group name (if group-based)
* Stage name
* Approval Description
* User Comments
* Date/Time

Users may revoke a manual approval they've given, provided that the build wasn't already deployed to the target stage








  