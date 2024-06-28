---
title: "Deployment using az.exe"
order: 2
---

When using BuildMaster to deploy applications, you may first need to create the target you want to deploy to. This is a typical solution if you want to access cloud services such as Microsoft Azure.

:::(Info) (NOTE:)
In BuildMaster 2022.9+, we have added new Azure operations and a script template.  See our [Azure App Services](/docs/buildmaster/deployment-targets/azure/azure) documentation.
:::

## Before We Begin
To deploy Azure resources within BuildMaster, you will first need to 
[Install Azure CLI](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli-windows?view=azure-cli-latest) on the machine where BuildMaster is installed. You will need to [create a service principal with the Azure CLI](https://docs.microsoft.com/en-us/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest) or [with the Azure Portal](https://learn.microsoft.com/en-us/azure/active-directory/develop/howto-create-service-principal-portal) before starting. Once installed, you can execute Azure CLI commands against your Azure subscription after you execute the [login command using a service principal](https://docs.microsoft.com/en-us/cli/azure/authenticate-azure-cli?view=azure-cli-latest#sign-in-with-a-service-principal).

### Implementation

As mentioned before, in order to execute Azure command line functions you will need to login to Azure to associate the action you are performing with an Azure subscription. We recommend that you combine the Azure login **and** the Azure command into one [OtterScript](/docs/otter/reference/otter-script) module such as this one. 

#### Create an Azure Service Principal service account
Begining in BuildMaster 2022.9, we have added a new **Azure Service Principal** service account type.  We recommend creating a new Azure Service Principal with "Allow encrypted properties..." checked to securely store your Azure service principal and leveraging that in your Module.

#### Azure-Execute Module (BuildMaster 2022.9+):
```
module Azure-Execute<$args>
{
    set $ExeFileName = "C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\wbin\az.cmd";
    set $applicationId = $SecureCredentialProperty(AzureServicePrincipalName, ApplicationId);
    set $tenantId = $SecureCredentialProperty(AzureServicePrincipalName, ServiceUrl);
    set $secret = $SecureCredentialProperty(AzureServicePrincipalName, Secret);

    InedoCore::Exec
    (
        FileName: $ExeFileName,
        Arguments: login --service-principal -u $applicationId -p $secret --tenant $tenantId
    );

    InedoCore::Exec
    (
        FileName: ExeFileName,
        Arguments: $args
    );
}
```


#### Azure-Execute Module (BuildMaster 2022.8 and lower):
```
module Azure-Execute<$args>
{
    set $ExeFileName = "C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\wbin\az.cmd";

    InedoCore::Exec
    (
        FileName: $ExeFileName,
        Arguments: login --service-principal -u <applicationId> -p <secret> --tenant <tenantId>
    );

    InedoCore::Exec
    (
        FileName: ExeFileName,
        Arguments: $args
    );
}
```

Once created as a global module, `Azure-Execute` can now be used easily from any OtterScript plan in BuildMaster to start [creating](https://docs.microsoft.com/en-us/azure-stack/user/azure-stack-quick-create-vm-windows-cli) resources such as [databases](https://docs.microsoft.com/en-us/cli/azure/sql/db?view=azure-cli-latest#az-sql-db-create), [virtual machines](https://docs.microsoft.com/en-us/azure-stack/user/azure-stack-quick-create-vm-windows-cli), and more. 


#### Create a Resource Group Named After Your BuildMaster Application
```
call Azure-Execute
(
    args: group create --name $ApplicationName-rg --location centralus
);
```

This runs these two commands to create a resource group in Azure.
```
az login --service-principal -u <applicationId> -p <secret> --tenant <tenantId>
az group create --name $ApplicationName-rg --location centralus
```

### More Commands

#### Create an Azure Web App Plan

```
call Azure-Execute
(
    args: appservice plan create --name $ApplicationName-plan --resource-group $ApplicationName-rg--sku F1
);
```
#### Create an Azure Web App

```
call Azure-Execute
(
    args: webapp create --name $ApplicationName --resource-group $ApplicationName-rg --plan $ApplicationName-plan
);
```

#### Create an Azure Web App Slot

```
call Azure-Execute
(
    args: webapp deployment slot create --name $ApplicationName --resource-group $ApplicationName-rg --slot $ReleaseNumber
);
```

#### Create a SQL Server on Azure in the Resource Group You Created
```
call Azure-Execute
(
    args: sql server create --admin-password mysecret --admin-user $ApplicationName-admin  --name $ApplicationName-server --resource-group $ApplicationName-rg --location centralus 
);
```

#### Create a Standard S0 Database Named After Your BuildMaster Pipeline
``` 
InedoCore::Exec
(
    FileName: "C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\wbin\az.cmd",
    Arguments: sql db create -g $ApplicationName-rg -s $ApplicationName-server -n $PipelineName-db --service-objective S0
);
```


