---
title: "Pipeline Event Listeners"
order: 2
---

Pipeline Event Listeners allow you to perform certain actions as a build progresses through a pipeline. For example, after a failed deployment to Production, you can send an email:

![buildmaster-pipeline-event-email](/resources/docs/buildmaster-pipeline-event-email.png){height="" width=""}

There are a number of Event Types and Actions that you can combine.

## Pipeline Events Types
There are five pipeline events that you can listen, but a build may not process through all five events.

### Stage-level Events
Stage-level events will occur each time a build is deployed to a stage. When selecting an action for a stage-level event, you can choose to have the action run on either all stages, or select specific stage(s).

There are three stage-level events:
* **Before Stage Deployment**; Immediately before a deployment to a stage. This is commonly used for setting variables.
* **Stage Deployment Successful**; When there was a successful deployment to a stage. This is commonly used for notifications.
* **Stage Deployment Failed**; When there are errors deploying to a stage. This is commonly used for notifications.

If an error occurs while running an action during a Before Stage Deployment, then the deployment targets will not be executed, and the Stage Deployment Failed event will occur.

If an error occurs while running an action in Stage Deployment Successful, then deployment to the stage will be considered failed. This will then cause the Stage Deployment Failed to occur.

### Pipeline-level Events
One pipeline-level event will occur per build:
* **Build Rejected from Pipeline**; A rejected build will not complete the pipeline; this event is commonly used for tearing down temporary environments/resources.
* **Build Completed Pipeline**; After a build has successfully been deployed to the final stage of the pipeline. This is commonly used to set statuses and create new releases.

If an error occurs while running an action in either event, it will not impact the status of the build or release. It will simply show as a failed Pipeline-event Execution under the Build's Deployments & Execution tab.

## Pipeline Event Listener Actions
With the exception of Build Completion Actions, you can perform any action in response to any event.

### Notification & Clean-up
* **Send an email**; sends an email to the specified addresses with the specified priority, subject, and body
* **Archive ProGet Release**; Sets the status of the related ProGet Project Release to archived.
* **Tag Release in Git**; Tags the Git repository and pushes a tag to a Git repository.

### Build Completion Actions
These actions may only be used with the *Build Completed Pipeline* event.
* **Set Build as Deployed or Rejected**; Marks this build as either deployed or rejected.
* **Cancel Other Releases**; Sets lower-sequenced, active builds and releases to a canceled status.
* **Create New Release**; Creates a new release by incrementing the last number of the current release

By default, new pipelines will be configured with a Set Build as Deployed or Rejected event listener. We do not recommend removing this event—otherwise, you will have to click Set Status to Deployed after deploying to the final stage.

### Variable & Execution-related
These event listeners help you add flexibility to your pipeline and enable advanced scenarios.
* **Set variable value**; creates or updates a build variable, or sets a temporary variable
* **Execute a Script**; Executes the specified OtterScript, PowerShell, etc. script

The script execution listener is particularly powerful, in that, you can use it to do anything from deprovisioning temporary environments to automating build/release status in BuildMaster.

### Advanced Usage:  “Run on Server”
Under the hood, most of the event listeners implemented using [OtterScript & Operations](/docs/buildmaster/otterscript-execution-engine/buildmaster-otterscript-overview) that run on the local BuildMaster server (i.e. `localhost`).

In some cases (especially when Executing a Script), you may use a different server name. On the advanced tab of these OtterScript-backed operations, you can select a different server to use. 

## Best Practices

### Don't Overuse Event Listeners
Because you can run an OtterScript with a Pipeline Event Listener, you can theoretically do anything you want. But in general, these steps should be reserved for things like sending notifications, setting statuses, and tagging in Git.

###  Stage Deployment Successful vs. Build Completed Pipeline
Oftentimes, you'll want to perform some action once a build has been deployed to production. For example, tag the commit in Git or tear down a temporary environment.

There are two events that you can choose from to perform this action:
* Stage Deployment Successful
* Build Completed Pipeline

The main difference is error-handling and visibility.
 * **If you use a Stage Deployment Successful**, then the stage will be considered failed when an error occurs.
 * **If you use a Build Completed Pipeline**, then the deployment will always show as successful, but the error will be buried in the Deployments & Executions tab of the build

For example, in our Git pipeline templates, we create the Git Tag event listener to use the Build Completed Pipeline event by default. The theory being, Git tagging isn't all that important (and very easy to manually do later)—so if it fails, it shouldn't look like a failed deployment in production. 

On the other hand, you may not be bothered by failed production deployments (you can easily override the status, anyways)—so you could just as easily use a Stage Deployment Successful event.
