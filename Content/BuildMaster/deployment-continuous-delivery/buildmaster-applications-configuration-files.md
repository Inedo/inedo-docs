---
title: "Configuration Files"
order: 7
---


:::(Internal) (TODO List)
- Fixing bug about Deployment Record always getting added, even without file changes (see MyInedo on BM)
- Screenshot for Deployment History
:::

BuildMaster can manage your application's environment-specific configuration files and let you change and deploy environment-specific configuration values without requiring code changes, manually logging into servers, or deploying the entire application.

![buildmaster-configfile-instances](/resources/docs/buildmaster-configfile-instances.png){height="" width="50%"}

This allows you to secure sensitive data (connection strings, API Keys, etc) and change/deploy configuration files without requiring code changes. BuildMaster will also record when this configuration is changed and is deployed.

## What is an Application Configuration File?

Many applications read values like database connection strings, third-party service URLs, API keys, etc. from a configuration or settings file. The name of this file will vary from platform to platform (and even application to application), but common examples include:

| Platform | Common Configuration Filenames |
| --- | --- |
| .NET | `web.config`, `connectionStrings.config`, `appSettings.json` |
| Java | `config.properties`, `configuration.properties` |
| PHP | `config.php`, `config.ini` |
| JavaScript | `default.json`, `config.js` |
| Python | `config.ini`, `settings.ini` |

These types of configuration files are often a poor fit for source control.

Not only will values stored in these files change from environment to environment, but they're often sensitive. For example, most organizations don't want all developers (with source control access) to also see the production database connection string.

As a result, most organizations end up with multiple "instances" of the same file, strewn across different servers and environments.  This leads to a number of maintenance challenges, including:
* Accidental overwriting; at deployment time, the values stored in source control can be overwritten with the file stored on disk
* Forgotten updates; because the values are rarely updated, it's easy to forget to update when deploying
* Changes outside deployment; it's not uncommon to need to change passwords or URLs without deploying a new version of the application
* Scripting is complex; managing and trying to replace secrets in the file with as script can be tricky and difficult to maintain

These are the problems that  BuildMaster's application configuration files were designed to help solve.

## Creating an Application Configuration File
You can create a configuration file by navigating to the application's Settings > Configuration Files page. In BuildMaster 2022.9 and earlier, you won't see the following screens (nor will you be able to select variable replacement options);  instead you'll only be able to create a [Classic Configuration Files](#classic-configuration-files).

### Importing from your Git repository
If you have a Git connection configured for your application, you'll first be prompted to import the contents of an existing file in your repository.

![buildmaster-configfile-create](/resources/docs/buildmaster-configfile-create.png){height="" width="50%"}

The configuration file list will initially contain common configuration names from your repository, but you can also type in the name of a file to search if it's not in the list. The contents of the file you selected will be imported into each "instance" of the new application configuration file.

### Specifying Instances
After selecting a file to import -- or, if you are creating a blank file -- you'll be prompted to specify the instances that you'd like to create.

In BuildMaster, a configuration file "instance" is used to store different file contents based on the environment you'll deploy the file to. These will show up as "tabs" when you edit the file.

![buildmaster-configfile-create-instances](/resources/docs/buildmaster-configfile-create-instances.png){height="" width="50%"}

Instances can be edited, deployed, and secured separately, and you can add or delete instances later.

### Configuration File Name and Description
The final step in creating a configuration file is selecting the name, description, and other file options.

![buildmaster-configfile-create-name](/resources/docs/buildmaster-configfile-create-name.png){height="" width="50%"}

Note that you can rename and edit all of these other options later.

:::(Info) (💡 Best Practices: Configuration File Naming )
Use the same filename that you'll deploy to disk later. This makes it easier to follow and understand for other users. 

If you have several configuration files with the same name - for example, that are deployed to different directories within the  application  - include the subdirectory in the file name.
:::

### Advanced: Securing Instances
The "Instance security" tab allows you to associate each instance with an [Environment](/docs/buildmaster/administration-agents-and-infrastructure/environments). By doing so, you can control which users can view, edit, and deploy different instances of a configuration file. For example:
* A configuration file instance that's associated with a Production environment cannot be deployed to a server that's not in the Production environment.
* Users can view configuration files instances in all instances except Production

