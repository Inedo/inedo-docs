---
title: "HOWTO: Migrate from Azure DevOps to ProGet"
order: 2
---

Migrating an Azure DevOps repository to ProGet can be done in a matter of minutes using ProGet’s Feed Importer feature. This feature allows packages from a remote feed to be downloaded to a local feed. 

This article will guide you through how to use the Feed Importer feature to migrate packages from your Azure DevOps repositories to ProGet, offering several methods depending on the package type. 

## Generating your Personal Access Token
Migrating your packages from Azure DevOps using any of the methods listed in this article will require a Personal Access Token. 

From Azure DevOps, navigate to "Settings" and select "Personal Access Tokens".

![Azure Settings](/resources/docs/azure-settings-pat.png){height="" width="50%"}

Now select "New Token" from the top right and then configure the details of your token. Once this is done select "Create".

![Create Token](/resources/docs/azure-pat-create.png){height="" width="50%"}

Finally, make sure you copy the Personal Access Token provided.

![Copy Token](/resources/docs/azure-pat-save.png){height="" width="50%"}

# Migrating NuGet, NPM or Maven Packages from Azure DevOps to ProGet
Migrating your NuGet, NPM or Maven packages can be done quickly and easily, in only a few clicks. All you will need is your Azure DevOps organization name and the Personal Access Token we generated above.

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

# Migrating Other Packages to ProGet
Due to limitations in API, other packages (e.g. PyPi, Conda, etc) will require alternative approaches to migrate your packages. However, these too remain quick and easy to do. For these packages, you have two choices:

* [Upload packages](#uploading-via-bulk-upload) in bulk from another source
* [Configure a connector](#configuring-a-connector) to allow tools to download/pull packages directly from ProGet 

## Uploading via Bulk Upload
Using ProGet's bulk upload function you can download packages from Azure DevOps, and then upload them in bulk to ProGet. Here we will show how to bulk upload PyPi packages, although uploading other packages is conceptually the same.

### Step 1: Download Packages from Azure DevOps
To download your packages from Azure DevOps, start by navigating to "Artifacts", select the appropriate feed (e.g. `test-pypi`), and then select the package you want to download. In this case, we want to download `dummy-py-library`

![Azure Search](/resources/docs/azure-feeds-selectpackage.png){height="" width="50%"}

From here, select the file name to download the package.

![Azure Download](/resources/docs/azure-feeds-downloadpackage.png){height="" width="50%"}

### Step 2: Upload Packages in Bulk
From ProGet, navigate to the feed you want to upload your packages to. If you need to create one [follow these steps](#step-1-create-a-new-feed) earlier in this article. From here select "Add Package".

![Add Package](/resources/docs/proget-python-addpackage.png){height="" width="50%"}

Then select "Bulk Package Import/File Copy".

![Bulk Package Import](/resources/docs/proget-python-addpackage-bulkimport.png){height="" width="50%"}

Next, specify the source of your packages, and select "Import".

![Bulk Import](/resources/docs/proget-python-bulkimport.png){height="" width="50%"}

Navigating back to the feed, we can see all packages have been uploaded.

![Python Feed](/resources/docs/proget-python-feed.png){height="" width="50%"}

## Configuring a Connector
An alternative to bulk importing is to set up a connector to your Azure DevOps repository in ProGet. This will allow you to access "legacy" packages if needed by using tools to pull or download packages directly from ProGet, while primarily using the feed to store current versions of packages you are using. 

In this article we will connect to a Pypi repository in Azure DevOps, however connecting to any other package repository is conceptually the same. 

### Step 1: Find Your Connector URL in Azure DevOps

To create a connector you will need your feed-specific connector URL. From Azure DevOps navigate to "Artifacts", select the appropriate feed and then select "Connect to Feed".

![Azure Connect Feed](/resources/docs/azure-feeds-connect.png){height="" width="50%"}

For a PyPi feed, select "Twine". Then copy the repository URL from the "Project Setup" section.

![Manage Python Feed](/resources/docs/azure-python-url.png){height="" width="50%"}

### Step 2: Configure Your Connector in ProGet

In ProGet, navigate to the feed you want to configure a connector with. If you need to create one [follow these steps](#step-1-create-a-new-feed) earlier in this article. From here select "Manage Feed".

![Manage Python Feed](/resources/docs/proget-python-managefeed.png){height="" width="50%"}

From here navigate to "Connectors" and select "add".

![Add Connector](/resources/docs/proget-python-connectors-add.png){height="" width="50%"}

Now enter your username, the connector URL from [Step 1](#generating-your-personal-access-token) and your Personal Access Token you generated [here](#generating-your-api-token).

![Add Connector Artifactory](/resources/docs/proget-createconnector-azure.png){height="" width="50%"}

Finally, select the "Advanced" tab and check the "Search using package match only", which will allow you to search for packages by name. Then, select "Save".

![Advanced](/resources/docs/proget-createconnector-advanced.png){height="" width="50%"}

You have now configured a connector, allowing tools to pull/download packages from ProGet directly.

![Connector Added](/resources/docs/proget-connector-created.png){height="" width="50%"}