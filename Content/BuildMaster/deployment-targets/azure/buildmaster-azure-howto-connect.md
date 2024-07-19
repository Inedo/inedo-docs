---
title: "HOWTO: Connect BuildMaster to Azure"
order: 3
---

To deploy your applications to Azure App Services, you'll need to first connect to [Azure](https://azure.microsoft.com/) by creating an Azure Service Principal in BuildMaster.

This guide will explain how to find and set up the necessary credentials to create an Azure Service Principal in Azure, and use them to create an Azure Service Principal account in BuildMaster.

## Step 1 - Locating your Tenant ID
Log in to your [Azure Portal](https://portal.azure.com/), navigate to your Azure Active Directory and then click "Properties".

![Home](/resources/docs/azureaad-main-aad-select.png){height="" width="50%"}

Scroll down to the "Tenant ID" section and copy the "Tenant ID", this will be used in a future step.

![Tenant ID](/resources/docs/azureaad-properties-tenantid.png){height="" width="50%"}

## Step 2 - Registering and locating your Application ID
Navigate to "App Registrations" on the left and click "New Registration".

![New App](/resources/docs/azureaad-appregistration-newregistration.png){height="" width="50%"}

Enter a name and select the supported account type and then click "Register". Copy the "Application ID", this will be used in a future step.

![Register](/resources/docs/azureaad-newappregister.png){height="" width="50%"}

## Step 3 - Adding and copying your Client Secret
Navigate to "App Registrations" and select your newly created Application.

![Select App](/resources/docs/azureaad-appregistration-selectapp.png){height="" width="50%"}

Navigate to "Certificates & secrets" on the left and add a new Client Secret. 

![New Secret](/resources/docs/azureaad-app-newsecret.png){height="" width="50%"}

Copy the "Value" (_not_ the Secret ID). This will be used as the "Client Secret" in a future step.

![Secret](/resources/docs/azureaad-app-secret.png){height="" width="50%"}

## Step 4 - Creating a Service Principal
Navigate to your Azure Subscription in the root of the Azure Portal and then click "Access control (IAM)" and add a new role assignment.

![Select Subscription](/resources/docs/azureaad-selectsubscription.png){height="" width="50%"}

![Add Role](/resources/docs/azureaad-subscription-addrole.png){height="" width="50%"}

Locate and click the "Contributor" role under "Privileged administrator roles" and click next.

![Contributor](/resources/docs/azureaad-subscription-selectcontributor.png){height="" width="50%"}

Click "Select Members", then search for and select the app registration you created in Step 5, and click "Select".

![Select Members](/resources/docs/azureaad-subscription-selectmembers.png){height="" width="50%"}

Click "Review + assign". You will receive a message indicating that the application was added as a contributor to the subscription.

![Review Assign](/resources/docs/azureaad-subscription-reviewassign.png){height="" width="50%"}

## Step 5 - Creating your Azure Service Principal account in Buildmaster

Open BuildMaster, navigate to "Administration" > "Services & Credentials" and click "Add Service Account".

![Service Credentials](/resources/docs/buildmaster-settings-servicescredentials.png){height="" width="50%"}

![Add Account](/resources/docs/buildmaster-addprincipalserviceaccount.png){height="" width="50%"}

Select "Cloud Service Account" > "Azure Service Principal", and then enter your "Tenant ID", "Application ID", and "Client Secret" from the service principal you created in Azure. Finally, click "Create Azure Service Principal".

![Add Principal](/resources/docs/buildmaster-addazureprincipal.png){height="" width="50%"}

:::(Internal) (This shouldn't be needed?)
Click the "Advanced" tab and enter a Credential Name.  

![Advanced Tab](/resources/docs/buildmaster-addazureprincipalname.png){height="" width="50%"}
:::

Following these steps allows us to create an Azure service principal that we can then use to connect to Buildmaster.

![Added Principal](/resources/docs/buildmaster-addedazureprincipal.png){height="" width="50%"}