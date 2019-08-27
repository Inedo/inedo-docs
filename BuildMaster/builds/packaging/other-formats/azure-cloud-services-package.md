---
title: Azure Cloud Services Package
subtitle: Create a Azure Cloud Services package for deployment
keywords: buildmaster, azure, cloud, services
sequence: 100
pre-requisites: Buildmaster, InedoCore, Azure
---

BuildMaster can be used to build, test, and deploy any type of application, that includes [Azure Cloud Services](https://docs.microsoft.com/en-us/azure/cloud-services/cloud-services-choose-me) 


To deploy an Azure Cloud Services project, you essentially need two files:

 * a Cloud Service package file `.cspkg` that contains your compiled code that will run as a service.
 * a Cloud Service config file `.cscfg` contains XML that determines configuration details for your Cloud Service.

These 2 files are created when you build a Cloud Services Project and are used by Azure to provision and configure your Cloud Service instance in Azure. Upon a successful build these 2 files can be found in this path: `<ProjectPath>\bin\<Configuration>\app.publish` which should be stored as a variable in your build plan to be used in your `Publish-AzureServiceProject` command. 

```
set $projectPath = "C:\SourceCode\CloudServiceApp\";
set $csOutputFilePath = "$projectPath\bin\<Configuration>\app.publish";
```



In order to create the 2 Cloud Service Package files from BuildMaster you can use the `MSBuild::Build-Project` operation. Simply add this operation into your plan and complete the form with the values that relate to your Cloud Service project.  

Sample Plan:
```
MSBuild::Build-Project <pathToProject>\<web-project>.csproj
(
    Configuration: Debug,
    Platform: AnyCPU,
    Arguments: /t:Publish /p:TargetProfile=Local /p:AutomatedBuild=True
    To: <pathToProject>\<web-project>
);
```

This plan effectively runs this "msbuild.exe" command
```
msbuild <web_app.csproj> /t:Publish /p:TargetProfile=Local /p:AutomatedBuild=True
```


#### Next Steps
Now that you have created the `.cspkg` and `.cscfg` files you can use them to create your Cloud Service in Azure. From within a Powershell operation, the Azure PowerShell command [`Publish-AzureServiceProject`](https://docs.microsoft.com/en-us/powershell/module/servicemanagement/azure/publish-azureserviceproject?view=azuresmps-4.0.0) is used to build the `.cspkg` and upload it. This can be added to your deploy plan by using the PSExec operation. 


Sample Plan:
```
Execute-PowerShell Publish-AzureServiceProject -package $csOutputFilePath\AzureCloudService1.cspkg -config $csOutputFilePath\ServiceConfiguration.Local.cscfg;
```
