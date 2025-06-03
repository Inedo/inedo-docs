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

### Optional: Bulk NuGet Package Download (PowerShell)
As ProGet requires a different feed for each package type, we recommend a simple PowerShell script to download your NuGet packages to local directory, separated by feed name into separate folders. This will also make it easier to bulk upload by feed later. You'll need to specify the path of `nuget.exe`, as well as the names of your MyGet feeds.

```powershell
$nugetPath = C:\Program Files\Nuget\nuget.exe # specify the path to nuget.exe
$feeds = @(public-nuget, nuget-unapproved, nuget-approved)  # enter your feed names here
$outputRoot = C:\nuget-packages\ # specify the path to download your NuGet packages to

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
    
You will then need to select the type of feed you would like to setup. In this guide, we will be setting up a NuGet feed for a fictitious company called Kramerica, but the steps should be nearly the same for any feed. Select the feed you wish to set up and fill in the relevant fields.

![New NuGet Feed](/resources/docs/proget-nuget-newfeed.png){height="" width="50%"}

## Step 3: Bulk Import Packages to ProGet

Now that your feed is created, you will need to import your downloaded MyGet packages.

In your feed, navigate to "Import Packages".

![Select Import Packages](/resources/docs/proget-nuget-importpackagesselect.png){height="" width="50%"}

Then, select "Import/Copy Package Files on Disk".

![Import From Disk](/resources/docs/proget-feeds-importpackagesfromdisk.png){height="" width="50%"}

Next, specify the source of your packages, and select "Import".

![Bulk Import](/resources/docs/proget-nuget-bulkpackageimport.png){height="" width="50%"}

Navigating back to the feed, we can see all packages have been uploaded:

![Imported Packages](/resources/docs/proget-nuget-bulkimportedfeed.png){height="" width="50%"}