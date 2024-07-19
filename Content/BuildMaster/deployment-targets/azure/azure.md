---
title: "Azure App Services"
order: 1
---

 Microsoft Azure is a cloud computing platform and service that provides a wide range of integrated tools and services for building, deploying, and managing applications and infrastructure. 

BuildMaster 2022.9 and later includes built-in operations for deploying your applications to an Azure Web App.  For other Azure operations, please see our [deployment using az.exe](/docs/buildmaster/deployment-targets/azure/az-exe) documentation.

## Prerequisites
BuildMaster's Azure Web App operations require the [Azure CLI (az.exe)](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli-windows?view=azure-cli-latest) to be installed on the server that is executing Azure deployment stage. Typically, this is the BuildMaster server itself.

## Connecting to Azure
To connect to Azure, you will need to use an [Azure Service Principal](https://learn.microsoft.com/en-us/azure/active-directory/develop/howto-create-service-principal-portal) on your Azure portal, and then an [Azure Service Principal resource in BuildMaster](/docs/buildmaster/deployment-targets/azure/buildmaster-azure-howto-connect). 

See [HOWTO: Connect BuildMaster to Azure](/docs/buildmaster/deployment-targets/azure/buildmaster-azure-howto-connect) for step-by-step guidance on how to do this.

## Deploying to Azure Web Apps
When deploying to an Azure Web, there are two different methods to deploying your application; slotted deploy and direct deploy.  

### Slotted Deployments
Slotted deployments provide several advantages, such as versioning, A/B testing, canary deployments, and rollback options. By deploying updates to a separate slot, organizations can conduct pre-production testing and validation before swapping the slot with the production environment, minimizing interruptions and errors during deployment.

::: (Info) (Note:)
Deployment slots are only supported in the Standard, Premium, and Isolated tiers.
:::

Although there are several methods for deploying to a web app, we recommend deploying applications using the following general pattern:
- Stop the web app slot (`Azure::Stop-AzureWebApp`)
- Deploy the build artifact (`Deploy-Artifact` then `Azure::Deploy-AzureWebApp`)
- Start the wep app slot (`Azure::Start-AzureWebApp`)
- Swap the slot to production (`Azure::Swap-AzureWebAppSlot`)

An example BuildMaster plan that deploys a website to an Azure web app using a slot is:
```
Azure::Stop-AzureWebApp
(
    Name: $ApplicationName,
    From: global::AzureCreds,
    ResourceGroupName: $ApplicationName-rg,
    Slot: Staging
);

Deploy-Artifact
(
    DeployAsZipFile: true,
    To: ~/
);

Azure::Deploy-AzureWebApp
(
    Name: $ApplicationName,From: global::AzureCreds,
    ResourceGroupName: $ApplicationName-rg,
    Slot: Staging,
    Source: ~/Default.zip,
    Type: auto
);

Azure::Start-AzureWebApp
(
    Name: $ApplicationName,
    From: global::AzureCreds,
    ResourceGroupName: $ApplicationName-rg,
    Slot: Staging
);

Azure::Swap-AzureWebAppSlot
(
    Name: $ApplicationName,
    From: global::AzureCreds,
    ResourceGroupName: $ApplicationName-rg,
    SourceSlot: Staging,
    TargetSlot: Production
);
```

### Direct Deployments
Direct deployments involve deploying your application directly to your live site. While slotted deployments offer numerous advantages, direct deployments can reduce complexity by eliminating the need to manage multiple slots. They also eliminate additional resource utilization for maintaining multiple instances, potential synchronization issues between slots, and the need for extensive monitoring and management to ensure optimal performance and cost efficiency.

Although there are several methods for deploying to a web app, we recommend deploying applications using the following general pattern:
- Stop the web app (`Azure::Stop-AzureWebApp`)
- Deploy the build artifact (`Deploy-Artifact` then `Azure::Deploy-AzureWebApp`)
- Start the wep app (`Azure::Start-AzureWebApp`)

An example BuildMaster plan that deploys a website to an Azure web app using a slot is:
```
Azure::Stop-AzureWebApp
(
    Name: $ApplicationName,
    From: global::AzureCreds,
    ResourceGroupName: $ApplicationName-rg
);

Deploy-Artifact
(
    DeployAsZipFile: true,
    To: ~/
);

Azure::Deploy-AzureWebApp
(
    Name: $ApplicationName,From: global::AzureCreds,
    ResourceGroupName: $ApplicationName-rg,
    Source: ~/Default.zip,
    Type: auto
);

Azure::Start-AzureWebApp
(
    Name: $ApplicationName,
    From: global::AzureCreds,
    ResourceGroupName: $ApplicationName-rg
);
```

::: (Internal) (Should we add this?)
## Azure Web App Operations

### Azure::Deploy-AzureWebApp
Deploys an artifact to an Azure web app or slot.


### Azure::Start-AzureWebApp

### Azure::Stop-AzureWebApp

### Azure::Swap-AzureWebAppSlot
:::