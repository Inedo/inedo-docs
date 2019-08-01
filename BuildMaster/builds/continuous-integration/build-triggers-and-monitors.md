---
title: Build Triggers & Scheduled Jobs
sub-title: Automatic Build Triggers & Scheduled Jobs
keywords: scheduled-jobs, monitors, webhooks, repository-monitors, buildmaster, continuous-integration
sequence: 200
---

An Automatic Build Trigger in BuildMaster allows users to trigger an action like a new build or running of a custom plan based on a repository monitor or a webhook. You can even schedule this action to any custom internal that suits your needs. This automation can be used as part of your continuous integration process and developers can introduce thier new code to a project and build and test it while ensuring that other environments remain unchanged until further verification and validation steps can occur in the pipeline.  

_As of version 6.1.11 __Webhooks__ and __Repository Monitors__ will be referred to as Triggers and will technically function the same way but with additional configuration options_

There are three types of build triggers:
 * __Repository Monitors__: These will monitor a git repository for new commits and will create a new build on active releases.
 * __Webhook monitors__: These will create a new build on active releases whenever a configured webhook is triggered. 
 * __Schedule monitors__: These monitors can be configured to run any custom plan or create new builds on active releases at a scheduled interval. 

### How to Configure an Automatic Build Trigger
As of version 6.1.11 Users can create or modify Build Triggers from either the Administration page under "Build Triggers & Scheduled Jobs" or from within an Application under Builds > Triggers. 

_In versions prior to version 6.1.11 webhooks and triggers are set up separately in thier own tabs within applications (under builds) or in the Administration section (under Repository Hooks & Monitors)._

These pages will each show you a listing of any existing Build Triggers or Scheduled Jobs. At the bottom of the list click the button called "Create New Build Trigger".

Next you will be prompted to select the trigger source which is either a Repository Monitor, a Webhook Monitor, or a Build Trigger (which can be scheduled). 


### Repository Monitor

-  __Run for application/group__: This will default to your current application when adding a trigger from your application, otherwise you will need to select the application or group that this trigger will run on behalf of.
- __Repository URL__:  This is the url of your repository and can be part of your credentials if they've already been created. 
- __Branch__: This is the specific branch to me monitored. Default is all branches.
- __Action__: You will have three choices of actions to take when the monitor is triggered.
    1. __Create new build in active releases__: This will kick off a new build for your application.
    2. ___Execute a custom plan__: This option allows you to run any build plan whether it is in a pipeline or not, even at the system level.
    3. __Nothing (disabled)__: this essentially disables the trigger and makes it inactive.
- __Create Variables__: Checking this box will assign values to Branch and CommitHash directly from the repository.    
- __Name__: The name of this Build Trigger. This will need to be unique to the scope they are in (system, application, group)

##### Connection/Identity
If you haven't already created your resource credentials for the repository, this can be done here.

##### Advanced
The advanced tab  is where you can set up a scheduled time to monitor your repository for changes. The default is every 4 minutes but can be set for any interval.

### Webhook Monitor

For __Webhook Monitors__ there are fewer options. 

-  __Run for application/group__: This will default to your current application when adding a trigger from your application, otherwise you will need to select the application or group that this trigger will run on behalf of.
- __Secret Token__: This value is a GUID generated automatically to use with your external webhook configuration.  
- __Action__: You will have three choices of actions to take when the monitor is triggered.
    1. __Create new build in active releases__: This will kick off a new build for your application.
    2. ___Execute a custom plan__: This option allows you to run any build plan whether it is in a pipeline or not, even at the system level.
    3. __Nothing (disabled)__: this essentially disables the trigger and makes it inactive.
- __Create Variables__: Checking this box will assign values to Branch and CommitHash directly from the repository.    
- __Name__: The name of this Build Trigger. This will need to be unique to the scope they are in (system, application, group)
- __Url__: Your webhook endpoint specific to this build trigger.


### Scheduled Jobs
Scheduled Jobs is a feature that was first implemented in version 6.1.11. 
 
A common use for Scheduled Jobs is for implementing continuous integration using a repository that would only be checked once per day or week to ensure all code changes have been submitted. 

Another possible scenario for a Scheduled Job would be to monitor an output folder for new files at a scheduled time. i.e. If your company has a folder that developers use for their build output. BuildMaster can automatically check that path every few minutes for new files and create a new build once the files are in the folder. Here is an [example](https://buildmaster.inedo.com/applications/42/) of how this works.


#### How to Configure a Scheduled Job
To create a Scheduled Job, go to the administration page and click "Build Triggers & Scheduled Jobs". From here you will see a listing of any existing Scheduled Jobs or Build Triggers. At the bottom of the list click the button called "Create New Scheduled Task".

You can now begin to configure your Scheduled Job by entering values for these settings:


- __Schedule/Run at time__: The schedule determines when the Scheduled Job will run and how often. 
- __Action__: You will have two choices of actions to take when the monitor is triggered.
    1. ___Execute a custom plan__: This option allows you to run any build plan whether it is in a pipeline or not, even at the system level.
    3. __Nothing (disabled)__: this essentially disables the trigger and makes it inactive.
- __Plan__: This is the name of the plan that will run in the first stage of your pipeline.
- __Name__: The name of this Scheduled Job. This will need to be unique to the scope they are in (system, application, group)


#### Monitoring Build Triggers & Scheduled Tasks
You can monitor Build Triggers & Scheduled Tasks by viewing the listings found either in your applications build overview section under the "Triggers" tab, or in the adminstration menu by clicking "Build Triggers & Scheduled Jobs". This will show you a listing of Build Triggers & Scheduled Tasks. 

|Name |Trigger (type)|Action|Scope |State
|---|---|---|---|---|
|Name of your Trigger/Job|Type of Trigger/Job|What action will occur when executed.|Application, Group, or System (not visible in applications)| Last run time. Next run time. Current state of the Trigger/Job
