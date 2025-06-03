---
title: "HOWTO: Migrate from Jfrog Artifactory to ProGet"
order: 3
---

Migrating a JFrog Artifactory repository to ProGet can be done in a matter of minutes using ProGet’s Feed Importer feature. This feature allows packages from a remote feed to be downloaded to a local feed. 

This article will guide you through how to use the Feed Importer feature to migrate packages from your Jfrog Artifactory repositories to ProGet, offering several methods depending on the package type.

## Generating your Personal Access Token

Migrating your packages by connecting to JFrog Artifactory will require a Personal Access Token. 

From JFrog Artifactory, navigate to "Artifacts", and select your local NuGet repository, in this case named `test-nuget-local`. From here select "Set Me Up".

![JFrog Menu](/resources/docs/jfrog-artifacts-setmeup.png){height="" width="50%"}

Next, select "Generate Token & Create Instructions", and copy the access token generated. Finally, copy your JFrog Artifactory URL.

![Create Access Token](/resources/docs/jfrog-artifacts-createaccesstoken.png){height="" width="50%"}

## Migrating Packages from JFrog Artifactory to ProGet

Migrating your packages can be done quickly and easily, in only a few clicks. All you will need is your JFrog Artifactory username, the access token we generated above, along with the server URL of your repository (e.g. `example.jfrog.io`).

### Step 1: Create a New Feed

To begin, we'll create a new feed that we will import our existing packages to. Start by navigating to "Feeds" and slecting "New Feed".

![Create New Feed](/resources/docs/proget-feeds-createnewfeed.png){height="" width="50%"}

Then select the package type. In this case, we will select "NuGet (.NET) Packages".

![Create NuGet Feed](/resources/docs/proget-newfeed-nugetselect.png){height="" width="50%"}

### Step 2: Connect to JFrog Artifactory

As we will be connecting to our JFrog Artifactory repository, select "Connect to Another Feed".

![Create Connector](/resources/docs/proget-createfeed-anotherfeed.png){height="" width="50%"}

Next, select "Import Packages from Another Feed".

![Import Packages](/resources/docs/proget-createfeed-importpackages.png){height="" width="50%"}

Then name your feed. In this case, we will call it `internal-nuget`.

![Name Feed](/resources/docs/proget-createfeed-name.png){height="" width="50%"}

Now select "JFrog Artifactory", which will configure ProGet to migrate your packages from JFrog Artifactory.

![JFrog](/resources/docs/proget-createfeed-migrate-jfrog){height="" width="50%"}

### Step 3: Migrate Your Packages

Now enter your JFrog Artifactory URL and access token you generated [here](#generating-your-personal-access-token-pat). 

![Configure Migration](/resources/docs/proget-migrate-jfrog.png){height="" width="50%"}

Next, select the repository of your NuGet packages. ProGet will automatically filter your NuGet repository from others, making it easier to find and select. In this case, our repository name is `test-nuget-local`. Then select "Confirm Import".

![Select Repository](/resources/docs/proget-migrate-jfrog-feed.png){height="" width="50%"}

Finally, select "Begin Import". ProGet will now migrate all of your Azure DevOps packages. Navigate to "Feeds" and the `internal-nuget` feed you created, where it will list all migrated packages.

![Migrated Packages](/resources/docs/proget-nugetfeed-fakepackages.png){height="" width="50%"}

## Uploading via Bulk Upload

Using ProGet's bulk upload function you can download packages from JFrog Artifactory, then upload them in bulk to ProGet. Here we will show you how to bulk upload PyPi packages, although uploading other package types is conceptually the same.

## Step 1: Download Packages from JFrog Artifactory

To download your packages from JFrog Artifactory, start by navigating to "Packages", then select the package you want to download. In this case, we want to download `dummy-py-library`.

![JFrog Packages](/resources/docs/jfrog-feeds-selectpackage.png){height="" width="50%"}

From here, select the download icon to download the package.

![JFrog Download](/resources/docs/jfrog-feeds-downloadpackage.png){height="" width="50%"}

### Step 2: Upload Packages in Bulk

From ProGet, navigate to the feed you want to upload your packages to. If you need to create one [follow these steps](#step-1-create-a-new-feed) from earlier in this article. From here select "Import Packages".

![Import Packages](/resources/docs/proget-python-addpackage.png){height="" width="50%"}

Then select "Import/Copy Package Files on Disk".

![Bulk Package Import](/resources/docs/proget-python-addpackage-bulkimport.png){height="" width="50%"}

Next, specify the source of your packages, and select "Import".

![Bulk Import](/resources/docs/proget-python-bulkimport.png){height="" width="50%"}

Navigating back to the feed, we can see all packages have been uploaded.

![Python Feed](/resources/docs/proget-python-feed.png){height="" width="50%"}