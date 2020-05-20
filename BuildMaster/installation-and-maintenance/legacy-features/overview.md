---
title: Overview
subtitle: BuildMaster Legacy Features Overview
keywords: buildmaster, legacy
sequence: 100
show-headings-in-nav: true
---

With the inclusion of the [Inedo Execution Engine](/docs/buildmaster/execution-engine/overview) and [the Inedo Agent](/docs/various/inedo-agent/the-agent) in BuildMaster v5, several existing features were replaced with newer features that were a better fit for the new model. We consider these since-rebuilt features to be "legacy features", and this document will describe the impact of using these features as well as migration guidance to a non-legacy method.

Note that most unused legacy features are hidden from the user interface in order to prevent unintended usage on newer installations; depending on when you first started using BuildMaster, you may not see these features at all.

::: {.attention .technical}
As of BuildMaster v6.2, **all legacy features listed on this page will be removed**. Installations that make use of these features cannot be upgraded to v6.2 directly. See the [BuildMaster v6.2 Migration documentation](62-migration) for more information.
:::

## Legacy Features Index {#index data-title="Legacy Feature Index"}

| Legacy Feature | Replacement Feature | |
|---|---|---|
| Deployment Plans | OtterScript Plans | [learn more](#plans) |
| TCP/IIS Agents | Inedo Agent | [learn more](#agents) |
| Server Groups | Server Roles | [learn more](#server-groups) |
| Server Pools | Server Roles & Resource Pools | [learn more](#server-pools) |
| Extension Configuration/Profiles | Configuration Variables & Resource Credentials | [learn more](#extension-configurations) |
| Build Importers | OtterScript Plans & Tool-specific Operations | [learn more](#build-importers) |
| Source Control Providers | Resource Credentials & Tool-specific Operations | [learn more](#source-control-providers) |
| SCM Build Triggers | Repository Monitors & Webhooks | [learn more](#scm-triggers) |
| URL Build Triggers | Release & Build API | [learn more](#url-triggers) |
| Recurring Builds | Scheduled Jobs | [learn more](#recurring-builds) |
| Issue Tracking Providers | Resource Credentials & Issue Sources | [learn more](#issue-tracking-providers) |
| Issue + Build Association | Automatically Change Issue Status | [learn more](#issues-and-builds) |
| Legacy Configuration Variables | Multi-scope Configuration Variables | [learn more](#variables) |
| Legacy Template Variables | Release Templates | [learn more](#template-variables) |
| PowerShell/Shell Scripts | Script Assets | [learn more](#scripts) |
| Change Controls | Issues, Release Notes, or Otter | [learn more](#change-controls) |
| Variable %-syntax | OtterScript Variable $-syntax | [learn more](#variable-syntax) |
| Configuration File $UnknownVariable Replacement | Escape with `$ | [learn more](#config-file-variable-syntax) |

## Best practice: migrate, but over time...

Generally speaking, you should plan to migrate from using legacy features. In addition to being less performant, less usable, and with less-defined behaviors, legacy features become harder to support -- both internally (i.e. BuildMaster users supporting their users) and externally (our support team).

However, this doesn't need to happen right away. These legacy features will be maintained indefinitely, likely through the end of the product line. Instead, come up with a migration plan that allows you to migrate away from legacy features with minimal business risk; for example, when you need to make changes to an application or deployment plan, you should plan to migrate the legacy feature as well.

## The Legacy Features Dashboard

As of BuildMaster 5.8, the Legacy Feature Dashboard will be a page within BuildMaster that will provide an overview of any legacy features that are being used in an existing installation of BuildMaster, and link to this page with migration guidance. For some legacy features, migration can be handled automatically from the Legacy Feature Dashboard.

### Legacy Plans => OtterScript Plans {#plans data-title="Deployment Plans"}

OtterScript-based Plans are used by the new execution engine; this is a complete rewrite and takes into account years of experience supporting complex, multi-server orchestrations with easy-to-use deployment plans, and incorporates a tremendous amount of technological advancements, including features such as: locking, iteration, asynchronous operation blocks, textual representation, versioning, template parsing, and more.

See [KB#118 (Legacy Plans in BuildMaster)](https://inedo.com/support/kb/1118) for more information and a migration strategy.

### Agents => Inedo Agent {#agents data-title="Agents"}

The Inedo Agent was designed from the ground-up, using years of experience from the Legacy Agents, to support multiple products using a secure, lightweight, highly-optimized, and resilient protocol. See [KB#1039 - The Inedo Agent and Legacy BuildMaster Agents](https://inedo.com/support/kb/1039) for more information and migration strategy.

### Server Groups => Server Roles {#server-groups data-title="Server Groups"}

Server groups were the only way to deploy to multiple servers with a single action in a legacy deployment plan. OtterScript makes deployment in this manner trivial with [iteration blocks](/docs/buildmaster/execution-engine/statements-and-blocks/loop), so there is no longer a need for server groups.

#### Migration Strategy

If you have existing server groups, you can run the Server Group Migration tool from the Legacy Features Dashboard. This will create a matching Server Role for every Server Group in the system and assign the same servers to it if possible, or will log an error with instructions detailing which server group failed to convert.

### Server Pools => Resource Pools {#server-pools data-title="Server Pools"}

Server pools were used to prevent more than one deployment action running on a specific server at a time. This functionality is replaced by the Acquire-Server operation in conjunction with Server Roles.

#### Migration Strategy

Existing server pools can be converted to Server Roles with the Server Group Migration tool. Deployment plans that refer to a server pool should be changed to use the Acquire-Server operation whenever exclusivity of a server is required, e.g.

```
Acquire-Server
( 
    Role: build-servers,
    ServerName => $AcquiredServerName
);
```

and then use the `$AcquiredServerName` in a `for server` context as needed. The acquired server will be released at the end of the execution, or earlier if the Release-Server operation is used.

### Extension Configuration => Extension Configuration Variables or Resource Credentials {#extension-configurations data-title="Extension Configurations"}

Extension configurers were used to configure extensions and provide defaults for common values (e.g. Windows SDK path) used system-wide, and also for common values needed outside the scope of a legacy plan execution (e.g. build importer UI). The primary benefit of the extension configuration variables is that they may be declared at any configuration variable scope. Using $MSBuildToolsPath in the WindowsSDK extension as an example, different configuration variables can be configured at the server level for different build servers and any builds/deployments performed in that server will use its specified MSBuild Tools Path instead of having to override it at the operation level.

#### Migration Strategy

Existing configurers must be converted on an individual basis because sometimes there is not a 1-1 mapping. Visiting the "Configuration" tab on the Extension Overview page will show any values which can be supplied. Note that unlike extension configuration, these values are almost never required and a "best guess" or lookup is done if not set. If for some reason a configuration value is required and not automatically deduced, any operations that require a value will fail and report the missing value as an error in the execution log.

### Build Importers => Operations {#build-importers data-title="Build Importers"}

Build importers were used primarily to pull an artifact into BuildMaster from a Continuous Integration tool like TeamCity, Jenkins, or package manager like NuGet. With the v5 Execution Engine, this is duplicative of what can be done generally with a single operation.

#### Migration Strategy

The specific migration strategy depends on the importer, but the basic idea is to create a plan, and add one (or more) of the following operations to reproduce the behavior of the importer:

{.docs}
 - `TeamCity::Queue-Build`
 - `TeamCity::Import-Artifact`
 - `TFS::Queue-Build`
 - `TFS::Download-Artifact`
 - `Jenkins::Queue-Build`
 - `Jenkins::Download-Artifact`

### Source Control Providers => Resource Credentials and Tool-Specific Operations {#source-control-providers data-title="Source Control Providers"}

Source control providers were used to specify source control server connection information in a single location and to abstract the experience of adding deployment plan actions no matter what source control system was used. This abstraction was both unnecessary and leaky, primarily because different systems have different workflows (e.g. SVN vs Git). The source control specific operations are the replacement for the Get Latest, Get Latest, and Apply Tag family of actions.

Any source control operations may accept the full connection and repository information (either inline or in a Resource Credential), so having the providers becomes duplicative once again.

#### Migration Strategy

Switching to these operations obviously requires legacy plans to be converted, and then Resource Credentials to be created for each provider or repository in the case of SVN which allows multiple repositories to be defined in a single provider. It's helpful to first take note of what operation properties are available for the specific source control system. As an example, the Git::Git-GetSource operation allows the specification of a repository URL, target disk path, branch or tag name, and credentials to connect. The values for these properties will become obvious from the saved data in the provider and Get Latest action, e.g. "Source directory" becomes "target disk path", "Remote URL" becomes "repository URL", and so on.

### SCM Build Triggers => Repository Monitors & Webhooks {#scm-triggers data-title="SCM Triggers"}

SCM build triggers were basic pull-based monitoring against a repository that had no support for branches or debugging. They also relied on a confusing "quiet period" that was only used to prevent directory conflicts when multiple changes/commits were detected. There was also no way to monitor multiple repositories by configuring a single SCM build trigger.

#### Migration Strategy

The replacement for legacy build triggers can either be a [repository monitor](/docs/buildmaster/ci-cd/continuous-integration/build-triggers/repository-monitors) (to poll the repository to changes) or a [webhook monitor](/docs/buildmaster/ci-cd/continuous-integration/build-triggers/webhook-monitors) (to receive events from a repository host i.e. push-based), depending on the use-case. To perform the migration, simply create a new instance of either of those options, and delete the existing SCM trigger.

### URL Build Triggers => Release & Build API {#url-triggers data-title="URL-Triggered Builds"}

With the addition of the Release & Build Deployment API, the functionality that was previously provided by URL-triggered builds became duplicative. The API offers several additional benefits that the URL-trigger did not, including: authentication and user-based authorization using an API key tied to an optional user, variable support, improved logging, and additional functionality such as creating or deployment releases.

#### Migration Strategy

The replacement for URL build triggers is simply adding an [API key](/docs/buildmaster/administration/security/api-keys) with access to the [Release & Build Deployment API](/docs/buildmaster/reference/api/release-and-build), then calling one of the relevant endpoints. More specifically, calling the [Create Build](/docs/buildmaster/reference/api/release-and-build#create-build) endpoint is likely the desired replacement.

### Recurring Builds => Scheduled Jobs {#recurring-builds data-title="Recurring Builds"}

Recurring builds, also known as "build schedules", were the initial method used to create builds on a set, periodic schedule, before SCM-triggered builds took precedence. They are no fully duplicative of Scheduled Jobs functionality, which can run any plan, within an application or globally, to perform the exact same functionality.

#### Migration Strategy

The replacement for Recurring Builds is either adding a [Repository Monitor](/docs/buildmaster/ci-cd/continuous-integration/build-triggers/repository-monitors) (to instead monitor a source control repository for changes vs. timed interval) or, to maintain identical functionality, adding a Scheduled Job (*Admin* > *Build Triggers & Scheduled Jobs* > *Create New Scheduled Job*) with a specific plan that calls `Create-Build`, optionally specifying an application name.

### Issue Tracking Providers => Issue Sources and Tool-Specific Operations {#issue-tracking-providers data-title="Issue Tracking Providers"}

Issue tracking providers, like source control providers, were used to specify connection information to an issue tracker (e.g. JIRA, YouTrack) and abstract integration points (e.g. Create Issue, Append Comment). Also like source control providers, this abstraction is leaky and overly complex. Issue Sources are unique per tool, and unlike issue tracking providers, can accept variables in their tool-specific properties simplifying configuration within BuildMaster.

#### Migration Strategy

Generally, an issue source should be created for each issue tracking provider. Issue tracking providers required configuration at the application level that filtered the retrieved issues. Because issue sources accept variables, this can be done at the system level instead. As an example, the YouTrack issue source can be configured with "Project name" and "Fix version". Values of $ApplicationName and $ReleaseName means that any issues associated with a BuildMaster application whose release name (or number if none specified) matches the fix version will be pulled from YouTrack and displayed on that application's release overview page.

If an issue source is unique to a specific application (i.e. the "Marketing" website is the only project that uses JIRA for issues), it can be defined at the application level on the application settings page.

### Association of Issues and Builds {#issues-and-builds data-title="Issues and Builds"}

Issues are always associated with a release, and in BuildMaster 6.1 and earlier, they may also be associated with up to two builds: Opened (the first build that BuildMaster spotted the issue) and Closed (the first build that BuildMaster noticed the issue was resolved). This feature was primarily intended to help QA identify when they could start testing changes. However, the ability to [Automatically Change Issue Status](/docs/buildmaster/applications/issue-tracking#changing-issue-status) is a better way to notify QA, much more intuitive, and simpler for all.

### Legacy Variables => Configuration Variables {#variables data-title="Variables"}

Refer to the [Legacy Variables documentation](variables) for more information.

### Legacy Template Variables (Release, Build, Execution, Promotion) => Release Templates {#template-variables data-title="Template Variables"}

Legacy template variables were used to specify variables and optionally default values for releases, builds, and executions/promotions (now just deployments). The new method to handle variable prompts during release, package, or deployment time is release templates.

Release templates act as an initial template rather than a "default" or "fallback", separating the concern of the variable values from their specification in the application settings. Once a release or package is created via template, release and package variables are created as necessary, and their values can be edited on an individual basis from the release or package page. Deployment variable values are injected as Runtime Variable values and cannot be changed since they are specific to a single deployment.

#### Migration Strategy

Converting legacy template variables requires inspecting the Legacy Template Variables page and copying those values into a new release template. In cases where an application has a single release template, it will be used as the default for all created releases maintaining the same behavior held by the legacy template variables (which applied to all releases).

Legacy template variables could have various restrictions (e.g. numbers only, dropdown lists) or could be derived from external sources (e.g. TeamCity Build Number). This functionality is replicated by the "Dynamic List" release template variable, and extended by allowing other options (such as multiple selections, restricting values, obscuring values, etc.)

### Percent Variable Syntax => OtterScript Variable Syntax {#variable-syntax data-title="Variable Syntax"}

In previous versions, variables were referenced like Batch script variables by surrounding them with % signs (i.e. `%RELNO%` for release number). That syntax was deprecated in favor of the $ syntax (i.e. `$ReleaseNumber`). While syntactically similar to OtterScript variable syntax which also uses the $ sign, the escaping rules were different (`$$VarName` evaluates to literal `$VarName`). This would generate an error when parsed by the OtterScript Execution Engine where the escape character is the backtick (i.e. <code>`$VarName</code>).

#### Migration Strategy

Migrating the variable syntaxes is rarely required if the % syntax was never used, since the primary difference is the escape character changing from `$` to <code>\`</code>. However, all uses of `%VARNAMES%` must be converted to `$VarName` or `${LongerNameWith.Chars}` if there are non-alphanumeric characters present in the name. Beyond alphanumeric characters, only the "_" (underscore), "-" (dash), "." (period), and " " (space) characters are permitted to be used in variable names. Any other characters will result in a runtime error when parsing the variable names.

For configuration files, you can use the migration tool within BuildMaster to automatically perform the conversion.

### Legacy PowerShell and Shell Scripts => PowerShell and Shell Script Assets {#scripts data-title="Scripts"}

Legacy scripts were scripts accessible from any deployment plan. They are replaced by Script Assets, which may be stored at the system or application level. Script assets do not require parameters to be specified within BuildMaster, they are automatically parsed from built-in help comments explained here: [https://technet.microsoft.com/en-us/library/ff458353.aspx](https://technet.microsoft.com/en-us/library/ff458353.aspx). "Ambient variables" are replaced by supplying the variables directly as arguments to the operation.

#### Migration Strategy

Migrating scripts can be done at the system level by running the Legacy Script Migration Tool on the Legacy Features Dashboard. This will essentially perform a copy/paste of the script contents into the new format. To consume the scripts, adding a PsCall or ShCall operation for PowerShell or Shell scripts respectively to a deployment plan will execute the script at deployment time. If a script is only used by a single application, it is recommended to add it as a Script Asset to that specific application to reduce system-wide clutter and prevent it from appearing in the plan editor for other applications unnecessarily.

### Change Controls => Issues & Release Notes {#change-controls data-title="Change Controls"}

Change controls were essentially a checklist designed to keep track of "occurrences" performed in a specific environment per release. With the advent of pipelines, change controls became less effective since promotions occurred through stages instead of environments, and ultimately replaceable by any issue tracker (including BuildMaster's built-in one) since those already handle complete/incomplete status per release based on whether the issue is closed or not.

Current customers using change controls may switch to using [Issues or Notes](docs/buildmaster/modeling-applications/issues-notes) going forward. One of the main use cases for change controls was to mimic server provisioning (e.g. setting up services, app pools, etc.), it is also worth investigating [Otter](/otter) as a fully automated replacement.

### Day/Time-based Promotion Requirement => Deployment Windows

For deployments that must occur within a certain time window, Deployment Windows should be used in favor of the legacy day/time-based promotion requirement. Multiple deployment windows can be specified for a single pipeline stage in addition to an optional time zone for global deployments. Additionally, more context is shown for deployment windows when deployment is forbidden.

#### Migration Strategy

Conversion to Deployment Windows should be done manually, and should be a straightforward 1:1 matchup of properties.


### Configuration File Variables  {#config-file-variable-syntax data-title="Config File Variables"}

In previous versions, variable expressions inside of configuration files (e.g. `$Variiiable` or `${Wrong Nam}`) would be ignored if they didn't resolve to a variable; this replacement logic was unique to configuration files, and use unique (and subtly different code). To simplify the code and unify the behavior, the Execution Engine's text templating libraries are used to perform the replacements, which means simply ignoring variable expressions is not possible. 

Instead, variable expressions that can't be resolved are replaced with their name. This mostly works, but there are some cases where the name will not match the exact expression (e.g. `${aaa}` will be replaced with `$aaa`). As such, a warning is left in the execution logs to encourage you to change it.  

#### Migration Strategy

If you don't want a variable expression to be replaced at deploy time, then escape the `$` character with a grave apostrophe, as such: "$MyExpression" with "\`$MyExpression".
