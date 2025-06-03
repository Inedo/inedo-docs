---
title: "HOWTO: Create and Upload Nuget Packages to a Private Repository in ProGet"
order: 7
---

ProGet let's you set up private NuGet repositories that will allow you to publish, store and share your internal NuGet packages within your organization.

This guide will walk you through the process of setting up a NuGet ["Feed"](/docs/proget/feeds/feed-overview) in ProGet as a private, custom repository. We'll also cover how to create, push, and install NuGet packages from this repository.

## Step 1: Create and Name a NuGet Feed { #create-feed }

Start by creating a NuGet feed to host your internal packages. Navigate to "Feeds" and "Create New Feed". Then select "NuGet (.NET) Packages".

![Create NuGet Feed](/resources/docs/proget-newfeed-nugetselect.png){height="" width="50%"}

Now select "No Connectors (Private packages only)" as this feed will be intended to host private packages.

![Private Packages](/resources/docs/proget-nuget-noconnectors.png){height="" width="50%"}

From here, we name our feed. For this example, we will call it `internal-nuget`, and then click "Create Feed".

![Name Feed](/resources/docs/proget-nuget-internal-name.png){height="" width="50%"}

You'll then see several options related to ProGet's [Vulnerability Scanning and Blocking](/docs/proget/sca/vulnerabilities) features. These are only for users looking to use third-party OSS packages. Leave these boxes unchecked, and select "Set Feed Features". You will then be directed to the new `internal-proget` feed, currently empty.

![Internal NuGet Feed](/resources/docs/proget-nuget-internal-empty.png){height="" width="50%"}

## Step 2: Create a NuGet Package

If you are using Visual Studio, you can create a NuGet package by following these steps:

1. Create a class library project in Visual Studio by selecting the Class Library template, choosing a suitable framework, then building the project to ensure it's set up correctly.
2. Configure the NuGet package properties by accessing project settings, ensuring the Package ID is unique and other details are filled out.
3. Use the Pack command in Release configuration to generate a `.nupkg` file, checking the Output window for its location

For more detail you can read [Microsoft's Official Documentation](https://learn.microsoft.com/en-us/nuget/quickstart/create-and-publish-a-package-using-visual-studio?tabs=netcore-cli).

In this guide we will cover creating a project in dotnet as it is easier to demonstrate, and also lets us cover the individual files that make up a NuGet package. To start, create the project in `dotnet` CLI by entering:

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

## Step 3: Adding the Feed as a Source { #add-feed }

To add your `internal-nuget` feed to Visual Studio, the NuGet CLI, or other clients such as [VS Code](https://code.visualstudio.com/) and [JetBrains Rider](https://www.jetbrains.com/rider/), you will need the feed URL. This is found on the top right of the feed page:

![Feed URL](/resources/docs/proget-nuget-internal-url.png){height="" width="50%"}

To add your feed as a Package Manager in Visual Studio, navigate to "Tools" > "NuGet Package Manager" > "Package Manager Settings". Then uncheck the box to the left of *nuget.org*

![Package Sources](/resources/docs/visualstudio-packagesources-highlightednugetorg.png)

This prevents Visual Studio from scanning both NuGet.org and ProGet for packages. If you configure Visual Studio to search only ProGet instead of NuGet.org and ProGet, you avoid problems such as bad licenses, vulnerable packages, and [dependency confusion](https://blog.inedo.com/software-supply-chain-security/three-things) in your packages when you use multiple sources.

Now you will need to create a new package source. Click the green `+` in the top right of the window, and then name the new package source. Then paste in your `internal-nuget` feed URL.

![Configure Sources](/resources/docs/visualstudio-packagesources-configureproget.png)

Now, click the "Update" button, followed by the "OK" button.

![Update Button](/resources/docs/visualstudio-packagesources-updateandok.png)

Visual Studio and ProGet are now connected.

:::(Warning) (Be sure to click "Update" prior to clicking "OK")
If you click "OK" without clicking "Update" your package source configuration will not be saved in Visual Studio.
:::

To confirm the connection in Visual Studio, right-click on a project in the Solution Explorer and select “Manage NuGet Packages…” from the menu. In the Package Manager window under "Browse", your should see a window populated with packages from the `internal-nuget` feed.

![Visual Studio Connection](/resources/docs/visualstudio-connectedprogetfeed.png)

### Using Other NuGet Clients { #add-others }

ProGet can be added as a source in a number of other popular clients, including the NuGet CLI, [VS Code](https://code.visualstudio.com/), and [JetBrains Rider](https://www.jetbrains.com/rider/). To learn how to do this, read [Integrating NuGet Client Tools with ProGet](/docs/proget/feeds/nuget/integrate-nuget).

## Step 4: Setting Up Authentication to Your NuGet Feed.

To push packages to a feed you will need to configure authentication using a "NuGet API Key". 

::: (Info) (💡 Best Practices: Use API Keys for Authenticated Feeds)
Instead of using your ProGet username/password for a NuGet feed, we recommend [Creating a ProGet API Key](/docs/proget/api/apikeys) to authenticate. You can enter `api` as the username and your key as the password.
:::

To learn how to authenticate to your NuGet feed to push packages to it, read [Authentication for Publishing Packages](/docs/proget/feeds/feed-overview#authenticate-cli)

## Step 5: Pushing Your Package to Your NuGet Feed

You can push your NuGet package to your NuGet feed in either Visual Studio or NuGet CLI. First make sure you have [added the feed as a source](#add-feed) with [authentication](/docs/proget/feeds/nuget#authenticating-to-nuget-feeds).

To push your NuGet package using Visual Studio, open the Package Manager Console by navigating to "Tools" > "NuGet Package Manager" > "Package Manager Console". Then. run the following Push command:

```bash
$ nuget push «path-to-package» -Source «source-name» -ApiKey api:«apikey»
```

:::(info) (Example:)
Pushing the package `MyPackage` to a configured source named `internal-nuget`, you would enter:

```bash
$ nuget push bin\Release\MyPackage.1.0.0.nupkg -Source internal-nuget -ApiKey api:abc12345
```
::::

Your package will then be uploaded and appear in your NuGet feed.

![Internal Feed Packages](/resources/docs/proget-nuget-internal-uploaded.png){height="" width="50%"}

### Pushing a Package with NuGet CLI

To push a NuGet package to ProGet in NuGet CLI, use the `dotnet nuget push` command:

```bash
$ dotnet nuget push «package-name» -Source «source-name»
```

## (Optional) Repackaging NuGet Packages in CI/CD

After publishing your NuGet packages, you might want to improve your CI/CD workflow by [repackaging](/docs/proget/packages/repackaging) pre-release versions into production-ready packages. This step allows you to create stable versions while maintaining an audit trail of the original pre-release versions, including details on who performed the operation and when. To learn more about repackaging your NuGet packages you can read [Repackaging NuGet Packages](/docs/proget/feeds/nuget#repackaging-packages)