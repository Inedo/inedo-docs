---
title: "HOWTO: Migrate from MyGet to ProGet"
order: 1
---

Migrating your existing MyGet feeds to ProGet is a straightforward process. By bulk downloading your packages from your feeds via MyGet you can then import them into your ProGet feeds. This article will walk through how to use the Bulk Package Import feature to migrate a MyGet feed to ProGet.

## Step 1: Download Your MyGet Packages

MyGet allows you to have multiple package types per feed and comes with unique URLs for each package type. However, ProGet requires a different feed for each package type, so you will need to download your packages for each feed type separately.

Your MyGet packages can be easily downloaded by navigating to your MyGet feed and selecting "Download".

![MyGet Download](/resources/docs/myget-migration-download.png){height="" width="50%"}

This option will provide you with a `.zip` file which will automatically organize your packages by type, if your MyGet feed contains multiple package types.

![Downloaded Packages](/resources/docs/myget-migration-packages.png){height="" width="50%"}

An alternative option is to download your MyGet packages by package type using an automated script. This option will require using your MyGet feed URLs.

Each package type in your MyGet feeds will require a different feed URL to be downloaded, available in the Feed Details tab.

![MyGet Feed URL](/resources/docs/myget-migration-url.png){height="" width="50%"}

Here is a list of simple PowerShell scripts for downloading common MyGet package types:

**NuGet**

```powershell
$nugetPath = "<Path to nuget.exe>"
$feeds = @("<Feed One Name>, <Feed Two Name>, <Feed Three Name>")
$outputRoot = "<Choose Output Folder Location>"
New-Item -ItemType Directory -Path $outputRoot -Force | Out-Null
foreach ($feedURL in $feeds) {
    Write-Host "Fetching packages from: $feedURL"
    $packages = & $nugetPath list -Source "https://www.myget.org/F/$feedURL/api/v2" -NonInteractive | ForEach-Object {
        ($_ -split ' ')[0]
    }
    foreach ($packageID in $packages) {
        Write-Host "Downloading $packageID..."
        & $nugetPath install $packageID -Source "https://www.myget.org/F/$feedURL/api/v2" -OutputDirectory "$outputRoot\$feedURL\$packageID" -NonInteractive -DependencyVersion Ignore -Prerelease
    }
}
```

**npm**

```powershell
$packages = @("<Packge One Name>", "<Package Two Name>")
$feed_url = "<Feed URL>"
$outputRoot = "<Choose Output Folder Location>"
New-Item -ItemType Directory -Path $outputRoot -Force | Out-Null
foreach ($package in $packages) {
    Write-Host "Packing $package from $feed_url"
    Set-Location $outputRoot
    cmd /c npm pack $package --registry $feed_url | ForEach-Object {
        Write-Host "Downloaded: $_"
    }
}
```

**RubyGems**

```powershell
$gemExe = "gem"
$gems = @("<Packge One Name>", "<Package Two Name>")
$feed_url = "<Feed URL>"
$outputRoot = "<Choose Output Folder Location>"
New-Item -ItemType Directory -Path $outputRoot -Force | Out-Null
foreach ($gemName in $gems) {
    Write-Host "Fetching Gem: $gemName"
    Set-Location $outputRoot
    & $gemExe fetch $gemName --source $feed_url
    if ($LASTEXITCODE -ne 0) {
        Write-Warning "Failed to fetch $gemName"
    } else {
        Write-Host "$gemName downloaded successfully."
    }
}
```

**PyPi**

```powershell
$pipExe = "pip"
$packages = @("<Packge One Name>", "<Package Two Name>")
$feed_url = "https://www.myget.org/F/<Feed Name>/simple/"
$outputRoot = "<Choose Output Folder Location>"
New-Item -ItemType Directory -Path $outputRoot -Force | Out-Null
foreach ($pkg in $packages) {
    Write-Host "Downloading: $pkg from $feed_url"
    & $pipExe download $pkg --no-deps --index-url $feed_url --dest $outputRoot    
    if ($LASTEXITCODE -ne 0) {
        Write-Warning "Failed to download $pkg"
    } else {
        Write-Host "$pkg downloaded successfully."
    }
}
```

## Step 2: Create a Feed in ProGet

ProGet supports the creation of multiple feed types, but for demonstration purposes we will be migrating a NuGet feed from from MyGet to ProGet. 

To create a new feed, navigate to the banner at the top of the page and click on "Feeds". Next select "New Feed".

![Create Feed](/resources/docs/proget-feeds-createnewfeed.png){height="" width="50%"}
    
Then select the package type. In this case, we will select "NuGet (.NET) Packages".

![Create NuGet Feed](/resources/docs/proget-newfeed-nugetselect.png){height="" width="50%"}

As we will be uploading packages from our MyGet feed, here we will select "Private/Internal NuGet (.NET) Packages".

![Create Private Feed](/resources/docs/proget-createfeed-privatefeed.png){height="" width="50%"}

Now name your feed. We will call ours `internal-nuget`.

![Name Feed](/resources/docs/proget-createfeed-name.png){height="" width="50%"}

You'll be taken to your new NuGet feed, currently empty.

![Empty NuGet Feed](/resources/docs/proget-nuget-internal-empty.png){height="" width="50%"}

## Step 3: Import Packages to ProGet

Now we have created a NuGet feed, we can import our MyGet packages. From the "View Packages" tab, navigate to the dropdown menu and select "Import Packages".

![Import Packages](/resources/docs/proget-importpackages.png){height="" width="50%"}

To upload your packages, select "Import/Copy Package Files on Disk".

![Import From Disk](/resources/docs/proget-feeds-importpackagesfromdisk.png){height="" width="50%"}

Next, enter the file path of the downloaded packages from your MyGet feed and select import.

![Disk Path](/resources/docs/proget-feeds-diskpath.png){height="" width="50%"}

The `internal-nuget` you created is now populated with your imported MyGet packages.

![Imported Packages](/resources/docs/proget-nugetfeed-fakepackages.png){height="" width="50%"}