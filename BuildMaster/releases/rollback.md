---
title: Rollbacks
keywords: buildmaster, releases, rollbacks
sequence: 500
---

BuildMaster does not have a special "rollback plan" that's used only in emergencies. Since such a plan would only get tested in rare cases (i.e. when something went wrong), chances are it will be out of date and likely make things even worse.

But what we can do is perform a **"Re-Deploy"**, which can effectively be used to rollback changes. Assuming you have your deployment plan configured correctly. Let's consider the state of a given application.

## Performing a Rollback {#performing-rollback data-title="Performing a Rollback"}

You'll note below that Release 1.0.3 (Build 1) was deployed to Production.

![](/resources/tutorials/roll-back/latest-deployment.png){.screenshot}

### Step 1: Navigate to Promotion Details {#step-one data-title="1. Navigate to Promotion Details"}

To go back to Release 1.0.2, navigate to the promotion details of the production-promoted build for that release.

![](/resources/tutorials/roll-back/previous-version.png){.screenshot}

### Step 2: Re-Deploy to Production {#step-two data-title="2. Re-Deploy to Production"}

You'll notice there's a big button called "Re-Deploy to Production". Clicking on that will run the production deployment plan using 1.0.2 Build 3's context.

### Step 3: Deploy the Artifact {#step-three data-title="3. Deploy the Artifact"}

You'll have the option to deploy the artifact immediately, which is common for most rollbacks as they are generally considered emergencies, or at a specific time in the future.

![](/resources/tutorials/roll-back/deploy-now.png){.screenshot}

The actions in a [deployment plan](/docs/buildmaster/deployments/plans) are designed to look at the *execution context* to determine what to do. In this case the plan will deploy the artifact associated with Release 1.0.2 Build 3\. This will ensure that whatever files were deployed with Release 1.0.2 Build 3 will _**always**_ be deployed with Release 1.0.2 Build 3.

Once the Release 1.0.2 Build 3 has been re-deployed, that information is reflected in BuildMaster.

![](/resources/tutorials/roll-back/rolled-back.png){.screenshot}

And like that, your changes are rolled back. Of course, this isn't a time machine and nothing (not even a time machine) can reliably and perfectly rollback all changes while keeping existing data.

**So, be careful using re-executions / rollbacks.**
