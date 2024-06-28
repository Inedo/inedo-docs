---
title: "HOWTO: Use and Create BuildMaster Application Templates"
order: 8
hidden: true
---

:::(Internal) 
This was adapted from an old tutorial on inedo.com, and doesn't necessarily follow the process/format of the other `HOWTO` articles.
:::
BuildMaster application templates will get you up-and-running with CI/CD in minutes. 

This tutorial will explain how to create a new application in BuildMaster using a built-in template, and demonstrate how to create your own application templates to help automate even faster. 

## Step 1: Create New Application

From the top of BuildMaster, navigate to "Applcations" and click [Create Application]

![gettingstarted_buildmaster_createapp_highlighted](/resources/docs/gettingstarted_buildmaster_createapp_highlighted.png)

This will take you to the “Create New Application” page. Here you will see Standard Applications, Featured Templates, and (at first unpopulated) User Templates.  

## Step 2: Select Template Type

On the "Create New Application" page, you can create an application from scratch with “Blank Application” or use “Clone Application” to copy everything in an existing application. 

For this tutorial, we’ll use the “GitHub CI/CD” template.

![gettingstarted_buildmaster_createapplist](/resources/docs/gettingstarted_buildmaster_createapplist.png)

## Step 3: Name Your Application Template

After selecting the desired template from the list (for this tutorial, we chose GitHub), name the application.

![gettingstarted_buildmaster_githubtemplate.png](/resources/docs/gettingstarted_buildmaster_githubtemplate.png)

These applications will be versioned as you move along the process. Choose one with meaningful names, understandable to other people on your team. 

When you're ready, click [Click Application].

BuildMaster will create the application based on the selected template. After success, the application setup template displays. 

![gettingstarted_buildmaster_appoverview.png](/resources/docs/gettingstarted_buildmaster_appoverview.png)


## Step 4: Adjust/Configure Your Application

On the Application overview page, there is a table with links and descriptions for the different parts of the application you can alter. 

For example, you can connect to a GitHub Repository.
By clicking on [GitHub Reporistory] a pop-up will appear.

![gettingstarted_buildmaster_configgithubrepo.png](/resources/docs/gettingstarted_buildmaster_configgithubrepo.png)

:::(warning)
The GitHub ProfitCalc application is Inedo’s sample application. The secure resource defaults to “Use in application: ProfitCalc,” “Organization name: inedo,” and “Repository: ProfitCalc.” You can change all these elements as needed for your application.
:::

## Creating a BuildMaster Application Template

BuildMaster makes it easy to set up
and save your own, organization- or team-specific templates.  

A unique template allows you to consistently enforce compliance and security standards across new applications.

For example, if your organization is regulated under SOX, and developers are barred from releasing their own work directly into production, you can create SOX-compliant templates that can be approved by compliance officers. 

BuildMaster’s pipelines are highly customizable, and a custom template will ensure pipelines created have specific gates, manual approvals, and so on.  

This allows developers to do as much as compliance allows in your pipelines, blocks them from acting when necessary, and gives end-to-end visibility, even when direct action isn’t possible. 

.:::(Warning) 
The remaining steps of this tutorial are optional.
:::

### Step 5: Review Changes

On the application's overview page, after it's been customized to your particular needs, there will be a prompt about unsaved changes.

Click [Review Changes].

![gettingstarted_buildmaster_reviewchanges.png](/resources/docs/gettingstarted_buildmaster_reviewchanges.png)

### Step 6: Commit Changes

BuildMaster will show the changes made during template configuration (Step 4), highlighting successful changes in green, changes with a warning in yellow-orange, and changes that will cause a break in red. 

The callout at the top reads “There are unsaved changes to the Release pipeline” with options to “Continue Editing” or “Commit Changes” for successes or warnings.

This messaging changes if there are breaks, making it easy to catch problems before they can cause trouble. 

![gettingstarted_buildmaster_commitchanges.png](/resources/docs/gettingstarted_buildmaster_commitchanges.png)

Click [Commit Changes] once you're ready.

### Step 7: Export the Template

Navigate to Settings > Advanced Settings and select "Export as Application Template" at the bottom of the page.

![gettingstarted_buildmaster_advancedsettings.png](/resources/docs/gettingstarted_buildmaster_advancedsettings.png)

A pop-up will prompt you to title the template. You can also optionally enter a description and select a custom icon to display in the “User Templates” section in BuildMaster. 

![gettingstarted_buildmaster_exporttemplate.png](/resources/docs/gettingstarted_buildmaster_exporttemplate.png)

### Step 8: Confirm new Template

Navigate back to the "Create New Application" template select page (per Steps 1 and 2).

You'll now see under "User Templates" your customized application.

![gettingstarted_buildmaster_usertemplate.png](/resources/docs/gettingstarted_buildmaster_usertemplate.png)