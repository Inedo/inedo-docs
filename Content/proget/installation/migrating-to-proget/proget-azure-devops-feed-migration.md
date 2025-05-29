---
title: "HOWTO: Migrate from Azure DevOps to ProGet"
order: 2
---

Migrating an Azure DevOps repository to ProGet can be done in a matter of minutes using ProGet’s Feed Importer feature. This feature allows packages from a remote feed to be downloaded to a local feed. 

This article will guide you through how to use the Feed Importer feature to migrate packages from your Azure DevOps repositories to ProGet. 

## Generating your Personal Access Token
Migrating your packages from Azure DevOps using any of the methods listed in this article will require a Personal Access Token. 

From Azure DevOps, navigate to "Settings" and select "Personal Access Tokens".

![Azure Settings](/resources/docs/azure-settings-pat.png){height="" width="50%"}

Now select "New Token" from the top right and then configure the details of your token. Once this is done select "Create".

![Create Token](/resources/docs/azure-pat-create.png){height="" width="50%"}

Finally, make sure you copy the Personal Access Token provided.

![Copy Token](/resources/docs/azure-pat-save.png){height="" width="50%"}

## Migrating Packages from Azure DevOps to ProGet
Migrating your packages can be done quickly and easily, in only a few clicks. All you will need is your Azure DevOps organization name and the Personal Access Token we generated above.

### Step 1: Create a New Feed
To begin we'll create a new feed that we will import our existing packages to. Start by navigating to "Feeds" and selecting "New Feed".

![Create New Feed](/resources/docs/proget-feeds-createnewfeed.png){height="" width="50%"}

Then select the package type. In this case, we will select "NuGet (.NET) Packages".

![Create NuGet Feed](/resources/docs/proget-newfeed-nugetselect.png){height="" width="50%"}

### Step 2: Connect to Azure DevOps
As we will be connecting to our Azure DevOps repository, select "Connect to Another Feed".

![Connect Feed](/resources/docs/proget-createfeed-anotherfeed.png){height="" width="50%"}

Next, select "Import Packages from Another Feed".

![Import](/resources/docs/proget-createfeed-importpackages.png){height="" width="50%"}

Then name your feed. In this case, we will call it `internal-nuget`.

![Name Feed](/resources/docs/proget-createfeed-name.png){height="" width="50%"}

Now select "Azure Artifacts", which will configure ProGet to migrate your packages from Azure DevOps.

![Azure](/resources/docs/proget-createfeed-migrate-azure.png){height="" width="50%"}

### Step 3: Migrate Your Packages
Now enter your organization name and the Personal Access Token you generated [here](#generating-your-personal-access-token-pat). 

![Azure Migrate](/resources/docs/proget-migrate-azure.png){height="" width="50%"}

Next, select the repository of your NuGet packages. ProGet will automatically filter your NuGet repository from others, making it easier to find and select. In this case, our repository name is `test-nuget`. Then select "Confirm Import".

![Migrate](/resources/docs/proget-migrate-azure-feed.png){height="" width="50%"}

Finally, select "Begin Import". ProGet will now migrate all of your Azure DevOps packages. Navigate to "Feeds" and the `internal-nuget` feed you created, where it will list all migrated packages.

![Migrated](/resources/docs/proget-nugetfeed-fakepackages.png){height="" width="50%"}

## Uploading via Bulk Upload
Using ProGet's bulk upload function you can download packages from Azure DevOps, and then upload them in bulk to ProGet. Here we will show how to bulk upload PyPi packages, although uploading other packages is conceptually the same.

### Step 1: Download Packages from Azure DevOps
To download your packages from Azure DevOps, start by navigating to "Artifacts", select the appropriate feed (e.g. `test-pypi`), and then select the package you want to download. In this case, we want to download `dummy-py-library`.

![Azure Search](/resources/docs/azure-feeds-selectpackage.png){height="" width="50%"}

From here, select the file name to download the package.

![Azure Download](/resources/docs/azure-feeds-downloadpackage.png){height="" width="50%"}

### Step 2: Upload Packages in Bulk
From ProGet, navigate to the feed you want to upload your packages to. If you need to create one [follow these steps](#step-1-create-a-new-feed) earlier in this article. From here select "Import Packages".

![Add Package](/resources/docs/proget-python-addpackage.png){height="" width="50%"}

Then select "Import/Copy Package Files on Disk".

![Bulk Package Import](/resources/docs/proget-python-addpackage-bulkimport.png){height="" width="50%"}

Next, specify the source of your packages, and select "Import".

![Bulk Import](/resources/docs/proget-python-bulkimport.png){height="" width="50%"}

Navigating back to the feed, we can see all packages have been uploaded.

![Python Feed](/resources/docs/proget-python-feed.png){height="" width="50%"}