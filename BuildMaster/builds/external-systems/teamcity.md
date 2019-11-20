---
title: TeamCity
subtitle: Integrating TeamCity with BuildMaster
sequence: 200
keywords: buildmaster, builds, teamcity
show-headings-in-nav: true
---

[TeamCity](https://www.jetbrains.com/teamcity) is a web-based continuous integration server by JetBrains. The software can be hosted by JetBrains in the cloud (known as InCloud), or installed and managed on-premise (Standalone). TeamCity offers all the typical functionality of a CI server:

{.docs}
 - intelligent CI builds
 - support for unit tests and static analysis
 - artifact storage

JetBrains estimates their current TeamCity users at over seven million, and for good reason: TeamCity is an extremely powerful CI server. But it isn’t an end-to-end CI/CD solution. BuildMaster is designed to continuously deliver your applications and components from source code to production, but unlike monolith CI/CD platforms like Azure DevOps or GitLab, you can work the tools and processes you’re already using, including a tight integration with TeamCity.

While BuildMaster can certainly perform [continuous integration and build automation](/docs/buildmaster/builds/continuous-integration), there are a lot of good reasons to continue to use TeamCity:

{.docs}
 - **Save time and resources on retraining** - if you're already familiar with TeamCity and the processes you've set up, you won't have to spend time learning something new
 - **Reuse complex automations** - processes like automated testing are complex to develop and are often fragile; moving these established, sensitive processes to a new system like BuildMaster will require some degree of effort that might be better spent elsewhere
 - **Keep your existing infrastructure** - you can use the TeamCity infrastructure that you've already built and is providing you with value
 
 A large percentage of customers use BuildMaster alongside TeamCity in exactly this manner and for these exact reasons.

::: {.attention .best-practice}
**See it live!** See an example of TeamCity integration by creating a new application using the *TeamCity Import* template.
:::

## TeamCity "Build Configurations" vs. BuildMaster "Builds" {#build-configurations-vs-builds data-title="TeamCity Build Configurations vs. BuildMaster Builds"}

While both TeamCity and BuildMaster use the term “build,” the concepts are a bit different. 
 -A “build configuration” in TeamCity defines which repository the collection of logs, test results, and files will be created from, how they’ll be created, who will be notified, etc. when a developer commits new code to a source control repository. These are intended for engineers and may not have any relation to a business application or component, and may not be understandable by testing or operations departments
 -A [BuildMaster build](/docs/buildmaster/builds/overview) is a broader concept. It represents a new version of an application or component that may be released to production using a pipeline with various stages that represent your software delivery process. Builds have a lifecycle that is not only visible to business stakeholders, but can be approved at different stages in the process and are organized under [releases](/docs/buildmaster/releases/overview) and [applications](/docs/buildmaster/administration/applications) that testers, operations, and managers understand.


## Installing the TeamCity Extension {#extension data-title="TeamCity Extension"}

Integrating TeamCity with BuildMaster is easy: Simply navigate to the Admin > Extensions page in your instance of BuildMaster and click on the TeamCity extension to install it.

If your instance doesn't have internet access, you can [manually install the TeamCity extension](/docs/buildmaster/reference/extensions#manual-install) after downloading the [TeamCity Extension Package](https://proget.inedo.com/feeds/Extensions/inedox/TeamCity).

## Connecting to TeamCity Using Resource Credentials {#connecting data-title="Connecting to your CI Server"}

[Resource Credentials](/docs/buildmaster/administration/resource-credentials) are used to connect BuildMaster to your Continuous Integration (CI) server, and each supported CI server has its own type of Resource Credential, which is defined in the corresponding [extension](#extension). You can create as many resource credentials as you need, at both the system- and application-level, which means you can connect to multiple CI servers and share those connections across applications, letting your teams continue working with whatever tool each prefers, including TeamCity.

Authentication with TeamCity uses the TeamCity-type Resource Credential. These credentials are effectively a username and password, so we recommend creating a specific account with the minimum amount of privileges required to interact with TeamCity, typically the permissions to the specific project or build configuration. Public servers (i.e., open-source) or internal servers that are firewalled off that don't require authentication can simply use guest authentication by leaving the username and password empty.

To connect to a standalone instance of TeamCity, make sure the API URL of the resource credentials is configured to the hostname for your installation, for example: `https://teamcity.jetbrains.com`

## Importing TeamCity Artifacts {#importing-builds data-title="Importing Builds"}

A continuous integration can be configured to not only compile your application's source code and run automated tests against it, but to capture "artifacts" of that process. These artifacts typically include whatever files the compiler output, i.e., your compiled application.

Different types of operations are used to get artifacts from different CI servers and projects in those systems. TeamCity's is TeamCity::Import-Artifact.

Once a build is [queued](#queue), importing an artifact ensures BuildMaster will be able to deploy it to future stages whether TeamCity is accessible or not. Use the `TeamCity::Import-Artifact` operation (and reference `$TeamCityBuildNumber` obtained from the `TeamCity::Queue-Build` operation, or reference a [special build name](#special-build-names)) to create a BuildMaster build artifact from an existing TeamCity artifact:

```
TeamCity::Import-Artifact
(
    Credentials: TeamCity,
    Project: $TeamCityProjectName,
    BuildConfiguration: $TeamCityBuildConfiguration,
    Artifact: $TeamCityArtifactName,
    TeamCityBuildNumber => $TeamCityBuildNumber
);
```

For organizations that have their CI process fully configured in TeamCity, leave the build number set to `lastSuccessful` and [capture](#capture) the actual build number using the `$TeamCityBuildNumber` output variable on `TeamCity::Import-Artifact` operation. This will create a visible association between the BuildMaster build and TeamCity build.

Both the import and queue operations support branches in TeamCity. This is generally only useful in conjunction with [special build names](#special-build-names) because specific build numbers are already unique per branch. While leaving the `Branch` property blank will refer to the `<default>` branch for the project, you may also specify one in the plan to limit the results of `lastSuccessful`, and optionally filter branch names with BuildMaster variables, for example: `Branch: feature-$ReleaseNumber`.

### How-To Summary: Use Import Artifact Operations {#import-summary data-title="Import Summary"}
 1. Specify a Resource Credential, which tells the operation which server to connect to.
 2. Specify a Job/Project Configuration, which tells the operation which set of builds to import from the CI server. 
 3. Specify Build Number.
 4. Specify Artifact Name(s), which are the files to download and associate with the BuildMaster build.

And like all operations in OtterScript, you specify variables for any of these parameters.

## Automatically Queuing Builds in TeamCity {#queuing-builds data-title="Queuing Builds"}

BuildMaster supports queuing builds in TeamCity, which can be useful as an automated notification of a release team's desire to create a build artifact (or artifacts). There a few reasons you may want to do this:
 - Create different builds then are automatically triggered by check-in
   - A “Release Configuration” build that instructs the compiler to optimize for speed and does not include debugging symbols
   - A “Release Branch” build that uses a different source control branch to create the build
 - Inject contextual information from BuildMaster (release number, etc.) into the application code after it’s checked-out but prior to being built by TeamCity; you can then display this information once the application is running in an environment
 - Link from TeamCity back to the application or build in BuildMaster, so you can have cross-links in both system 
 
Fortunately, this is all easy with [OtterScript](/docs/otter/reference/otter-script). 

### How-To Summary: Use the Queue Operation {#queuing-summary data-title="Queuing Summary"}

 1. Specify a Resource Credential, which tells the operation which server to connect to.
 2. Specify a Job/Project Configuration, which tells the operation which job or configuration to create a build from on TeamCity.
 3. Specify Arguments that will be used by the underlying configuration (typically user-defined keys or variables that will, in turn, be passed to the tools TeamCity uses, such as MSBuild or Maven)
 4. Specify Whether to Wait, which determines whether BuildMaster will wait for the build to complete or just queue it and continue executing your OtterScript (generally speaking, you should wait for builds so that it's clear in the execution logs whether it was successful or not).

## Special Build Names {#special-build-names data-title="Special Build Names"}

BuildMaster can reference TeamCity builds using the following special build names instead of direct build numbers:

{.docs}
 - `lastSuccessful` - refers to the last build that completed successfully
 - `lastPinned` - refers to the last build that has been pinned
 - `lastFinished` - refers to the latest build whether it was successful or not (should rarely be referenced in BuildMaster)

Additionally, builds may be referenced by their underlying TeamCity ID as opposed to name/number. To specify a build ID instead, append `:id` as a suffix to the identifier, for example: `1234:id`

## Pushing Artifacts from TeamCity to an Artifact Repository {#push data-title="Pushing Artifacts from TeamCity"}

To push artifacts from TeamCity to BuildMaster or an artifact repository like [ProGet](https://inedo.com/proget), refer to the [Trigger from External Systems](/docs/buildmaster/builds/external-systems/trigger-via-api) documentation to see the variety of ways this can be accomplished.

## Capturing TeamCity Build Numbers

Whether you [prompt for a build number] or ask TeamCity for the lastSucessfulBuild, BuildMaster lets you capture and link this back to the TeamCity build number.
Capturing this reference at build time is very important so you can see *exactly* what code was used to create the build artifacts:
 - **From a debugging perspective**, this saves you wasted time trying to figure out which code was deployed and saves frustration when the deployed application doesn’t seem to match the code
 - **From an auditing perspective**, this lets you see exactly who made which changes to a deployed application and when those changes were made

BuildMaster gives you a lot of flexibility in capturing this information. Although you could always manually dig through the execution logs to find this information, a simpler approach is as follows:
 1. Specify an *output parameter* on the appropriate operation (such as [import artifact] or [queue build]); the operation will them set the value of runtime variable to be the CI server’s build number;
 2. Set a build variable from the runtime variable; because runtime variables only exist during build time, capturing this as a build variable will create a permanent record on the build

Use the `TeamCity::Queue-Build` operation in a build plan. Once the build is created (and optionally waited on), the build number of the TeamCity build can be captured as an output variable using the `TeamCityBuildNumber => $buildNumber` property:

```
TeamCity::Queue-Build
(
	Credentials: KramericaTeamCity,
	Project: ProfitCalc,
	BuildConfiguration: v8builds,
	Artifact: profit-calc-web.zip,
	TeamCityBuildNumber => $tcBuildNumber
); 
```

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
Using distinct build numbers for each build will help keep documentation clear, avoid silos and confusion, and will make tracking a specific build much simpler. Assigning a distinct build number at the time of the build, therefore, distinguishes the build from the start.

When you create a new build from the web UI, a dropdown box appears. From here, you can select the build numbers that were generated by TeamCity.

Variable prompts are configured in an application's Release Templates. If you're not familiar with the feature, that's okay-- all applications have a "Default" release template that's used behind-the-scenes to determine which pipeline to use, and which variables to prompt for at release, build, and deploy time.

After you configure a branch name variable prompt (e.g. $TeamCityBuildNumber), BuildMaster will create a build-scoped variable based on whatever the user selects. You can use that variable in your OtterScript plans to import artifacts from that build.

All BuildMaster applications have a “Default” release template that’s used behind-the-scenes to determine which pipeline to use, and which variables to prompt for at release, build, and deploy time.

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
