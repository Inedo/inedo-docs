---
title: "Migrate an On-Premise ProGet Installation to Azure"
order: 6
---

As the cloud popularity grows, many users have began to migrate their infrastructure to different cloud providers.  Microsoft Azure is one of the most, if not the most, popular cloud provider today.  Microsoft has invested a lot into their platform and they have added great integration built directly into their Windows products.  This allows users to have an easier time migrating their internal infrastructure to the cloud or building a hybrid Windows-based architecture leveraging both on-premise and cloud-based architectures.

If you’re evaluating which platform is right for your organization, see [ProGet vs. Azure Artifacts](https://inedo.com/proget/vs-azure-artifacts) for a feature and management comparison that can help guide migration decisions.

This guide helps to direct users on how to migrate an on-premise ProGet installation to Microsoft Azure.

Typically ProGet is setup in Azure using a VM.  Some Azure components you will need for this configuration are:
- Azure SQL
- A virtual machine to run ProGet and the ProGet Service
- Optionally:
    - Azure Blob Storage for the package storage (you can also use the storage on the VM for this)
        - One downside we have observed is that Azure's blob storage is not always the fastest in the more affordable tiers.
    - Azure Front Door as a proxy.  Azure VM's typically come with an external IP address that can also be used without the need for a proxy.
   
Coming soon, you will be able to migrate to an App Service using the ProGet docker image. The Azure components you would need is:
- Azure SQL
- Azure App service plan with a Linux based webapp
- Azure Blob Storage for the package store
- Azure Front Door (or another proxy for SSL offloading and improved connection handling)
    - This can also be a second App Service running an nginx image

## Methods For Migrating Your ProGet Server

There are three ways to migrate ProGet to a VM on Azure:
1. Full database and package file move
2. Migrate feeds using Feed Replication (**Enterprise Only**)
3. Migrate using a drop path (excludes Docker)
	- Maven can be migrated using direct filesystem copy and a provided SQL script
	
**It is recommended to use the same version on both your local servers and in Azure. Although you can upgrade at the same time, it can make the move process much more complicated.**

### Base Setup
 In all cases, you will first need to setup ProGet on a VM.  To do this:
- Setup a new windows based Azure VM
   - See our [install guide](/docs/proget/installation/installation-guide) for the minimum requirements.
- Create a new database named `ProGet` on Azure SQL
- Install ProGet on the new VM pointing to the database you created in the previous step.  **Make sure to install the same version that is installed on your local server**

### To migrate the full database and package files from on-prem to an Azure VM
1. Before migrating, you may want to disable publish access to your ProGet service (so no new packages are published during the migration)
    - The easiest way to do this is to deny all users for the Publish task
2. You will need to back-up some components of your ProGet server
    1. Your packages (if not already stored in Azure Blob storage).  
		- You can find your storage path by looking at the `Storage.PackagesRootPath` in your _Advanced Settings_
	2. Your Database
	3. Your Encryption key. Your encryption key is stored in your [shared config](/docs/installation/configuration-files).  This is typically stored at `%PROGRAMDATA%\Inedo\SharedConfig\ProGet.config`.  
		- If you are not using a shared config file, then you will need to grab the encryption key from your `Web_appSettings.config` and the `App_appSettings.config` files found in your ProGet installation folder which should be installed to `C:\Program Files\ProGet` or `C:\ProGet\`.
		- If you are not using the shared config on the old server, the new server will install using the shared config.  You can copy your encryption key into the shared config on the new server.
		- Your database connection string is also stored in these files if you need to find it. 
3. Copy your packages
   - If you are planning to use Azure Blob storage, you will first need to [migrate your existing feeds to use Azure blob storage](/docs/proget/advanced-features/proget-advanced-cloud-storage).
      - Migrating your feeds to Azure Blob Storage can take a significant amount of time based on your Feed size and your Blob storage tier.
   - If you are planning to just use the local storage on the VM, you will need to copy your existing packages to the VM at the **same folder path as your existing server**
	  - Please note, this may take some time to transfer all these files up.
4. Restore your ProGet database to Azure SQL.  For instructions, see Microsoft's [Tutorial: Migrate SQL Server to Azure SQL Database offline using DMS](https://docs.microsoft.com/en-us/azure/dms/tutorial-sql-server-to-azure-sql).
5. Update your encryption key in the [shared config](/docs/installation/configuration-files) file on the new VM.

### To migrate using feed replication:

See our [feed migration guide](/docs/proget/feeds/feed-overview/proget-administration-migrating-a-proget-feed).

::: (INFO) (NOTE)
This method requires a ProGet Enterprise license and requires either the source instance to be accessible to the target or the target instance to be accessible to the source over the network. If you do not have ProGet Enterprise or your instances are isolated, you will need to use an alternative method. 
:::

### To migrate using a drop path

See our [feed migration guide](/docs/proget/feeds/feed-overview/proget-administration-migrating-a-proget-feed).

::: (WARNING) (NOTE)
This method will not carry over your download counts, statistics, or your feed connectors.
:::