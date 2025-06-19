---
title: "HOWTO: Migrate from Sonatype Nexus to ProGet"
order: 4
---

Using ProGet’s Feed Importer feature, migrating a Sonatype Nexus repository to ProGet takes just minutes. This feature enables you to download packages from a remote feed directly into a local feed. 

In this article, you’ll learn how to use ProGet’s Feed Importer feature to move packages from your Sonatype Nexus repositories to ProGet. 

## Migrating Packages from Sonatype Nexus to ProGet
Package migration is fast and straightforward, requiring only a few clicks and your Nexus URL and credentials.

### Step 1: Create a New Feed
First, let’s set up a new feed where your packages will be imported. Go to “Feeds” and choose “Create New Feed” to begin.

![Create New Feed](/resources/docs/proget-feeds-createnewfeed.png){height="" width="50%"}

Next, choose the package type. For this example, we'll select “NuGet (.NET) Packages”.

![Create NuGet Feed](/resources/docs/proget-newfeed-nugetselect.png){height="" width="50%"}

Now, select the appropriate feed type. In this case, we'll choose “Private/Internal NuGet (.NET) Packages”.

![Create Private Feed](/resources/docs/proget-createfeed-privatefeed.png){height="" width="50%"}

Then, enter a name for your feed. For this example, we’ll use “internal-nuget”.

![Name Feed](/resources/docs/proget-createfeed-name.png){height="" width="50%"}

### Step 2: Connect to Sonatype Nexus
With the feed created, it’s time to connect to your Sonatype Nexus repository. In the `internal-nuget` feed, click the dropdown and select “Import Packages”.

![Sonatype Import](/resources/docs/proget-importpackages.png){height="" width="50%"}

Because we’re connecting to a remote feed, choose “Download Package From Another Service”.

![Sonatype Connect](/resources/docs/proget-downloadpackage-azure.png){height="" width="50%"}

Then, choose “Sonatype Nexus” to have ProGet configured to migrate your packages from the chosen service.

![Sonatype](/resources/docs/proget-connectfeed-migrate-sonatype.png){height="" width="50%"}

### Step 3: Migrate Your Packages
Now, enter your Nexus URL along with your credentials. 

![Sonatype Migrate](/resources/docs/proget-migrate-sonatype.png){height="" width="50%"}

Next, choose the repository containing your NuGet packages. ProGet automatically filters to make finding your NuGet repository easier. For this example, our repository is named `test-nuget`. Then click “Confirm Import”.

![Migrate](/resources/docs/proget-migrate-sonatype-feed.png){height="" width="50%"}

To finish, select “Begin Import.” ProGet will migrate all your packages from Sonatype Nexus. You can then go to “Feeds” and check the `internal-nuget` feed to view all migrated packages.

![Migrated](/resources/docs/proget-nugetfeed-fakepackages.png){height="" width="50%"}