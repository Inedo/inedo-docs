---
title: "HOWTO: Add Manual Steps to Automated Deployment"
order: 1
---

When working with a [deployment pipeline](/docs/buildmaster/deployment-continuous-delivery/buildmaster-pipelines), incorporating [manual deployment steps](/docs/buildmaster/deployment-continuous-delivery/buildmaster-manual-deployment-steps) provides control and flexibility to the process. These could include manual code reviews, data migration or manual configuration that may be required.

This article uses a scenario at GoodHealth" to help illustrate how to add manual deployment steps in BuildMaster.

:::(Info) (⚙️ Manual Deployment Steps in Action at "GoodHealth")
*We are GoodHealth, a healthcare organization, upgrading our electronic healthcare records to a more efficient platform.*

**Challenge**: Before integration testing, we always need to migrate data from our legacy system. Users often forget to do this, and it's  not feasible to automate because it's a UI-based tool.

**Solution**: we will create a manual "stop services" task during the integration stage of the deployment pipeline to allow us to perform this.
:::

## Step 1: Create a manual "Stop Services" task at the Integration stage

We'll start by navigating to our "Scripts" by selecting "Settings" > "Scripts", and then selecting our "Deploy" script.

![Scripts](/resources/docs/buildmaster-scripts-2.png){height="" width="50%"}

This will bring up the OtterScript Visual and Text editor. We'll select the text editor and add ```Stop Services``` as our title along with a description, followed by open braces:

```
# Stop Services
# Ensure that existing services, website containers or clusters are stopped before deployment.
{
}

```
![Text Editor](/resources/docs/otter-editdeploy-texteditor.png){height="" width="50%"}

We'll then select the Visual Editor, showing the deployment step entered. From here we'll add a statement to describe the nature of the task required for this step by selecting "add statement".

![Visual Editor](/resources/docs/otter-editdeploy-visualeditor-stopservices-1.png){height="" width="50%"}

We'll now use the search box to search for and select "Manual Operation".

![Find Statement](/resources/docs/otter-find-statement.png){height="" width="50%"}

We'll enter our task name as ```Migrate Data```, assign it to the ```Admin``` and then select "Save".

![Manual Operation](/resources/docs/otter-perform-manualoperation.png){height="" width="50%"}

With our manual deployment step added, we'll select "Commit Changes" via either the Visual or Text Editor.

![Visual Editor Commit](/resources/docs/otter-editdeploy-visualeditor-commit.png){height="" width="50%"}

![Text Editor Commit](/resources/docs/otter-editdeploy-texteditor-commit.png){height="" width="50%"}

## Step 2: Observe the Manual Deployment Step in Action

Now we have added our manual deployment step, we'll return to our pipeline where the deployment process has now paused at the "Integration" stage.

![Build Pending](/resources/docs/buildmaster-buildoverview-integration-pending.png){height="" width="50%"}

We'll select the grey "Deploying GoodHealth to Integration" message at the top of the page to direct us to the "Executions" page, where we observe the following message in the log output explaining that there is a pending manual deployment step that needs to be completed before deployment can continue:

```
INFO: Task "Migrate Data" assigned to principal "Admin".
INFO: Execution will pause until the task has been marked as performed or the execution is canceled.
```

![Task Pending](/resources/docs/buildmaster-deploy-manual-stop-admin-2.png){height="" width="50%"}

## Step 3: Complete the task

The administrator will log in, and see the message "A manual task requires your attention." They'll select this and are taken to the "My Tasks" page, where they'll find the pending "Migrate Data" task. The administrator will migrate the data as planned, and then select "Mark Complete".

![Task Complete](/resources/docs/buildmaster-task-complete.png){height="" width="50%"}

Alternatively, the administrator can also view pending tasks by navigating to "User" -> "My Tasks".

![User MyTasks](/resources/docs/buildmaster-buildoverview-user-mytasks.png){height="" width="50%"}

From the "Overview" page we'll see that deployment has now progressed to the next stage.

![Pipeline Testing](/resources/docs/buildmaster-pipline-testing.png){height="" width="50%"}

As demonstrated, manual deployment steps are an effective way of providing greater control and flexibility to a deployment pipeline.

## Alternative Scenarios

While this article covered one possible scenario, there are many others in which creating manual deployment steps would be effective, for example:

- A financial organization, adding a security review during the deployment of a new system.
- An airline company, adding a user acceptance test during the deployment of an update to their reservation platform.
- An e-commerce company, adding a localization check during the deployment of their online services in a new region.
