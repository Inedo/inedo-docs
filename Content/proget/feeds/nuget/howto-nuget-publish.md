---
title: "HOWTO: Create and Upload Nuget Packages to a Private Repository in ProGet"
order: 6
---

ProGet let's you set up private NuGet repositories that will allow you to publish, store and share your internal NuGet packages within your organization.

This guide will walk you through the process of setting up a NuGet ["Feed"](/docs/proget/feeds/feed-overview) in ProGet as a private, custom repository. We'll also cover how to create, push, and install NuGet packages from this repository.

## Step 1: Create and Name a NuGet Feed

Start by creating a NuGet feed to host your internal packages. Navigate to "Feeds" and "Create New Feed". Then select "NuGet (.NET) Packages".

![](){height="" width="50%"}

Now select "No Connectors (Private packages only)" as this feed will be intended to host private packages.

![](){height="" width="50%"}

From here, we name our feed. For this example, we will call it `internal-nuget`, and then click "Create Feed".

![](/resources/docs/proget-cran-internalfeed.png){height="" width="50%"}

You'll then see several options related to ProGet's [Vulnerability Scanning and Blocking](/docs/proget/sca/vulnerabilities) features. These are only for users looking to use third-party OSS packages. Leave these boxes unchecked, and select "Set Feed Features". You will then be directed to the new `internal-proget` feed, currently empty.

![](/resources/docs/proget-cran-feed-empty.png){height="" width="50%"}

## Step 2: Create a NuGet Package

To create a NuGet package, create the project in `dotnet` CLI by entering:

```bash
$ dotnet new classlib -n MyPackage 
$ cd MyPackage 
```

In the project folder, create a `.nuspec` file, that will contain the package metadata. For example:

```xml
<?xml version="1.0"?> 
<package > 
  <metadata> 
    <id>MyPackage</id> 
    <version>1.0.0</version> 
    <authors>Your Name</authors> 
    <owners>Your Organization</owners> 
    <description>A simple example NuGet package.</description> 
    <language>en-US</language> 
    <tags>example</tags> 
  </metadata> 
</package> 
```

Then run the following to create the package: 

```bash
$ dotnet pack --configuration Release 
```

### Step 4: Adding the Feed to NuGet CLI { #add-feed }

To add your `internal-nuget` feed to NuGet CLI, you will need the feed URL. This is found on the top right of the feed page:

![](){height="" width="50%"}

Then add the feed as a source to your NuGet client by entering:

```bash
$ nuget add source https://«proget-server»/nuget/public-nuget/v3/index.json --name internal-nuget
```

### Step 5: Setting Up Authentication to Your NuGet Feed.

To push packages to a feed you will need to configure authentication using a "NuGet API Key". To authenticate you can either create an API Key in ProGet or use `«username:password»` as one. To learn how to do either, see [Authenticating to a NuGet Feed](/docs/proget/feeds/nuget/#authenticating-to-a-nuget-feed).

## Step 6: Pushing Your Package to Your NuGet Feed

To push your NuGet package to your `internal-nuget` feed, enter:

```bash
$ nuget push «feed-url» -Source internal-nuget
```

Or if you are using `«username:password»` as an API Key:

```bash
$ nuget push «feed-url» -Source internal-nuget -ApiKey «username:password»
```