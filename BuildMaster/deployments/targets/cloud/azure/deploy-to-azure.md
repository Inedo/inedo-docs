---
title: Azure Deployment
subtitle: Deployment using az.exe
keywords: azure, az, cli, buildmaster, cloud
sequence: 100
---


When using BuildMaster to deploy applications you may first need to create the target to which you are deploying. This is a typical solution when you want to deploy to cloud services such as Microsoft Azure. 


#### Before we begin
To deploy azure resources within buildmaster you will first need to 
[Install Azure CLI](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli-windows?view=azure-cli-latest) on the machine where buildmaster is installed. Once installed you can begin to execute azure cli commands against your azure subscription after you execute the login command. We recommend [logging in to azure using a service principal](https://docs.microsoft.com/en-us/cli/azure/authenticate-azure-cli?view=azure-cli-latest#sign-in-with-a-service-principal) instead of a username/password.  You will need to create a [service principal](https://docs.microsoft.com/en-us/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest) with az CLI before.


#### Implementation

As mentioned before, in order to execute azure command line functions you will need to login to azure to associate the action you are performing to an azure subscription. We recommend that you combine the azure login **and** the azure command into one [OtterScript](https://inedo.com/docs/otter/reference/otter-script) module such as this one. 


##### Azure-Execute module:
```
module Azure-Execute<$args>
{

    set $ExeFileName = "C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\wbin\az.cmd";

    InedoCore::Exec
    (
        FileName: $ExeFileName,
        Arguments: login --service-principal -u <url> -p <password> --tenant <tenant>
    );

    InedoCore::Exec
    (
        FileName: ExeFileName,
        Arguments: $args
    );
}
```

Once created as a global module, `Azure-Execute` can now be used easily from any OtterScript plan in BuildMaster to start [creating](https://docs.microsoft.com/en-us/azure-stack/user/azure-stack-quick-create-vm-windows-cli) resources such as [databases](https://docs.microsoft.com/en-us/cli/azure/sql/db?view=azure-cli-latest#az-sql-db-create), [virtual machines](https://docs.microsoft.com/en-us/azure-stack/user/azure-stack-quick-create-vm-windows-cli), and more. 


###### Create a Resource Group named after your BuildMaster application
```
call Azure-Execute
(
    args: group create --name $ApplicationName-rg --location centralus
);
```

This essentially run these 2 commands which will create a resource group in azure.
```
az login --service-principal -u <url> -p <password> --tenant <tenant>
az group create --name $ApplicationName-rg --location centralus
```

#### More Commands

###### Create a SQL Server on Azure in the Resource Group you just created.
```
call Azure-Execute
(
    args: sql server create --admin-password mysecret --admin-user $ApplicationName-admin  --name $ApplicationName-server --resource-group $ApplicationName-rg --location centralus 
);
```

###### Create a Standard S0 database named after your BuildMaster pipeline.
``` 
InedoCore::Exec
(
    FileName: "C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\wbin\az.cmd",
    Arguments: sql db create -g $ApplicationName-rg -s $ApplicationName-server -n $PipelineName-db --service-objective S0
);
```



