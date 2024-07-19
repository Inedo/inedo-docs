---
title: "HOWTO: Migrate from Jfrog Artifactory to ProGet"
order: 3
---

Migrating a JFrog Artifactory repository to ProGet can be done in a matter of minutes using ProGet’s Feed Importer feature. This feature allows packages from a remote feed to be downloaded to a local feed. 

This article will guide you through how to use the Feed Importer feature to migrate packages from your Jfrog Artifactory repositories to ProGet, offering several methods depending on the package type. 

## Generating Your API Token
Migrating your packages from JFrog Artifactory using any of the methods listed in this article will require a JFrog Artifactory API token. This step is also required to find the URL you will need for [configuring a connector](#configuring-a-connector), covered later in this article.

This article will explain how to acquire an API token from a NuGet repository, although doing so for other packages is conceptually the same.

From JFrog Artifactory, navigate to "Artifacts" and select your "local" NuGet repository. In this case, named `test-nuget-local`. From here select "Set Me Up".

![Jfrog Setup](/resources/docs/jfrog-nuget-select.png){height="" width="50%"}

Next, select "Generate Token & Create Instructions", and copy the API token generated. If you plan to [configure a connector](#configuring-a-connector), also copy the URL. 

![Copy Token](/resources/docs/jfrog-nuget-pat.png){height="" width="50%"}

# Migrating NuGet, NPM or Maven Packages from Jfrog Artifactory to ProGet
Migrating your NuGet, NPM or Maven packages can be done quickly and easily, in only a few clicks. All you will need is your Jfrog Artifactiory username and the API token we generated above, along with the server URL of your repository (e.g. `example.jfrog.io`).

### Step 1: Create a New Feed
To begin we'll create a new feed that we will import our existing packages to. Start by navigating to "Feeds" and selecting "New Feed".

![Create New Feed](/resources/docs/proget-feeds-createnewfeed.png){height="" width="50%"}

Then select the package type. In this case, we will select "NuGet (.NET) Packages".

![Create NuGet Feed](/resources/docs/proget-newfeed-nugetselect.png){height="" width="50%"}

### Step 2: Connect to Jfrog Artifactory
As we will be connecting to our Jfrog Artifactiory repository, select "Connect to Another Feed".

![Connect Feed](/resources/docs/proget-createfeed-anotherfeed.png){height="" width="50%"}

Next, select "Import Packages from Another Feed".

![Import](/resources/docs/proget-createfeed-importpackages.png){height="" width="50%"}

Then name your feed. In this case, we will call it `internal-nuget`.

![Name Feed](/resources/docs/proget-createfeed-name.png){height="" width="50%"}

Now select "Jfrog Artifactory", which will configure ProGet to migrate your packages from Jfrog Artifactory.

![Jfrog](/resources/docs/proget-createfeed-migrate-artifactory.png){height="" width="50%"}

### Step 3: Migrate Your Packages
Now enter your Artifactory Server URL (e.g. `https://example.jfrog.io`), your username and the API Token you generated [here](#generating-your-api-token). 

![Jfrog Migrate](/resources/docs/proget-migrate-artifactory.png){height="" width="50%"}

Next, select the repository of your NuGet packages. ProGet will automatically filter your NuGet repository from others, making it easier to find and select. In this case, our repository name is `test-nuget-local`. Then select "Confirm Import".

![Migrate](/resources/docs/proget-migrate-artifactory-feed.png){height="" width="50%"}

Finally, select "Begin Import". ProGet will now migrate all of your Jfrog Artifactory packages. Navigate to "Feeds" and the `internal-nuget` feed you created, where it will list all migrated packages.

![Migrated](/resources/docs/proget-nugetfeed-fakepackages.png){height="" width="50%"}

## Migrating Other Packages to ProGet

Due to limitations in API, other packages (e.g. PyPi, Conda, etc) will require alternative approaches to migrate your packages. However, these too remain quick and easy to do. For these packages, you have two choices:

* [Upload packages](#uploading-via-bulk-upload) in bulk from another source
* [Configure a connector](#configuring-a-connector) to allow tools to download/pull packages directly from ProGet 

## Uploading via Bulk Upload
Using ProGet's bulk upload function you can download packages from Jfrog Artifactory, and then upload them in bulk to ProGet. Here we will show how to bulk upload PyPi packages, although uploading other packages is conceptually the same.

### Step 1: Download Packages from Artifactory
To download your packages from Artifactory, start by navigating to Packages, and then search by name (or filter by package type).

![Jfrog Search](/resources/docs/jfrog-python-search.png){height="" width="50%"}

Select a package you want to download and then select the download icon. Repeat this step as necessary for all packages you want to migrate to ProGet.

![Jfrog Download](/resources/docs/jfrog-python-download.png){height="" width="50%"}

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
An alternative to bulk importing is to set up a connector to your Jfrog Artifactory repository in ProGet. This will allow you to access "legacy" packages if needed by using tools to pull or download packages directly from ProGet, while primarily using the feed to store current versions of packages you are using. 

In this article we will connect to a Pypi repository in Jfrog Artifactory, however connecting to any other package repository is conceptually the same. 

In ProGet, navigate to the feed you want to configure a connector with. If you need to create one [follow these steps](#step-1-create-a-new-feed) earlier in this article. From here select "Manage Feed".

![Manage Python Feed](/resources/docs/proget-python-managefeed.png){height="" width="50%"}

From here navigate to "Connectors" and select "add".

![Add Connector](/resources/docs/proget-python-connectors-add.png){height="" width="50%"}

Now enter your username as well as your full connector URL (e.g. `https://example.jfrog.io/artifactory/api/pypi/test-pypi-local`) and API Token, both of which you will have copied in [this step](#generating-your-api-token).

![Add Connector Artifactory](/resources/docs/proget-createconnector-artifactory.png){height="" width="50%"}

Finally, select the "Advanced" tab and check the "Search using package match only", which will allow you to search for packages by name. Then, select "Save".

![Advanced](/resources/docs/proget-createconnector-advanced.png){height="" width="50%"}

You have now configured a connector, allowing tools to pull/download packages from ProGet directly.

![Connector Added](/resources/docs/proget-connector-created.png){height="" width="50%"}

ProGet can [connect to any repositories](/docs/proget/feeds/connector-overview) — including Jfrog’s artifactory — which makes migration and parallel usage easy, but you can also use our [professional services](https://inedo.com/professional-services) to help with the migration, and we have a [Migration Guide from Jfrog to ProGet](https://inedo.com/support/resources/jfrogs-guide-to-migate-to-proget).

