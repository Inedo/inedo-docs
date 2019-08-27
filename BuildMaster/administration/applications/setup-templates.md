---
title: Setup Templates
sequence: 60
show-headings-in-nav: true
---
BuildMaster is used to build and deploy all sorts of applications and projects from developer libraries to mobile applications to legacy monoliths. BuildMaster has many different features that can automate and give visibility into different parts of your build and deploy processes. 

Keeping your use of features consistent for similar application types reduces the tedious work of figuring out how to set up or change things. Maintaining this consistency, however, can be difficult as you scale. Application setup templates simplify this process.

{.attention .best-practice} Application Setup Templates are only available in BuildMaster 6.2 (or BuildMaster 6.1.12+ with preview features enabled)

An application setup template (not to be confused with an [application template](application-templates) or a [text template](/docs/executionengine/components/text-templating)) provides a quick overview of important settings in your application and shows whether the application is properly configured. And if it's not or if you need to change a setting, you can directly do it from that page instead of having to navigate elsewhere to change something.

### Built-in Setup Templates{#built-in data-title="Built-in Setup Templates"}
Inedo provides many setup templates, including:

* CI/CD Application using either GitLab, GitHub, or Azure DevOps (TFS) for source control and issue tracking
* CI Build Importer using either Azure DevOps (TFS), Team City, or Jenkins
* CI/CD Package for either NuGet or Universal Packages
* CI/CD Container with Docker
* Base container image for Docker

These built-in setup templates are a great place to start, but BuildMaster's real power lies in mixing and matching components of these setup templates to best suit how you build your own applications.

### Creating Your Own Templates{#creating-your-own data-title="Creating Your Own Templates"}
To see all templates, go to Admin > Applications > Setup Templates.

Although you can edit built-in templates, we don't recommend it, as these templates may be updated and your edits overridden in future versions of BuildMaster. Instead, use the [Duplicate...] button to create a copy and optionally update applications using it.

There are three main properties of a Setup Template:
* **Name**: uniquely identifies a template and can have no more than fifty characters, numbers (0-9), upper- and lower-case letters (a-Z), dashes (-), and underscores (_)
* **Contents**: a JSON document (see below) describing how the template behaves
* **Hidden**: indicates that the template should be selectable on new applications 

#### Setup Template JSON Format{#format data-title="Setup Template JSON Format"}
* **Title**: a string displayed instead of the name in most places
* **Description**: a string displayed on the application setup page
* **RequiredExtensions**: an array of strings with extension names that the application requires; the application will display a warning in various places (including application setup page) if these are not installed
* **Configuration**: an object with Boolean properties that indicate whether the corresponding advanced setting should be configured; valid properties are `DisplayPlans`, `DisplayPipelines`, `DisplayIssues`, `DisplayConfigurationFiles`, and `DisplayDatabases`
* **Settings**: an array of objects, each with two optional properties (`SettingName`, `SettingPurpose`), and a required property that specifies the type of setting required 

  * Credential: `CredentialType`, `CredentialName`
  * Variable: `VariableName`
  * Pipeline: `PipelineName`
  * ReleaseTemplate: `ReleaseTemplateName`
  * Plan: `PlanName`
  * BuildTrigger: `BuildTriggerName`
  * IssueSource: `IssueSourceName`
  * ConfigurationFile: `ConfigurationFileName`
  *	TextTemplate: `TextTemplateName`

Credential setting objects may also specify a `CredentialName`. `IssueSourceName` and `ConfigurationFileName` can be "*", which indicates any is accepted.

#### Renaming Templates
Although you can't rename a setup template once you've created it, you can duplicate it, adding the new name, and delete the old template.

### Importing and Exporting Setup Templates{#importing-exporting data-title="Important and Exporting"}
To make application setup templates work transparently with application template packages, BuildMaster behaves as follows:

* When you export your application as a template, the associated setup template will automatically be bundled in export package
* If you create a new application using an application template package that you exported, the bundled template will automatically be added to BuildMaster if it doesn't exist in the system
* If a bundled setup template already exists and is different than the template stored in BuildMaster, a warning will be logged during application import; this allows you to inspect and reconcile the differences between the setup template already stored in BuildMaster and in the package 
* The only exception to the above rule is templates starting with the name `inedo.`, which indicates a built-in template

