---
title: "HOWTO: Triggering a Build via the API"
order: 7
---


BuildMaster manages the CI/CD process for applications of all types and is designed to integrate with any existing process. Sometimes, this process involves initiating a build through a deployment pipeline from an existing CI tool (e.g., TeamCity, Jenkins) or package manager such as [ProGet](/proget).

Builds may be created in BuildMaster by sending an HTTP request to the [Release & Build Deployment API](/docs/buildmaster/reference/api/release-and-build). Common use-cases for this include:

 - automatically triggering a BuildMaster build or deployment from an external tool
 - wiring up a custom webhook defined in Slack, Teams, etc.
 - personal preference for using a command line or PowerShell interface instead of the web UI
 - notification from BuildMaster that a build can be created from a  [drop path](/docs/buildmaster/builds-continuous-integration/buildmaster-artifacts/buildmaster-drop-folders)
 
## Example: Deploying a Universal Package from ProGet

The Universal Package Format ([UPack](https://inedo.com/upack)) is very simple and can be used to package applications and components built with any technology: .NET Web applications, NodeJS applications, Windows services, plug-ins for your applications, scripts for system configuration and so on.

In this example, anyone (or any external tool) with access to ProGet can push a package, including developers and vendors, and configure the Universal Feed to automatically trigger a build in BuildMaster.

### 1. Enable the API:

In order to create a build via the API, it must be enabled. To do so, visit `Administration` > `API Keys & Access Logs` to add an API key, making sure to check the "Grant access to Release & Build Deployment API" option.

### 2. Call the API: 

Because the API is called via HTTP, it can be accessed many ways. For this example, we will use a [ProGet "package-added" webhook](/docs/proget/administration/proget-notifications-webhooks) to initiate a build in BuildMaster. In ProGet, the webhook is configured as follows:

| Setting | Value |
|---|---|
| Feed | `VendorPackages` |
| Event | `added` |
| URL | `http://{buildmaster-server}/api/releases/builds/create` |
| Method | `POST` |
| Condition | *`always runs`* |
| Headers | `Content-Type=application/json`<br/>`X-ApiKey=<api-key>` |
|Content | `$ToJson(%(`<br/>`  applicationName: $PackageName,`<br/>`  releaseNumber: $PackageVersion,`<br/>`  reason: $PackageName pushed to ProGet`<br/>`))` |

::: (INFO)
Using `$PackageName` in this example assumes that an application exists in BuildMaster with that name, otherwise an error is generated. Alternatively, this webhook can hit a "proxy" application in BuildMaster whose first plan in the pipeline is configured to ensure that an application and a share exist and trigger it.
:::

When this webhook is configured, each time a package is added (e.g., uploaded, pulled from a remote connector), a new build is created for the associated application that matches the package name for the release that matches the package version.

## Example: Kicking off a Build with a PowerShell Script:

PowerShell is the Windows solution for scripting. It can be run from any desktop or server (including Jenkins or TeamCity). The webhook from the previous example corresponds to the following one-line PowerShell command after resolving `$PackageVersion` and `$PackageName`:

```
Invoke-WebRequest http://{buildmaster-server}/api/releases/builds/create `
  -ContentType "application/json" `
  -Method POST `
  -Body (ConvertTo-Json @{applicationName = "TriggerExample"; releaseNumber = "2.14.0"; reason = "NuGet.Core pushed to ProGet"} ) `
  -Headers @{"X-ApiKey" = "<api-key>"}
```

*Note: If using Integrated Windows Authentication in BuildMaster, an additional argument for `-Credentials` may be required.*