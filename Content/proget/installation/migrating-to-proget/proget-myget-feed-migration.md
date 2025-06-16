---
title: "HOWTO: Migrate from MyGet to ProGet"
order: 1
---

Migrating your existing MyGet feeds to ProGet is a straightforward and simple process. By using ProGet’s Feed Importer feature, packages from a remote feed can be downloaded to a local feed. This article will walk through how to use the Feed Importer feature to migrate a MyGet feed to ProGet. 

## Step 1: Prepare MyGet for Migration and Gather Information 
With the exception of PHP composer packages, all other supported package types in MyGet are also supported in ProGet. MyGet allows you to have multiple package types per feed and comes with unique URLs for each package type. However, ProGet requires a different feed for each package type, so you will need the URL for each type of package in your MyGet feed. 

The MyGet feed URL can be found under Feed Name>Feed Details>Package Type. In the screenshot below, a feed with NuGet packages is being migrated so we need to copy the NuGet feed URL. If you are migrating a feed with a different package type, such as Npm or Maven, then you will need to navigate to that package type and copy its feed URL. 

![MyGet Feed URL](/resources/docs/mygetfeedmigration-nugetfeedurl.jpg){height="" width="50%"} 

:::(Info) (User Access)
Users in MyGet only need read-only access to view the feed URL and authentication information.
:::

Now that you have gathered the feed URLs for each package type you wish to migrate, any active upstream sources will also need to be disabled.

To disable upstream sources in MyGet navigate to Feed> Upstream Sources and delete any active upstream sources.

![MyGet Upstream Sources](/resources/docs/mygetfeedmigration-upstreamsources.jpg){height="" width="50%"}


## Step 2: Create a Feed in ProGet

ProGet supports the creation of multiple types of feeds, but for demonstration purposes we will be migrating a feed with NuGet packages from MyGet to ProGet. 

To create a new feed, navigate to the banner at the top of the page and click on feeds. Next select Create New Feed.

![Create a New Feed in ProGet](/resources/docs/amazons3-createfeed%281%29.jpg){height="" width="50%"}
    
You will then need to select the type of feed you would like to setup. In this guide we will be setting up a NuGet feed for a fictious company called Kramerica, but the steps should be nearly the same for any feed. Select the feed you wish to setup and fill in the relevant fields.

![NuGet Feed Creation](/resources/docs/proget-nuget-newfeed.png){height="" width="50%"}

## Step 3: Connect ProGet Feed to MyGet Feed
Now that your feed is created, a connector to your imported feed will need to be added. Click on add connector and fill in the relevant information.

![MyGet Feed Connector](/resources/docs/mygetfeedmigration-connector.jpg){height="" width="50%"}

If your connector is setup correctly you will see your packages shown as remote packages in your ProGet feed. If you do not see your packages at this point, skip ahead to the troubleshooting section.

![MyGet Imported Packages](/resources/docs/mygetfeedmigration-importedpackages.jpg){height="" width="50%"}

While your feed is connected you will still need to complete step 4 in order to import and have access to the packages.

## Step 4: Import packages to ProGet
Now that your imported MyGet feed is connected to ProGet you will need to import the packages.

Navigate to Feeds> Feed Name> Manage Feed> Connectors & Replication> Connectors Used By This Feed.

![Import Packages](/resources/docs/mygetfeedmigration-importfeedbutton.jpg){height="" width="50%"}

After clicking on the import button, you will have to fill in some information about your imported packages.

![Import Package Details](/resources/docs/mygetfeedmigration-importpackagesfromconnector.jpg){height="" width="50%"}

After clicking on Import, you will see a Live Action Log Output displaying the packages that have been imported. 

![Live Action Log Output](/resources/docs/mygetfeedmigration-execution.jpg){height="" width="50%"}

Your feed and its packages have successfully been imported!

## Step 5: Delete Connector from ProGet
With your feed fully imported, we recommend deleting the connector from ProGet. 

Navigate to Feeds> Connectors> Connector Name and click on the red x to delete the connector. 

![Delete Connector](/resources/docs/mygetfeedmigration-deleteconnector.jpg){height="" width="50%"}
 

## Step 6: Repeat steps 1-5 for each feed.
To migrate your various feeds from MyGet to ProGet simply repeat steps 1-5 for each feed until you’re fully migrated!  

## Troubleshooting
### Issue: Why don't I see all my packages imported?
![Import Package Details](/resources/docs/mygetfeedmigration-importfeed.jpg){height="" width="50%"}

If you run into this issue, ensure that your Maximum Package Count is not lower than the number of packages you are importing. 

### Issue: Why don't I have an import button?

If you have followed the steps in this guide and are not seeing the import button mentioned in step 4 than it is likely that you are either operating on an older version of ProGet or using a public repository. You must be operating on ProGet v6.0.7 or newer and using a private repository to import packages.  

### Issue: Why don't I see any packages after adding my connector?

This is likely because your MyGet feed requires credentials to be accessed. Your credentials can be added in the boxes for Username and Password when creating a connector in step 3. Be sure to use a MyGet username that has at least read-only access to the MyGet feed you wish to migrate. The password will be the same as the one used by the choosen user when logging into MyGet.
