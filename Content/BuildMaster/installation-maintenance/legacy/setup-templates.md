---
title: "Setup Templates"
order: 6
url-slug: "buildmaster/installation-maintenance/buildmaster-legacy/buildmaster-applications-concepts-setup-templates"
---

:::(Error) (Setup Templates were Removed in BuildMaster 2024)
This feature was mainly used in BuildMaster 6.2 and BuildMaster 7.0 to customize template applications managed by Inedo when creating a new application.

With the introduction of improved application creation in BuildMaster 2022, this feature is obsolete and was removed in BuildMaster 2024; see [Legacy (Deprecated) Features](/docs/buildmaster/installation-maintenance/buildmaster-legacy) to learn more.
:::

BuildMaster is used to build and deploy all kinds of applications and projects, from developer libraries to mobile applications to legacy monoliths. BuildMaster has many different features that can automate and give visibility into different parts of your build and deployment process.

Consistent use of features across similar application types reduces the tedious work of figuring out how to set things up or change things. However, maintaining this consistency can become difficult as you scale. Application setup templates simplify this process.

::: (INFO) 
Application Setup Templates are only available in BuildMaster 6.2 (or BuildMaster 6.1.12+ with preview features enabled). [View a step-by-step tutorial to using our Application Templates](https://www.inedo.com/buildmaster/getting-started-with-application-templates-in-buildmaster).
:::

An application setup template (not to be confused with an [application template](/docs/buildmaster/installation-maintenance/buildmaster-legacy/buildmaster-legacy-application-templates) or a [text template](/docs/executionengine/overview/text-templating)) gives a quick overview of important settings in your application and shows whether the application is configured correctly. And if it's not, or you need to change a setting, you can do so directly from this page instead of navigating elsewhere to change something.

### Built-in Setup Templates
Inedo offers many setup templates, including:

* CI/CD Application using either GitLab, GitHub or Azure DevOps (TFS) for source control and issue tracking
* CI Build Importer using either Azure DevOps (TFS), Team City or Jenkins
* CI/CD Package for NuGet or Universal Packages
* CI/CD Container with Docker
* Base container image for Docker

These built-in setup templates are a great starting point, but the real power of BuildMaster is in mixing and matching the components of these setup templates to find the best solution for building your own applications.

### Creating Your Own Templates
To see all templates, go to Admin > Applications > Setup Templates.

Although you can edit built-in templates, we don't recommend doing so because these templates may be updated in future versions of BuildMaster and your changes may be overwritten. Instead, use the [Duplicate...] button to create a copy and optionally update applicationsthat use them.

There are three main properties of a Setup Template:
* **Name**: uniquely identifies a template that can have no more than fifty characters, numbers (0-9), uppercase and lowercase letters (a-Z), hyphens (-), and underscores (_)
* **Contents**: a JSON document (see below) that describes how the template behaves
* **Hidden**: specifies that the template should be selectable on new applications

#### Setup Template JSON Format
* **Title**: a string of characters displayed in place of the name in most places
* **Description**: a string that is displayed on the application setup page
* **RequiredExtensions**: an array of strings containing the names of extensions that the application requires; the application will display a warning in various places (including the application setup page) if they are not installed
* **Configuration**: an object with Boolean properties indicating whether the corresponding advanced setting should be configured; valid properties are 'DisplayPlans', 'DisplayPipelines', 'DisplayIssues', 'DisplayConfigurationFiles', and 'DisplayDatabases'
* **Settings**: an array of objects, each with optional properties ('SettingName', 'SettingPurpose', 'Optional') and a required property specifying the type of the required setting

  * Credential: `CredentialType`, `CredentialName`
  * Variable: `VariableName`
  * Pipeline: `PipelineName`
  * ReleaseTemplate: `ReleaseTemplateName`
  * Plan: `PlanName`
  * BuildTrigger: `BuildTriggerName`
  * IssueSource: `IssueSourceName`
  * ConfigurationFile: `ConfigurationFileName`
  *	TextTemplate: `TextTemplateName`
  *	Container Sources: `ContainerSourceRequired` (boolean)
  *	Package Sources: `PackageSourceRequired` (boolean)

Credential setting objects may also specify a `CredentialName`. `IssueSourceName` and `ConfigurationFileName` can be "\*", which indicates any is accepted.

Plan settings may also require a `RequiresEdit` property, which indicates the plan must be edited.

#### Renaming Templates
While you cannot rename a setup template once it has been created, you can duplicate it, add the new name, and delete the old template.

### Importing and Exporting Setup Templates
To make application setup templates work transparently with application template packages, BuildMaster behaves as follows:

* When you export your application as a template, the associated setup template is automatically bundled in the export package
* When you create a new application using an exported application template package, the bundled template is automatically added to BuildMaster if it does not exist in the system
* If a bundled setup template already exists and is different from the template stored in BuildMaster, a warning is logged during the application import; this allows you to check and reconcile the differences between the setup template already stored in BuildMaster and the package
* The exception to the above rule are templates that start with the name 'inedo.' which indicates a built-in template; it will always be udpated
