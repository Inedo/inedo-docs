---
title: "HOWTO: Migrate from MyGet to ProGet"
order: 1
---

Migrating your existing MyGet feeds to ProGet is a straightforward process. By bulk downloading your packages from your feeds via MyGet, or with a simple PowerShell script, you can then import them into your ProGet feeds. This article will walk through how to use the Bulk Package Import feature to migrate a MyGet feed to ProGet. 

## Step 1: Download Your MyGet Packages

All supported package types in MyGet are also supported in ProGet. Your MyGet feed packages can be downloaded via "Manage Feeds" > "Packages" > "Download".

![MyGet Feed Download](/resources/docs/myget-migration-download.png){height="" width="50%"}

MyGet allows you to have multiple package types per feed and comes with unique URLs for each package type.

![MyGet Feed URLs](/resources/docs/myget-migration-url.png){height="" width="50%"}

:::(Info) (User Access)
Users in MyGet only need read-only access to view the package type URL and authentication information.
:::

However, ProGet requires a different feed for each package type, so we recommend using the URL for each package type in your MyGet feed, in comnination with a simple PowerShell script to download your packages by feed and type. For demonstration purposes we will be migrating a NuGet feed to ProGet:

```powershell
$nugetPath = <Path to nuget.exe>
$feeds = @(<Feed One Name>, <Feed Two Name>, <Feed Three Name>)
$outputRoot = <Choose Output Folder Location>
New-Item -ItemType Directory -Path $outputRoot -Force | Out-Null
foreach ($feedURL in $feeds) {
    Write-Host "Fetching packages from: $feedURL"
    $packages = & $nugetPath list -Source "https://www.myget.org/F/$feedURL/api/v2" -NonInteractive | ForEach-Object {
        ($_ -split ' ')[0]
    }
    foreach ($packageID in $packages) {
        Write-Host "Downloading $packageID..."
        & $nugetPath install $packageID -Source "https://www.myget.org/F/$feedURL/api/v2" -OutputDirectory "outputRoot\$feedURL\$packageID" -NonInteractive -DependencyVersion Ignore -Prerelease
    }
}
```

## Step 2: Create a Feed in ProGet

To create a new feed, navigate to the banner at the top of the page and click on feeds. Next select Create New Feed.

![Create New Feed](/resources/docs/proget-feeds-createnewfeed.png){height="" width="50%"}
    
You will then need to select the type of feed you would like to setup. In this guide we will be setting up a NuGet feed for a fictious company called Kramerica, but the steps should be nearly the same for any feed. Select the feed you wish to setup and fill in the relevant fields.

![New NuGet Feed](/resources/docs/proget-nuget-newfeed.png){height="" width="50%"}

## Step 3: Bulk Import Packages to ProGet

Now that your feed is created, you will need to import your downloaded MyGet packages.

In your feed, navigate to "Import Packages".

![Select Import Packages](proget-nuget-importpackagesselect.png){height="" width="50%"}

Since you packages are downloaded, select "Import/Copy Package Files on Disk".

![Import From Disk](proget-feeds-importpackagesfromdisk.png){height="" width="50%"}

Next, specify the source of your packages, and select "Import".

![Bulk Import](proget-nuget-bulkpackageimport.png){height="" width="50%"}

Navigating back to the feed, we can see all packages have been uploaded:

![Imported Packages](proget-nuget-bulkimportedfeed.png){height="" width="50%"}