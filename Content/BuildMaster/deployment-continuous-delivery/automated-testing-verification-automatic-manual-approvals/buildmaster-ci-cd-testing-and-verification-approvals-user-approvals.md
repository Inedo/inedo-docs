---
title: "User Approvals"
order: 1
---

There are two types of manual approvals: user and group. A user-based approval can only be entered by the specified user, whereas a group-based approval can be entered by any members of the specified group. You can also specify that more than one member of a particular group must approve a build before deploying to a stage.

## Configuring User Approvals 

You can view the User Approval function by selecting "view pipeline" on the Build Overview tab. 

![Build Overview](/resources/docs/BuildMaster-BuildOverviewv2.png){height="" width="70%"}
Next, select "Add a stage requirement" under Build Stage. 

![Pipeline Stages](/resources/docs/BuildMaster-PipelineStagesv2.png){height="" width="70%"}
The Stage Requirement Check window will populate. Select "Add Manual Approval".

![Stage Requirement](/resources/docs/BuildMaster-Pipeline-StageRequirement.png){height="" width="70%"}
From here, you can configure your user approval. 

![User Approval](/resources/docs/BuildMaster-Pipeline-StageRequirement-UserApproval.png){height="" width="70%"}

Afterwards, a commit prompt will populate at the top of the page. Select "Commit Changes" to implement the user approval. 

![Commit Change](/resources/docs/BuildMaster-Pipeline-Commit.png){height="" width="70%"}

After the user approval is committed, you can view it by clicking on the relevant stage in the pipeline. 

![User Approval](/resources/docs/BuildMaster-Pipeline-DeployBuild-Approval.png){height="" width="70%"}

In either case, when a user approves a build, the following is recorded:

* User's Name
* Group name (if group-based)
* Stage name
* Approval Description
* User Comments
* Date/Time

Users may revoke a manual approval they've given, provided that the build wasn't already deployed to the target stage.