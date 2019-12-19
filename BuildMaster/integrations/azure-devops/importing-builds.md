---
title: Importing Builds
subtitle: Importing Builds from Azure DevOps
sequence: 300
keywords: buildmaster, builds, azure-devops
show-headings-in-nav: true
---

[Azure DevOps](https://azure.microsoft.com/en-us/services/devops/) is a CI/CD service provided as part of Microsoft's Azure platform. 

[[ATTN: Lauren]]
- a few more sentences about Azure DevOps CI/CD (their service is Azure DevOps Pipelines: https://azure.microsoft.com/en-us/services/devops/pipelines/)

While BuildMaster can certainly perform [continuous integration and build automation](/docs/buildmaster/builds/continuous-integration), there are a lot of good reasons to continue to use Azure DevOps:

 - **Save time and resources on retraining** - if you're already familiar with Azure DevOps and the processes you've set up, you won't have to spend time learning something new
 - **Reuse complex automations** - processes like automated testing are complex to develop and are often fragile; moving these established, sensitive processes to a new system like BuildMaster will require some degree of effort that might be better spent elsewhere
 - **Keep your existing infrastructure** - you can use the Azure DevOps infrastructure that you've already built and is providing you with value
 
A large percentage of customers use BuildMaster alongside Azure DevOps in exactly this manner and for these exact reasons.

::: {.attention .best-practice}
**See it live!** See an example of Azure DevOps integration by creating a new application using the *Azure DevOps CI/CD* template.
:::

## Azure DevOps "Builds" vs. BuildMaster "Builds" {#build-comparison data-title="Azure DevOps vs. BuildMaster Builds"}

While both Azure DevOps and BuildMaster use the term "build," the concepts are a bit different. 

 - A "build" in Azure DevOps is effectively the result of a build pipeline running, and typically includes build artifacts as part of the attached output. Once a pipeline is configured, it almost always has a 1-1 relationship with a commit to an Azure Repo.

 - A [BuildMaster build](/docs/buildmaster/builds/overview) is a broader concept. It represents a new version of an application or component that may be released to production using a pipeline with various stages that represent your software delivery process. Builds have a lifecycle that is not only visible to business stakeholders, but can be approved at different stages in the process and are organized under [releases](/docs/buildmaster/releases/overview) and [applications](/docs/buildmaster/administration/applications) that testers, operations, and managers understand.

## Installing the Azure DevOps Extension {#extension data-title="Azure DevOps Extension"}

Integrating Azure DevOps with BuildMaster is easy: Simply navigate to the *Admin* > *Extensions* page in your instance of BuildMaster and click on the Azure DevOps extension to install it.

If your instance doesn't have internet access, you can [manually install the Azure DevOps extension](/docs/buildmaster/reference/extensions#manual-install) after downloading the [Azure DevOps Extension Package](https://proget.inedo.com/feeds/Extensions/inedox/AzureDevOps).

## Connecting to Azure DevOps Using Resource Credentials {#credentials data-title="Connecting to Azure DevOps"}

An Azure DevOps [Resource Credential](/docs/buildmaster/administration/resource-credentials) is used to connect BuildMaster to Azure DevOps at the instance level, but also supports connections on a "team project collection" level. These credentials are effectively a username and [personal access token](https://docs.microsoft.com/en-us/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate?view=azure-devops), so we recommend creating a specific account with the minimum amount of privileges required to interact with Azure DevOps, typically the permissions to the projects that will be integrated with BuildMaster.

To connect to a standalone instance of Azure DevOps (i.e. Azure DevOps Server), make sure the instance URL of the resource credentials is configured to the hostname for your installation, for example: `https://azure-server.apps.corp`

## Importing Azure DevOps Artifacts {#importing-builds data-title="Importing Builds"}

Azure DevOps can be configured to not only compile your application's source code and run automated tests against it, but to capture "artifacts" of that process. These artifacts typically include whatever files the compiler output, i.e., your compiled application. 

Importing an artifact ensures BuildMaster will be able to deploy it to future stages whether Azure DevOps is accessible or not, and you can capture these artifacts in BuildMaster using the `AzureDevOps::Import-Artifact` operation.

### Example: Using the `AzureDevOps::Import-Artifact` operation

Here's how to import the `profit-calc-web.zip` artifact from the latest build within the `ProfitCalc` project:

```
AzureDevOps::Import-Artifact
(
    Credentials: CorpAzure,
    Project: ProfitCalc,
    Artifact: profit-calc-web.zip
); 
```

Note that you can also [capture the actual build number](#capture) using the `AzureDevOpsBuildNumber` output variable, which will help create a visible association between the BuildMaster build and Azure DevOps. And like all operations in OtterScript, you specify variables for any of these parameters.

## Automatically Queuing Builds in Azure DevOps {#queuing-builds data-title="Queuing Builds"}

You can queue builds in Azure DevOps using the `AzureDevOps::Queue-Build` operation. There a few reasons you may want to do this:

{.docs}
 - Create different builds in Azure DevOps that are automatically triggered by check-in
   - A "Release Configuration" build that instructs the compiler to optimize for speed and does not include debugging symbols
   - A "Release Branch" build that uses a different source control branch to create the build
 - Inject contextual information from BuildMaster (release number, etc.) into the application code after it's checked-out but prior to being built by Azure DevOps; you can then display this information once the application is running in an environment
 - Link from Azure DevOps back to the application or build in BuildMaster, so you can have cross-links in both systems 
 
### Example: Using the `AzureDevOps::Import-Artifact` operation

Here's how to queue a build within the `ProfitCalc` project:

```
AzureDevOps::Queue-Build
(
    Credentials: CorpAzure,
    Project: ProfitCalc,
    WaitForCompletion: true
); 
```

## Branches in Azure DevOps {#branches data-title="Azure DevOps Build Branches"}

Azure DevOps has support for builds built from feature branches, and both the import and queue operations support using these branches.

For example, you can import artifacts from the last successful `calculation-fix` build on the `ProfitCalc` project by specifying the `Branch` parameter on the operation.

```
AzureDevOps::Import-Artifact
(
    Credentials: CorpAzure,
    Project: ProfitCalc,
    Branch: calculation-fix,
    Artifact: profit-calc-web.zip
); 
````

While leaving the `Branch` property blank will refer to the `<default>` branch for the project, you may also specify one to optionally filter branch names with BuildMaster variables, for example: `Branch: feature-$ReleaseNumber`.

## Pushing Artifacts from Azure DevOps to an Artifact Repository {#push data-title="Pushing Artifacts from Azure DevOps"}

To push artifacts from Azure DevOps to BuildMaster or an artifact repository like [ProGet](https://inedo.com/proget), refer to the [Trigger from External Systems](/docs/buildmaster/builds/external-systems/trigger-via-api) documentation to see the variety of ways this can be accomplished.

## Capturing Azure DevOps Build Numbers {#capturing data-title="Capturing Azure DevOps Build Numbers"}

Whether you [prompt for a build number](#prompting-for-build-numbers) or ask Azure DevOps for the `latest` build, BuildMaster lets you capture and link this back to the Azure DevOps build number.

Capturing this reference at build time is very important so you can see *exactly* what code was used to create the build artifacts:

{.docs}
 - **From a debugging perspective**, this saves you wasted time trying to figure out which code was deployed and saves frustration when the deployed application doesn't seem to match the code
 - **From an auditing perspective**, this lets you see exactly who made which changes to a deployed application and when those changes were made

BuildMaster gives you a lot of flexibility in capturing this information. Although you could always manually dig through the execution logs to find this information, a simpler approach is as follows:
 1. Specify an *output parameter* on the appropriate operation (such as [import artifact](#importing-builds) or [queue build](#queuing-builds)); the operation will them set the value of runtime variable to Azure DevOps's build number;
 2. Set a [build configuration variable](/docs/buildmaster/administration/configuration-variables) from the runtime variable; because runtime variables only exist during build time, capturing this as a build variable will create a permanent record on the build

### Example: Capturing a Queued Build Number 

This example uses both the `AzureDevOps::Queue-Build` operation and the `Set-BuildVariable` operation to queue a build in Azure DevOps and then set a configuration variable.

```
AzureDevOps::Queue-Build
(
    Credentials: CorpAzure,
    Project: ProfitCalc,
    AzureDevOpsBuildNumber => $AzureDevOpsBuildNumber
); 

Set-BuildVariable AzureDevOpsBuildNumber
(
    Value: $AzureDevOpsBuildNumber
);
```

### Display a Hyperlink to Azure DevOps

Once this variable is captured, a [variable value renderer](/docs/buildmaster/administration/value-renderers) can be used to link `$AzureDevOpsBuildNumber` back to Azure DevOps on the Build Overview page, for example:

```
<a class="ci-icon azure-devops" href="https://dev.azure.com/inedo/ProfitCalc/_build/results?buildId=1$AzureDevOpsBuildNumber">Build #$AzureDevOpsBuildNumber</a>
```

## Deploying Azure DevOps Artifacts {#deployment data-title="Deploying Azure DevOps Artifacts"}

Once an artifact is captured via the optional queuing followed by "import" operations in a build plan, future stages simply need to use the `Deploy-Artifact` operation to deploy to any number of servers or targets:

```
Deploy-Artifact profitcalc-web
(
    To: E:\Websites\ProfitCalc
);
```

Alternatively, you can download artifacts directly from Azure DevOps using the `AzureDevOps::Download-Artifact` operation:

```
AzureDevOps::Download-Artifact
(
    Credentials: CorpAzure,
    Project: ProfitCalc,
    Artifact: profit-calc-web.zip,
    TargetDirectory: E:\Websites\ProfitCalc
); 
 ````

## Prompt for Build Number at Build Time {#prompting-for-build-numbers data-title="Prompt for Build Numbers"}

With [release templates](/docs/buildmaster/releases/templates), you can build a deployment pipeline that prompts for build numbers and/or build configurations based on data directly from Azure DevOps. When creating the release template, the following list variable source options are available:

{.docs}
 - Azure DevOps Project Name
 - Azure DevOps Build Definition
 - Azure DevOps Build Number

## Automatically Importing into BuildMaster after CI Build {#auto-starting data-title="Automatically Importing into BuildMaster"} 

At the end of a build pipeline in Azure DevOps, you may wish to automatically import those artifacts into BuildMaster so you can smoothly move these builds into the pipeline for testing and release. This can be programmatically done using the [Release & Build Deployment API](/docs/buildmaster/reference/api/release-and-build). 

When you want to specify the build number to import from, simply specify that as a variable (e.g., `$AzureDevOpsBuildNumber`) when making the API request. That will automatically create a build-scoped variable, which can then be used in your OtterScript plans to import artifacts from that build.

**Example: Triggering an Importing Artifact Build Using PowerShell**
```
Invoke-WebRequest http://{buildmaster-server}/api/releases/builds/create `
  -ContentType "application/json" `
  -Method POST `
  -Body '{"applicationName": "hdars", "releaseNumber": "2.14.0", "$AzureDevOpsBuildNumber": "$(Build.BuildNumber)" }' `
-Headers @{"X-ApiKey" = "<api-key>"}

```
