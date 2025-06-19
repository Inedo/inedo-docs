---
title: "HOWTO: Migrate from JFrog Artifactory to ProGet"
order: 3
---

Migrating a JFrog Artifactory repository to ProGet can be done quickly with ProGet’s Feed Importer feature. This feature allows packages from a remote feed to be downloaded to a local feed. 

This article will guide you through how to use the Feed Importer feature to migrate your packages from your Jfrog Artifactory repositories to ProGet.

## Generating your Personal Access Token

Migrating your packages by connecting to JFrog Artifactory will require a Personal Access Token.

From JFrog Artifactory, navigate to "Artifacts", and select your local repository, in this case our NuGet repository named `test-nuget-local`. From here select "Set Me Up".

![JFrog Menu](/resources/docs/jfrog-artifacts-setmeup.png){height="" width="50%"}

Next, select "Generate Token & Create Instructions", and copy the access token generated. Finally, copy your JFrog Artifactory URL.

![Create Access Token](/resources/docs/jfrog-artifacts-createaccesstoken.png){height="" width="50%"}

## Migrating Packages from JFrog Artifactory to ProGet

Migrating your packages can be done quickly and easily, in only a few clicks. All you will need is your JFrog Artifactory Personal Access Token, along with the server URL of your repository (e.g. `example.jfrog.io`).

### Step 1: Create a New Feed

To begin, we'll create a new feed that we will import our existing packages to. Start by navigating to "Feeds" and selecting "New Feed".

![Create New Feed](/resources/docs/proget-feeds-createnewfeed.png){height="" width="50%"}

Then select the package type. In this case, we will select "NuGet (.NET) Packages".

![Create NuGet Feed](/resources/docs/proget-newfeed-nugetselect.png){height="" width="50%"}

As we will be connecting to our JFrog Artifactory repository, here we will select "Private/Internal NuGet (.NET) Packages".

![Create Private Feed](/resources/docs/proget-createfeed-privatefeed.png){height="" width="50%"}

Now name your feed, for example we will call ours `internal-nuget`.

![Name Feed](/resources/docs/proget-createfeed-name.png){height="" width="50%"}

You'll be taken to your new NuGet feed, currently empty.

![Empty NuGet Feed](/resources/docs/proget-nuget-internal-empty.png){height="" width="50%"}

### Step 2: Connect to JFrog Artifactory

Now we have created a NuGet feed, we can connect it to our JFrog Artifactory repository. From the "View Packages" tab, navigate to the dropdown menu and select "Import Packages".

![Import Packages](/resources/docs/proget-importpackages.png){height="" width="50%"}

To connect to your remote JFrog feed, select "Download Package From Another Service".

![Download Packages](/resources/docs/proget-downloadpackage-azure.png){height="" width="50%"}

Next select "JFrog Artifactory" to configure ProGet to migrate your packages from your JFrog repository.

![JFrog Artifactory](/resources/docs/proget-connectors-jfrog.png){height="" width="50%"}

### Step 3: Migrate Your Packages

Now, using the "Access Key Authentication type" option, enter your JFrog server URL, and the JFrog Personal Access Token.

![Enter Details](/resources/docs/proget-migrate-jfrog.png){height="" width="50%"}

Then, select the repository of your NuGet packages, which ProGet will automatically filter from others, making it easier to find. In this case, our JFrog repository is called `test-nuget-local`. Then select "Confirm Import".

![Select Repository](/resources/docs/proget-migrate-jfrog-feed.png){height="" width="50%"}

Finally, select "Begin Import". ProGet will now migrate all of your JFrog Artifactory packages. Navigate to the `internal-nuget` feed you created, where it will be populated with all the migrated packages.

![Migrated Packages](/resources/docs/proget-nugetfeed-fakepackages.png){height="" width="50%"}