---
title: "Deploy Azure Web App Script Template"
order: 4
---

The Deploy Azure Web App Script Template is a simple, no-code solution for deploying your application to [Azure WebApps](https://azure.microsoft.com/en-us/products/app-service/web) (optionally, using Slotted Deployment), and can be converted to OtterScript at any time.

This script template will:
* [Stop Azure Web App](#stop-azure-web-app) to allow for the artifact to be deployed
* [Deploy Artifact](#deploying-artifact), which will synchronize the contents of the artifacts file and target directory by performing an incremental transfer
* [Deploy Azure Web App](#deploying-azure-web-app), which will deploy a specified web app, including the option to [deploy to a slot](/docs/buildmaster/deployment-targets/azure/azure#deploying-to-azure-web-apps)
* optionally [Deploy an application configuration file](#deploying-config-files), which can be a [built-in configuration file](/docs/buildmaster/deployment-continuous-delivery/buildmaster-applications-configuration-files)
* [Start Azure Web App](#start-azure-web-app) again.

This article will detail the options for these steps, as well as how they're performed.

## Using this Script Template

To add this script template to your application, navigate to Scripts, click "Add Script", and then select "Deploy Artifacts to Azure Web App" from the list of Deploy Script Templates. After doing so, you'll see a dialog like this:

![Manage Vulnerability Sources](/resources/docs/buildmaster-deploy-azurescript.png){height="" width="50%"}

## Working with Slotted Deployments (Staging Environments)

Azure's App Services use [staging slots](https://learn.microsoft.com/en-us/azure/app-service/deploy-staging-slots) to create separate instances of an application. Each slot serves as an isolated environment where changes can be deployed independently from the production environment. 

This script template will deploy to a specific deployment slot (such as `Staging`); once that deployment is complete, you can test as required, and then "swap" the slots using a separate script. This "swap script" requires just a single OtterScript operation.

```
Azure::Swap-AzureWebAppSlot
(
    From: global::MyAzurePrinciple,
    ResourceGroupName: my-az-resgroup-name,
    Name: my-az-app-name,
    SourceSlot: Staging,
    TargetSlot: Production
);
```
You can run this as a separate script, or convert the script template to OtterScript and add it to the end.

## Stop Azure Web App
First, the script template will stop the specified Azure web app and waits for the Azure web app to be stopped.

:::(Info) (🛠 OtterScript Notes)
The `Azure::Stop-AzureWebApp` operation will execute with the web app name as the default `Name` parameter, the Azure service credentials as the `From` parameter, and the resource group name as the `ResourceGroupName` parameter. If a slot name has been defined in the Deployment Options, the operation will run with the slot name as an additional `Slot` parameter. 
:::

## Deploying Artifact

The script template will deploy an artifact as a zip file to the working directory.  This is due to how `az.exe` deploys an application to an Azure Web App.  To learn more, see [Build Artifacts](/docs/buildmaster/builds-continuous-integration/buildmaster-artifacts) for more details.

### Additional Artifact Options
On the "Deploy Artifact Options" tab, there is a field you can set:
* **Artifact name** is the name of the artifacts file attached to your build; the default name for this file is *Default*, but you may have named it something different when creating it

:::(Info) (🛠 OtterScript Notes)
The `Deploy-Artifact` operation will be executed with a `DeployAsZipFile` parameter set as `true`, the working directory as the `To` parameter, and other parameters based on the options selected.
:::

## Deploying Azure Web App
The script template will deploy the specified Azure Web App.

### Deployment Options
On the "Deployment Options" tab, there are three fields you can set:
* **Type** allows you to specify the type of file you wish to deploy. The default is set to "Automatically Detect".
* **Additional Files** When deploying a web app to Azure, you have the option to remove additional files from your Azure Web App. 
  * Checked: Azure will delete all files prior to pushing your zipped artifact.
  * Unchecked: Azure will add or overwrite the files on your Azure Web App, but will not remove any extra files that are in your Web App.
* **Slot Name** for the name of your slot if you have a specific deployment slot already set up.

:::(Info) (🛠 OtterScript Notes)
The `Azure::Deploy-AzureWebApp` operation will be run with the following parameters:
- `Name` is the Web App Name
- `From` is the Azure service credentials
- `ResourceGroupNamethe` is the resource group name
- `Slot` is the slot name if one has been entered under "Deployment Options".
:::

## Deploying Config Files
If you selected an option for the "Configuration file" field, the script template will deploy a configuration file.

### Deploy BuildMaster Configuration File
If "Deploy BuildMaster Configuration File" is selected, you must have first created a [configuration file](/docs/buildmaster/deployment-continuous-delivery/buildmaster-applications-configuration-files) in your BuildMaster application. You may select this configuration file from the Config File Options tab to include it as part of your deployment.  

The script template will first deploy the configuration to the working directory and then deploy it as a static file to your Azure Web App at the specified target path.

:::(Info) (🛠 OtterScript Notes)
The `Deploy-ConfigFile` operation will be executed with the `ConfigFile` parameter set as the selected configuration file, the `Instance` parameter set as the selected instance, and the `To` parameter set to the working directory.  

Then the `Azure::Deploy-AzureWebApp` operation will be executed with the web app name as the default `Name` parameter, together with the following parameters:

- `From`: the Azure service credentials
- `ResourceGroupNamethe`: the resource group name
- `Type`: set to `static` or the file type entered under "Deployment Options".
- `Source`: your working directory plus config file name
- `Target` will be the specified target path combined with your config file name. 
- `Slot`: the slot name if one has been entered under "Deployment Options".
:::

## Start Azure Web App
Finally, the script template will start the specified Azure Web App.

:::(Info) (🛠 OtterScript Notes)
The `Azure::Start-AzureWebApp` operation will be executed with the web app name as the default `Name` parameter, the Azure service credentials as the `From` parameter, and the resource group name as the `ResourceGroupName` parameter. If a slot name has been defined in the Deployment Options, the operation will run with the slot name as an additional `Slot` parameter. 
:::
