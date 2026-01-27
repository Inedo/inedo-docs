---
title: "HOWTO: Migrate from Another Repository to ProGet"
order: 5
max-header-level: 3
---

Migrating a repository to ProGet can be done in a matter of minutes. By downloading your packages from other repositories you can then import them into your ProGet feeds.

:::(Info) (Using JFrog Artifactory, Azure DevOps Artifacts, Sonatype Nexus, or MyGet?)
ProGet has some extra features to help migrate from these services. See
* [Azure DevOps Artifacts Migration Guide](/docs/proget/installation/migrating-to-proget/proget-azure-devops-feed-migration)
* [Jfrog Artifactory Migration Guide](/docs/proget/installation/migrating-to-proget/proget-jfrog-artifactory-feed-migration)
* [Sonatype Nexus Migration Guide](/docs/proget/installation/migrating-to-proget/proget-sonatype-nexus-feed-migration)
* [MyGet Migration Guide](/docs/proget/installation/migrating-to-proget/proget-myget-feed-migration)
:::

This article will guide you through how to use the Bulk Package Import feature to migrate packages from other repositories to ProGet.

## Step 1: Download Packages from Another Repository

First you will need to download your packages from your other repository. The method to do this will vary from service to service. Please consult the relevant service documentation on how to do this.

## Step 2: Upload Packages in Bulk
From ProGet, navigate to the feed you want to upload your packages to. Here we will show how to bulk upload PyPi packages, although uploading other packages is conceptually the same. From here select "Import Packages".

![Add Package](/resources/docs/proget-python-addpackage.png){height="" width="50%"}

Then select "Import/Copy Package Files on Disk".

![Bulk Package Import](/resources/docs/proget-python-addpackage-bulkimport.png){height="" width="50%"}

Next, specify the source of your packages, and select "Import".

![Bulk Import](/resources/docs/proget-python-bulkimport.png){height="" width="50%"}

Navigating back to the feed, we can see all packages have been uploaded.

![Python Feed](/resources/docs/proget-python-feed.png){height="" width="50%"}

## Configuring a Connector
An alternative to bulk importing is to set up a connector to repository in ProGet. This will allow you to access "legacy" packages if needed by using tools to pull or download packages directly from ProGet, while primarily using the feed to store current versions of packages you are using.  

### Step 1: Obtain Repository Credentials
To create a connector to your repository in ProGet, you will need a Connector URL (Endpoint URL), username and password/API token.

The method to obtain these will vary from service to service. Please consult the relevant service documentation on how to do this.

### Step 2: Configure Your Connector in ProGet

In ProGet, navigate to the feed you want to configure a connector with. From here select "Connectors".

![Manage Python Feed](/resources/docs/proget-python-managefeed.png){height="" width="50%"}

From here select "add".

![Add Connector](/resources/docs/proget-python-connectors-add.png){height="" width="50%"}

Now enter your username, the Connector URL, and password/API token from [Step 1](#step-1-obtain-repository-credentials). Then, select "Save". 

![Add Connector Artifactory](/resources/docs/proget-createconnector-azure.png){height="" width="50%"}

You have now configured a connector, allowing tools to pull/download packages from ProGet directly.

![Connector Added](/resources/docs/proget-connector-created.png){height="" width="50%"}
