---
title: "HOWTO: Migrate from Sonatype Nexus to ProGet"
order: 4
---

Migrating a Sonatype Nexus repository to ProGet can be done in a matter of minutes using ProGet’s Feed Importer feature. This feature allows packages from a remote feed to be downloaded to a local feed. 

This article will guide you through how to use the Feed Importer feature to migrate packages from your Sonatype Nexus repositories to ProGet. 

## Migrating Packages from Sonatype Nexus to ProGet
Migrating your packages can be done quickly and easily, in only a few clicks. All you will need is your Nexus URL and credentials.

### Step 1: Create a New Feed
To begin we'll create a new feed that we will import our existing packages to. Start by navigating to "Feeds" and selecting "Create New Feed".

![Create New Feed](/resources/docs/proget-feeds-createnewfeed.png){height="" width="50%"}

Then select the package type. In this case, we will select "NuGet (.NET) Packages".

![Create NuGet Feed](/resources/docs/proget-newfeed-nugetselect.png){height="" width="50%"}

Next, select the feed type for your NuGet packages. In this case, we'll select "Private/Internal NuGet (.NET) Packages".

![Create Private Feed](/resources/docs/proget-createfeed-privatefeed.png){height="" width="50%"}


Then name your feed. In this case, we will call it `internal-nuget`.

![Name Feed](/resources/docs/proget-createfeed-name.png){height="" width="50%"}

### Step 2: Migrate Your Packages
Now that we've created a feed, we're ready to migrate our packages. From the `internal-nuget` feed, navigate to "Import Packages" in the dropdown.

![Sonatype Import](/resources/docs/proget-importpackages.png){height="" width="50%"}

As we will be connecting to our Sonatype Nexus repository, select "Download Package From Another Service".

![Sonatype Connect](/resources/docs/proget-downloadpackage-azure.png){height="" width="50%"}

Now select "Sonatype Nexus", which will configure ProGet to migrate your packages from Sonatype Nexus.

![Sonatype](/resources/docs/proget-connectfeed-migrate-sonatype.png){height="" width="50%"}

Now enter your Nexus URL and credentials. 

![Sonatype Migrate](/resources/docs/proget-migrate-sonatype.png){height="" width="50%"}

Next, select the repository of your NuGet packages. ProGet will automatically filter your NuGet repository from others, making it easier to find and select. In this case, our repository name is `test-nuget`. Then select "Confirm Import".

![Migrate](/resources/docs/proget-migrate-sonatype-feed.png){height="" width="50%"}

Finally, select "Begin Import". ProGet will now migrate all of your Sonatype Nexus packages. Navigate to "Feeds" and the `internal-nuget` feed you created, where it will list all migrated packages.

![Migrated](/resources/docs/proget-nugetfeed-fakepackages.png){height="" width="50%"}