By default, BuildMaster will automatically map instances to environments by name. If your instances have different names than your environments, you'll want to map these yourself.
 
### Advanced:  Deployment Options
Prior to deploying a configuration file instance to disk, BuildMaster can replace variables or perform advanced processing that allows you to show/hide sections based on if/else statements. This is  be particularly useful when you want to include build- or release-specific information (such as `$ReleaseNumber` or `$Branch`) as configuration values.

#### Option: Replace variables ($MyVar, $ReleaseNumber, etc.)
When this option is selected, BuildMaster will use the processing engine that OtterScript uses for expanding variables in strings.  Basically, this means that anything that "looks" like a variable will be replaced with the appropriate value; see [OtterScript's automatic variable expansion](/docs/executionengine/otterscript/strings-and-literals#string-literal-syntaxes) to learn more.

:::(Warning)
When BuildMaster tries to deploy a file that has a variable expression that can't be mapped to a variable (e.g. the `$ha2` in `dbPassword =  ke$ha2!cool`), an error will be raised during deployment. 
:::

<div>To escape a variable expression, prefix the dollar-sign character (<code>$</code>) with a grave apostrophe (<code>`</code>). For example, <code>dbPassword =  ke$ha2!cool</code> would need to be escaped as <code>dbPassword =  ke`$ha2!cool</code>.</div>

#### Option: Advanced processing (<%if ...%>, <%foreach...%>, etc.)
When this option is selected, BuildMaster will use OtterScript "tags" in addition to variable replacement to process the text. This allows you to use:
* `if/else` statements to display different blocks of text
* `foreach` statements to display a dynamic blocks of texts

A "tag" is an OtterScript code fragment that's placed between a `<%` and `%>`. For example:
```
<% if $DebugMode { %>
# additional configuration for debug mode
debug_mode = true
debug_timeout = 28
debug_release = $ReleaseNumber
<% } else { %>
debug_mode = false
%>
```

When the file is deployed, BuildMaster will test the `$DebugMode` variable, and write one of the two blocks as coded.

:::(Warning)
Although a tag expression (`<%`) is much less likely to occur in a configuration file, you'll also need to escape it similarly to variables (<code>&lt;`%</code>).
:::


<div>To escape a tag expression, prefix the percent-sign character (<code>%</code>) with a grave apostrophe (<code>`</code>). For example, <code>apiKey =  a~n!f3&lt;%vs3a</code> would need to be escaped as <code>apiKey =  a~n!f3&lt;`%vs3a</code>.</div>

See [OtterScript Text Templating](/docs/executionengine/overview/text-templating) to learn more.

## Advanced: Use Key/Value Pairs and a Template
:::(Info) (💡 Best Practice: Simplify Your Configuration Files Instead)
Instead of using this feature, we recommend using application configuration files that only environment-specific settings when practical. For example, when using XML-based `.config` in .NET, you can "split" the file using the `configSource` attribute.
:::

In some cases, an application configuration file needs to be substantially similar across environments.  For example, the `web.config` file used by ASP.NET often contains *dozens* of runtime settings - but only a handful will change from environment to environment.

While the runtime settings rarely change, editing each instance in BuildMaster is time consuming and error prone. This is where BuildMaster's Key/Value Pairs configuration files may come in handy.

![buildmaster-configfile-template](/resources/docs/buildmaster-configfile-template.png){height="" width="50%"}

### Working with Key/Value Pairs Configuration Files
To create a Key/Value Pairs configuration file, select "All Options" in the bottom-left corner of the modal window. From there, you can select "Use Key/Value Pairs and a Template", select your instances, and create the file from there.

Like a standard configuration file, a Key/value Pairs configuration file will have a tab for each instance, and allow you to associate instances with environments so that you can restrict users from viewing, editing, and deploying them.

#### "Template" tab
A Key/Value Pairs file will have a tab named "Template" that should contain the majority of the application configuration file. Any environment-specific settings should be expressed as variables (e.g. `$DbConnString`, `$WorkingDirectory`, etc) that will be replaced at deployment time. 

For example a snippet of a template may look like this:
```
<appSettings>
  <add key="Core.BaseWorkingDirectory" value="$BaseWorkingDirectory"/>
  <add key="Core.DbConnectionString" value="$DbConnectionString"/>
  <add key="Core.MinimumLogLevel" value="$MinimumLogLevel" />
</appSettings>
```

Any variable expressions that you don't want replaced at deployment time need to be escaped using a grave apostrophe. For example, `invoiceFormat = ${ddddd}` should be <code>invoiceFormat = `${ddddd}</code>

#### Instances tab
The instances in a Key/value Pairs configuration file should contain only the keys/values that you want to replace in the template. By default, instances are edited using a visual editor that makes it easy to add and edit keys:

![buildmaster-configfile-instance-editor](/resources/docs/buildmaster-configfile-instance-editor.png){height="" width="50%"}

The editor will detect missing keys and prompt you to add them to the instance. 

Under the hood, instances are stored as basic key/value text files and you can also edit them in Text mode. An instance may look like this:
```
BaseWorkingDirectory=c:\WebApps\HDars
DbConnectionString=server=intdbsv1;user=sa;password=hunter42
MinimumLogLevel=40
```

Note that, you'll also need to escape any variable expressions that you don't want replaced at deployment time. For example, `dbPassword=ke$ha2!cool` will most likely throw an error, and should be <code>dbPassword=ke`$ha2!cool</code>.

## Deployments
BuildMaster can deploy configuration files in two ways; OtterScript or Manual deployments.  When a configuration file is deployed, the deployment will be recorded which will track which instance, release, and the user that deployed the config file.

### Deploy Using OtterScript
The most common method of deployment is the Deploy Configuration File operation (`Deploy-ConfigFile` in OtterScript). When this operation is executed during a deployment, the version of the configuration file instance associated with the release currently in context will be deployed to the target path. If there is an existing file at the target path, it will only be overwritten if its text contents are different than the configuration file text in BuildMaster.  
```
Deploy-ConfigFile
(
    Instance: Testing,
    To: c:\sites\ProfitCalcWeb,
    ConfigFileName: appsettings.json
);
```

### Deploy Manually
Configuration files may also be deployed outside the context of a deployment. It is generally not recommended to deploy configuration files in this manner because much of the context associated with the script-based deployment is lost (e.g. release number and build number). One-off deployments should be reserved for emergency cases that require a configuration change immediately without the overhead of the traditional release cycle.

When manually deploying your configuration file, your deployment options will be defaulted to your last deployment location.  This will also allow you preview which changes will be deployed compared to the file on disk.

![BuildMaster-ConfigurationFile-ManualDeploy](/resources/docs/BuildMaster-ConfigurationFile-ManualDeploy.png){height="" width="50%"}

## Deployment History
Deployment history for configuration file instances is stored for configuration files deployed from BuildMaster. The instance, version number, user, and date of deployment is visible, as well as the ability to compare arbitrary versions to highlight what changes were made to an instance.

:::(Internal) (TODO: Screenshot would be nice)
:::

## Classic Configuration Files

Configuration files created prior to BuildMaster 2023 are called "Classic" configuration files. The main difference with Classic configuration files is that the editor UI exposes more options, thus giving access to "hidden" features and more granular control over the configuration files.

However, we've found that Classic configuration files are too complicated (too many options), which is why they're hidden away like this. We don't plan to deprecate the features here, but we don't recommend using them either.

### Templating Behavior
A Classic configuration file can have multiple templates, and each instance may (or may not) be associated with that template. When an instance isn't associated with a template, it's deployed as normal.

###  XSLT-based templating
Classic configuration file templates or their instances may be used as XSLT stylesheets and XML data that will be automatically transformed before deployment. In most cases, "XSL Stylesheet as Template" is the desired transform option because the XML data would be stored in the template instances. However, you can configure 

### Mandatory "Safe" Variable Replacement
Variable expressions in a Classic configuration file are always replaced, and there's no option to choose "do not process" or "advanced processing". However, instead of giving an error when deploying, a warning is issued. This was mostly to remain compatible with pre-BuildMaster 6.2 features (see [Legacy Features](/docs/buildmaster/installation-maintenance/buildmaster-legacy/buildmaster-legacy-features#config-file-variable-syntax) to learn more).