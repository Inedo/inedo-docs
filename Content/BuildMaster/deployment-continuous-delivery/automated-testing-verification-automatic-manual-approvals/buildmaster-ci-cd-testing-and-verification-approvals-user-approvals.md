---
title: "User Approvals"
order: 1
---

User approvals require one or more people to approve a build before it can be deployed to a stage. They are added to a pipeline stage as a stage requirement.

There are two types of manual approvals:

* **User approvals:** A user-based approval can only be entered by the specified user.
* **Group approvals:** A group-based approval can be entered by any members of the specified group. You can also specify that more than one member of a particular group must approve a build before deploying to a stage.

## Configuring User Approvals

You can create a User Approval by selecting "view pipeline" on the Build Overview tab, and selecting "Add a stage requirement" under a pipeline stage.

![Pipeline Stages](/resources/docs/BuildMaster-PipelineStagesv2.png){height="" width="70%"}

Then select "Add Manual Approval" under the Stage Requirement Check window to set up the user approval.

![User Approval](/resources/docs/BuildMaster-Pipeline-StageRequirement-UserApproval.png){height="" width="70%"}

Make sure to select "Commit Changes" at the top of the pipeline page to create the user approval. 

When a user approval is needed at a stage of a release, you will see it under "Manual Approvals" when navigating to "Builds" > "Build Overview".

![User Approval](/resources/docs/BuildMaster-Pipeline-DeployBuild-force.png){height="" width="70%"}

## Forcing a Build Past an Approval

A build may be forced to a stage even if it has not received the required approval. When a build requires an approval, the build will show as blocked. To force a build past an approval, navigate to the build's page, select the blocked build, and select "force".

![Force User Approval](/resources/docs/BuildMaster-Pipeline-DeployBuild-forcedeployment.png){height="" width="70%"}

Note that you can leave a comment if needed when forcing the build.

## Approval Records and Revocation

When a user approves a build, the following is recorded:

* User's Name
* Group name (if group-based)
* Stage name
* Approval Description
* User Comments
* Date/Time

Users may revoke a manual approval they've given, provided that the build wasn't already deployed to the target stage








  
