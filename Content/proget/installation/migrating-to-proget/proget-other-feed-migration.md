---
title: "HOWTO: Migrate from Another Repository to ProGet"
order: 4
---

Migrating a repository to ProGet can be done in a matter of minutes using ProGet’s Feed Importer feature. This feature allows packages from a remote feed to be downloaded to a local feed. 

:::(Info) (Using JFrog Artifactory, Azure DevOps Artifacts, or MyGet?)
ProGet has some extra features to help migrate from these services. See
* [Azure DevOps Artifacts Migration Guide](/docs/proget/installation/migrating-to-proget/proget-azure-devops-feed-migration)
* [Jfrog Artifactory Migration Guide](/docs/proget/installation/migrating-to-proget/proget-jfrog-artifactory-feed-migration)
* [MyGet Migration Guide](/docs/proget/installation/migrating-to-proget/proget-other-feed-migration)
:::


This article will guide you through how to use the Feed Importer feature to migrate packages from other repositories to ProGet, offering several methods depending on the package type. 


## API tokens and Endpoint URLs
To migrate packages from other repositories to ProGet, you will often need an API token and a Feed Endpoint URL. 

The method to obtain this will vary from service to service. Please consult the relevant service documentation on how to do this.


## Migrating NuGet Packages from Another Repository to ProGet
Migrating your NuGet can be done quickly and easily, in only a few clicks. All you will need is the Endpoint URL, your username and password or API token.

### Step 1: Create a New Feed
To begin we'll create a new feed that we will import our existing packages to. Start by navigating to "Feeds" and selecting "New Feed".

![Create New Feed](/resources/docs/proget-feeds-createnewfeed.png){height="" width="50%"}

Then select the package type. In this case, we will select "NuGet (.NET) Packages".

![Create NuGet Feed](/resources/docs/proget-feeds-nugetselect.png){height="" width="50%"}

### Step 2: Connect to Another Repository
As we will be connecting to another repository, select "Connect to Another Feed".

![Connect Feed](/resources/docs/proget-createfeed-anotherfeed.png){height="" width="50%"}

Next, select "Import Packages from Another Feed".

![Import](/resources/docs/proget-createfeed-importpackages.png){height="" width="50%"}

Then name your feed. In this case, we will call it `internal-nuget`.

![Name Feed](/resources/docs/proget-createfeed-name.png){height="" width="50%"}

Now select "Other Repository/Server Type" to configure ProGet to migrate your packages from another repository.

![Other Repo](/resources/docs/proget-createfeed-migrate-other.png){height="" width="50%"}

### Step 3: Migrate Your Packages
Now enter your Endpoint URL, username and password/API token, and select "Import Options".

![Azure Migrate](/resources/docs/proget-migrate-other.png){height="" width="50%"}

Confirm the details are correct, and then select "Confirm Import".

![Migrate](/resources/docs/proget-migrate-other-confirm.png){height="" width="50%"}

Finally, select "Begin Import". ProGet will now migrate all of your repository packages. Navigate to "Feeds" and the `internal-nuget` feed you created, where it will list all migrated packages.

![Migrated](/resources/docs/proget-nugetfeed-fakepackages.png){height="" width="50%"}

# Migrating Other Packages to ProGet
Due to limitations in API, other packages (e.g. PyPi, Conda, etc) will require alternative approaches to migrate your packages. However, these too remain quick and easy to do. For these packages, you have two choices:

* [Upload packages](#uploading-via-bulk-upload) in bulk from another source
* [Configure a connector](#configuring-a-connector) to allow tools to download/pull packages directly from ProGet 

## Uploading via Bulk Upload
Using ProGet's bulk upload function you can download packages from Azure DevOps, and then upload them in bulk to ProGet. Here we will show how to bulk upload PyPi packages, although uploading other packages is conceptually the same.

### Step 1: Download Packages from Another Repository

First you will need to download your packages from your other repository. The method to do this will vary from service to service. Please consult the relevant service documentation on how to do this.

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
An alternative to bulk importing is to set up a connector to repository in ProGet. This will allow you to access "legacy" packages if needed by using tools to pull or download packages directly from ProGet, while primarily using the feed to store current versions of packages you are using. 

In this article we will connect to a Pypi repository, however connecting to any other package repository is conceptually the same. 

### Step 1: Obtain Repository Credentials
To create a connector to your repository in ProGet, you will need a Connector URL (Endpoint URL), username and password/API token.

The method to obtain these will vary from service to service. Please consult the relevant service documentation on how to do this.

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