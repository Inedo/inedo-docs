---
title: TeamCity
subtitle: Integrating TeamCity with BuildMaster
sequence: 200
keywords: buildmaster, builds, teamcity
show-headings-in-nav: true
---

[TeamCity](https://www.jetbrains.com/teamcity) is a web-based continuous integration server by JetBrains. The software can be hosted by JetBrains in the cloud (known as InCloud), or installed and managed on-premise (Standalone). TeamCity offers all the typical functionality of a CI server:

 - intelligent CI builds
 - support for unit tests and static analysis
 - artifact storage

JetBrains estimates their current TeamCity users at over seven million, and for good reason: TeamCity is an extremely powerful CI server. But it isn't an end-to-end CI/CD solution. BuildMaster is designed to continuously deliver your applications and components from source code to production, but unlike monolith CI/CD platforms like Azure DevOps or GitLab, you can work the tools and processes you're already using, including a tight integration with TeamCity.

While BuildMaster can certainly perform [continuous integration and build automation](/docs/buildmaster/builds/continuous-integration), there are a lot of good reasons to continue to use TeamCity:

 - **Save time and resources on retraining** - if you're already familiar with TeamCity and the processes you've set up, you won't have to spend time learning something new
 - **Reuse complex automations** - processes like automated testing are complex to develop and are often fragile; moving these established, sensitive processes to a new system like BuildMaster will require some degree of effort that might be better spent elsewhere
 - **Keep your existing infrastructure** - you can use the TeamCity infrastructure that you've already built and is providing you with value
 
A large percentage of customers use BuildMaster alongside TeamCity in exactly this manner and for these exact reasons.

::: {.attention .best-practice}
**See it live!** See an example of TeamCity integration by creating a new application using the *TeamCity Import* template.
:::

## TeamCity "Build Configurations" vs. BuildMaster "Builds" {#build-configurations-vs-builds data-title="TeamCity Build Configurations vs. BuildMaster Builds"}

While both TeamCity and BuildMaster use the term "build," the concepts are a bit different. 

 - A "build configuration" in TeamCity defines which repository the collection of logs, test results, and files will be created from, how they'll be created, who will be notified, etc. when a developer commits new code to a source control repository. These are intended for engineers and may not have any relation to a business application or component, and may not be understandable by testing or operations departments

 - A [BuildMaster build](/docs/buildmaster/builds/overview) is a broader concept. It represents a new version of an application or component that may be released to production using a pipeline with various stages that represent your software delivery process. Builds have a lifecycle that is not only visible to business stakeholders, but can be approved at different stages in the process and are organized under [releases](/docs/buildmaster/releases/overview) and [applications](/docs/buildmaster/administration/applications) that testers, operations, and managers understand.

## Installing the TeamCity Extension {#extension data-title="TeamCity Extension"}

Integrating TeamCity with BuildMaster is easy: Simply navigate to the Admin > Extensions page in your instance of BuildMaster and click on the TeamCity extension to install it.

If your instance doesn't have internet access, you can [manually install the TeamCity extension](/docs/buildmaster/reference/extensions#manual-install) after downloading the [TeamCity Extension Package](https://proget.inedo.com/feeds/Extensions/inedox/TeamCity).

## Connecting to TeamCity Using Resource Credentials {#connecting data-title="Connecting to your CI Server"}

A TeamCity [Resource Credential](/docs/buildmaster/administration/resource-credentials) is used to connect BuildMaster to your TeamCity server. You can create as many resource credentials as you need, at both the system- and application-level, which means you can connect to multiple TeamCity servers and share those connections across applications, letting your teams continue working with their various instances of TeamCity.

These credentials are effectively a username and password, so we recommend creating a specific account with the minimum amount of privileges required to interact with TeamCity, typically the permissions to the specific project or build configuration. Public servers (i.e., open-source) or internal servers that are firewalled off that don't require authentication can simply use guest authentication by leaving the username and password empty.

To connect to a standalone instance of TeamCity, make sure the API URL of the resource credentials is configured to the hostname for your installation, for example: `https://teamcity.jetbrains.com`

## Importing TeamCity Artifacts {#importing-builds data-title="Importing Builds"}

TeamCity can be configured to not only compile your application's source code and run automated tests against it, but to capture "artifacts" of that process. These artifacts typically include whatever files the compiler output, i.e., your compiled application. 

Importing an artifact ensures BuildMaster will be able to deploy it to future stages whether TeamCity is accessible or not, and you can capture these artifacts in BuildMaster using the `TeamCity::Import-Artifact` operation.

### Example: Using the TeamCity::Import-Artifact operation

Here's how to import the `profit-calc-web.zip` artifact from the last successful build within the `ProfitCalc` project (using the `v8builds` build configuration):

```
 TeamCity::Import-Artifact
 (
     Credentials: KramericaTeamCity,
     Project: ProfitCalc,
     BuildConfiguration: v8builds,
     BuildNumber: lastSuccessful,
     Artifact: profit-calc-web.zip
 ); 
```

Note that you can also [capture the actual build number](#capture) using the `TeamCityBuildNumber` output variable, which will help create a visible association between the BuildMaster build and TeamCity. And like all operations in OtterScript, you specify variables for any of these parameters.

### How-To Summary: Use Import Artifact Operations

 1. Specify a Resource Credential, which tells the operation which TeamCity server to connect to.
 2. Specify a Project and Bulid Configuration, which tells the operation where to look for a build.
 3. Specify a Build Number, or reference a [special build name](#special-build-names)), which tells the operation which build to import artifacts from.
 4. Specify Artifact Name(s), which are the files to download and associate with the BuildMaster build.

## Automatically Queuing Builds in TeamCity {#queuing-builds data-title="Queuing Builds"}

You can queue builds in TeamCity using the `TeamCity::Queue-Build` operation. There a few reasons you may want to do this:

 - Create different builds in TeamCity that are automatically triggered by check-in
   - A "Release Configuration" build that instructs the compiler to optimize for speed and does not include debugging symbols
   - A "Release Branch" build that uses a different source control branch to create the build
 - Inject contextual information from BuildMaster (release number, etc.) into the application code after it's checked-out but prior to being built by TeamCity; you can then display this information once the application is running in an environment
 - Link from TeamCity back to the application or build in BuildMaster, so you can have cross-links in both system 
 

### Example: Using the TeamCity::Import-Artifact operation

Here's how to queue a build within the `ProfitCalc` project (using the `v8builds` build configuration):

```
TeamCity::Queue-Build
(
    Credentials: KramericaTeamCity,
    Project: ProfitCalc,
    BuildConfiguration: v8builds,
    AdditionalParameters: &buildType=release,
    WaitForCompletion: true
); 
```

This example will also pass the `buildType=release` as an additional paramter, and wait for the build to be completed. 

### How-To Summary: Use the Queue Operation

 1. Specify a Resource Credential, which tells the operation which server to connect to.
 2. Specify a Project and Bulid Configuration, which tells the operation where to look for a build.
 3. Specify Additional Parameters that will be used by TeamCity in query string format; these are often be passed to the tools TeamCity uses, such as MSBuild or Maven
 4. Specify Whether to Wait, which determines whether BuildMaster will wait for the build to complete or just queue it and continue executing your OtterScript (generally speaking, you should wait for builds so that it's clear in the execution logs whether it was successful or not).
 
## Special Build Names {#special-build-names data-title="Special Build Names"}

BuildMaster can reference TeamCity builds using the following special build names instead of direct build numbers:

{.docs}
 - `lastSuccessful` - refers to the last build that completed successfully
 - `lastPinned` - refers to the last build that has been pinned
 - `lastFinished` - refers to the latest build whether it was successful or not (should rarely be referenced in BuildMaster)

Additionally, builds may be referenced by their underlying TeamCity ID as opposed to name/number by appending `:id` as the build number suffix. For example:

```
 TeamCity::Import-Artifact
 (
     Credentials: KramericaTeamCity,
     Project: ProfitCalc,
     BuildConfiguration: v8builds,
     BuildNumber: 1234:id,
     Artifact: profit-calc-web.zip
 ); 
 ````

Generally speaking, you should avoid using build number or TeamCity IDs directly in OtterScript like the above example. Instead, you should [capture the actual build number](#capture) as a variable, and use that instead.

## Branches in TeamCity {#branches data-title="TeamCity Branches"}

TeamCity has support for builds built from feature branches, and both the import and queue operations support using these branches.

For example, you can import artifacts from the last successful `calculation-fix` build on the `ProfitCalc` project by specifying the `Branch` parameter on the operation.

```
 TeamCity::Import-Artifact
 (
     Credentials: KramericaTeamCity,
     Project: ProfitCalc,
     BuildConfiguration: v8builds,
     Branch: calculation-fix,
     Artifact: profit-calc-web.zip
 ); 
 ````

The Branch parameter is generally only useful in conjunction with [special build names](#special-build-names) because specific build numbers are already unique per branch. 

While leaving the `Branch` property blank will refer to the `<default>` branch for the project, you may also specify one in the plan to limit the results of `lastSuccessful`, and optionally filter branch names with BuildMaster variables, for example: `Branch: feature-$ReleaseNumber`.

## Pushing Artifacts from TeamCity to an Artifact Repository {#push data-title="Pushing Artifacts from TeamCity"}

To push artifacts from TeamCity to BuildMaster or an artifact repository like [ProGet](https://inedo.com/proget), refer to the [Trigger from External Systems](/docs/buildmaster/builds/external-systems/trigger-via-api) documentation to see the variety of ways this can be accomplished.

## Capturing TeamCity Build Numbers {#capturing data-title="Capturing TeamCity Build Numbers"}

Whether you [prompt for a build number](#prompting-for-build-numbers) or ask TeamCity for the `lastSucessfulBuild`, BuildMaster lets you capture and link this back to the TeamCity build number.

Capturing this reference at build time is very important so you can see *exactly* what code was used to create the build artifacts:
 - **From a debugging perspective**, this saves you wasted time trying to figure out which code was deployed and saves frustration when the deployed application doesn't seem to match the code
 - **From an auditing perspective**, this lets you see exactly who made which changes to a deployed application and when those changes were made

BuildMaster gives you a lot of flexibility in capturing this information. Although you could always manually dig through the execution logs to find this information, a simpler approach is as follows:
 1. Specify an *output parameter* on the appropriate operation (such as [import artifact](#importing-builds) or [queue build](#queuing-builds)); the operation will them set the value of runtime variable to TeamCity's build number;
 2. Set a [build configuration variable](/docs/buildmaster/administration/configuration-variables) from the runtime variable; because runtime variables only exist during build time, capturing this as a build variable will create a permanent record on the build

### Example: Capturing a Queued Build Number 

This example uses both the `TeamCity::Queue-Build` operation and the `Set-BuildVariable` operation to queue a build in TeamCity and then set a configuration variable.

```
TeamCity::Queue-Build
(
    Credentials: KramericaTeamCity,
    Project: ProfitCalc,
    BuildConfiguration: v8builds,
    Artifact: profit-calc-web.zip,
    TeamCityBuildNumber => $tcBuildNumber
); 

Set-BuildVariable TeamCityBuildNumber
(
    Value: $tcBuildNumber
);
```

### Display a Hyperlink to TeamCity

Once this variable is captured, a [variable value renderer](/docs/buildmaster/administration/value-renderers) can be used to link `$TeamCityBuildNumber` back to TeamCity on the Build Overview page, for example:

```
<a class="ci-icon teamcity" href="https://teamcity.jetbrains.com/viewLog.html?buildNumber=$TeamCityBuildNumber&buildTypeId=acejump_buildplugin">Build #$TeamCityBuildNumber</a>
```

## Deploying TeamCity Artifacts {#deployment data-title="Deploying TeamCity Artifacts"}

Once an artifact is captured via the optional queuing followed by "import" operations in a build plan, future stages simply need to use the `Deploy-Artifact` operation to deploy to any number of servers or targets:

```
Deploy-Artifact profitcalc-web
(
    To: E:\Websites\ProfitCalc
);
```

## Prompt for Build Number at Build Time {#prompting-for-build-numbers data-title="Prompt for Build Numbers"}

With [release templates](/docs/buildmaster/releases/templates), you can build a deployment pipeline that prompts for build numbers and/or build configurations based on data directly from TeamCity. When creating the release template, the following list variable source options are available:

{.docs}
 - TeamCity Project Name
 - TeamCity Build Configuration
 - TeamCity Build Number

Refer to the [Choosing a specific TeamCity Build with BuildMaster](https://inedo.com/support/tutorials/buildmaster/teamcity/choosing-specific-artifact-from-teamcity) tutorial for more information.

## Automatically Importing into BuildMaster after CI Build {#auto-starting data-title="Automatically Importing into BuildMaster"} 

At the end of a build process in TeamCity, you may wish to automatically import those artifacts into BuildMaster so you can smoothly move these builds into the pipeline for testing and release. This can be programmatically done using the [Release & Build Deployment API](/docs/buildmaster/reference/api/release-and-build). 

When you want to specify the build number to import from, simply specify that as a variable (e.g., `$TeamCityBuildNumber`) when making the API request. That will automatically create a build-scoped variable, which can then be used in your OtterScript plans to import artifacts from that build.

**Example: Triggering an Importing Artifact Build Using PowerShell**
```
Invoke-WebRequest http://{buildmaster-server}/api/releases/builds/create `
  -ContentType "application/json" `
  -Method POST `
  -Body '{"applicationName": "hdars", "releaseNumber": "2.14.0", "$JenkinsBuildNumber": "88512" }' `
-Headers @{"X-ApiKey" = "<api-key>"}

```
