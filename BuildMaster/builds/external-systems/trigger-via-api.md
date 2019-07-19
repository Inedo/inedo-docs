---
title: External Build Triggering
subtitle: Triggering a Build via the API
url: builds/external-systems/trigger-via-api
keywords: buildmaster, builds, proget, teamcity, jenkins
sequence: 700
---

BuildMaster manages the CI/CD process for applications of all types and is designed to integrate with any existing process. Sometimes, this process includes initiating a build through a deployment pipeline from an existing CI tool (e.g. TeamCity, Jenkins) or package manager such as [ProGet](/proget).

Builds may be created in BuildMaster by sending an HTTP request to the [Release & Build Deployment API](/support/documentation/buildmaster/reference/api/release-and-build-deployment). Common use-cases for this include:

{.docs}
 - automatically triggering a BuildMaster build or deployment from an external tool
 - wiring up a custom webhook defined in Slack, Teams, etc.
 - personal preference using a command line or PowerShell interface instead of the web UI
 - notifying BuildMaster that a build may be created from a [drop path](drop-path)
 
## Example: Deploying a Universal Package from ProGet

The Universal Package format ([UPack](https://inedo.com/upack)) is very simple, and can be used to package applications and components built with any technology: .NET web applications, NodeJS applications, Windows services, plug-ins for your applications, system configuration scripts, and so on. 

This example allows anyone (or any external tool) with access to ProGet to push a package, including developers and vendors, and configuring the Universal Feed to automatically trigger a build in BuildMaster.

### 1. Enable the API:

In order to create a build via the API, it must be enabled. To do so, visit the `Administration` > `API Keys & Access Logs` page to add an API key, making sure to check the "Grant access to Release & Build Deployment API" option.

### 2. Call the API: 

Because the API is called via HTTP, it can be accessed in a variety of ways. For this example, we will use a [ProGet "package-added" webhook](/support/documentation/proget/advanced/webhooks) to initiate a build in BuildMaster. In ProGet, the webhook is configured as follows:

| Setting | Value |
|---|---|
| Feed | `VendorPackages` |
| Event | `added` |
| URL | `http://{buildmaster-server}/api/releases/builds/create` |
| Method | `POST` |
| Condition | *`always runs`* |
| Headers | `Content-Type=application/json`<br/>`X-ApiKey=<api-key>` |
|Content | `$ToJson(%(`<br/>`  applicationName: $PackageName,`<br/>`  releaseNumber: $PackageVersion,`<br/>`  reason: $PackageName pushed to ProGet`<br/>`))` |

{.attention .best-practice} Using `$PackageName` in this example requires that an application in BuildMaster with that name exists, otherwise an error will be generated. Alternatively, this webhook to hit a "proxy" application in BuildMaster, whose first plan in the pipeline is configured to ensure an application and release exists and trigger it.

When this webhook is configured, any time a package is added (e.g. uploaded, pulled from a remote connector), the associated application that matches the package name will have a new build created for the release that matches the package's version.

## Example: Kicking off a Build with a PowerShell Script:

PowerShell is the Windows solution for scripting. It can be run from any desktop or server (including Jenkins or TeamCity). The webhook from the previous example is equivalent to the following one-line PowerShell command after `$PackageVersion` and `$PackageName` have been resolved:

```
Invoke-WebRequest http://{buildmaster-server}/api/releases/builds/create `
  -ContentType "application/json" `
  -Method POST `
  -Body (ConvertTo-Json @{applicationName = "TriggerExample"; releaseNumber = "2.14.0"; reason = "NuGet.Core pushed to ProGet"} ) `
  -Headers @{"X-ApiKey" = "<api-key>"}
```

*Note: If using Integrated Windows Authentication in BuildMaster, an additional argument for `-Credentials` may be required.*