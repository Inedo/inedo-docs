---
title: "HOWTO: Proxy Packages from NuGet.org in Visual Studio or CLI"
order: 1
---

Connecting ProGet with Visual Studio or integrating it into `dotnet` is a great way to increase the security of the most widely used integrated development environment for Windows developers. ProGet acts as a NuGet Package Manager, as it can [detect package licenses](https://docs.inedo.com/docs/proget/sca/licenses), [scan packages for vulnerabilities](/docs/proget/sca/vulnerabilities), and control promotion between feeds.

ProGet can create a ["Feed"](/docs/proget/feeds/feed-overview) to proxy NuGet packages from [NuGet.org](https://www.nuget.org/) so that teams can consume them in their projects just as they would if pulling directly from the OSS repository. Using this feed will let you cache packages, which is useful when Nuget.org is experiencing issues. It also lets you easily see which packages are being downloaded and used frequently.

In this article, we'll explain how to proxy NuGet packages from [NuGet.org](https://www.nuget.org/) to a feed in ProGet, and then connect it to Visual Studio or CLI and set up authentication. We’ll also look at how to set up a private repository for using internal NuGet packages, as well as how to establish a package approval process to manage which packages your team can utilize in production.

## Step 1: Create and Name a NuGet Feed

We'll first create a NuGet feed to proxy packages from [NuGet.org](https://www.nuget.org/). You can read more on creating feeds by reading [Creating and Managing Feeds](/docs/proget/feeds/feed-overview#creating-and-managing-feeds)

In your ProGet instance, select "Feeds" and "Create New Feed", and select "NuGet (.NET) Packages".

![](/resources/docs/proget-newfeed-nugetselect.png){height="" width="50%"}

Then select "Connect to NuGet.org" which will allow us to proxy and cache packages from NuGet.org.

![](/resources/docs/proget-nuget-connecttoorg.png){height="" width="50%"}

Select "No, Create One Feed", as we will only need a single feed to proxy NuGet packages. Now you will need to name the feed. For this example, we will name it `public-nuget`, and then click "Create Feed".

![](/resources/docs/proget-nuget-onefeedname.png){height="" width="50%"}

You'll then choose from several vulnerability and license options. More information on these can be found in the [Vulnerability Scanning and Blocking](/docs/proget/sca/vulnerabilities) and [Licensing Detection and Blocking](https://docs.inedo.com/docs/proget/sca/licenses) documentation. Select "Set Feed Features". ProGet will create The `public-nuget` feed, which will now be populated with packages proxied from NuGet.org.

![](/resources/docs/proget-publicnuget-feed.png){height="" width="50%"}

## Step 2: Adding ProGet As A Source  { #add-source }

To add your `public-nuget` feed to either Visual Studio or the CLI, you will need the feed URL. This is found on the top right of the feed page:

![](/resources/docs/proget-nuget-public-url){height="" width="50%"}

### Adding ProGet to Visual Studio
To add your feed as a Package Manager in Visual Studio, navigate to "Tools" > "NuGet Package Manager" > "Package Manager Settings". Then uncheck the box to the left of *nuget.org*

![visualstudio-packagesources-highlightednugetorg.png](/resources/docs/visualstudio-packagesources-highlightednugetorg.png)

This prevents Visual Studio from scanning both NuGet.org and ProGet for packages. If you configure Visual Studio to search only ProGet instead of NuGet.org and ProGet, you avoid problems such as bad licenses, vulnerable packages, and [dependency confusion](https://blog.inedo.com/software-supply-chain-security/three-things) in your packages when you use multiple sources.

Now you will need to create a new package source. Click the green `+` in the top right of the window, and then name the new package source. Then paste in your `public-nuget` feed URL.

![visualstudio-packagesources-configureproget.png](/resources/docs/visualstudio-packagesources-configureproget.png)

Now, click the "Update" button, followed by the "OK" button.

![visualstudio-packagesources-updateandok.png](/resources/docs/visualstudio-packagesources-updateandok.png)

Visual Studio and ProGet are now connected.

:::(Warning) (Be sure to click "Update" prior to clicking "OK")
If you click "OK" without clicking "Update" your package source configuration will not be saved in Visual Studio.
:::

To confirm the connection in Visual Studio, right-click on a project in the Solution Explorer and select “Manage NuGet Packages…” from the menu. In the Package Manager window under "Browse", your should see a window populated with packages from the `public-nuget` feed.

![visualstudio-connectedprogetfeed.png](/resources/docs/visualstudio-connectedprogetfeed.png)

### Adding the Feed to The NuGet CLI { #add-cli }

To add the feed as a source to your NuGet client, use the `dotnet nuget add source` command:

```bash
$ dotnet nuget add source https://«proget-server»/nuget/public-nuget/v3/index.json --name public-nuget
```

By default, NuGet clients are configured with nuget.org as a source. We recommend removing this to exclusively use your `public-nuget` feed as a source:

```bash
$ dotnet nuget remove source nuget.org
```

You can confirm that your have configured your sources correctly by entering:

```bash
$ dotnet nuget sources list
```

### Adding the Feed to Other NuGet Clients { #add-others }

ProGet can be added as a source in a number of other popular clients, including [VS Code](https://code.visualstudio.com/) and [JetBrains Rider](https://www.jetbrains.com/rider/).

### In VS Code

To add your `public-nuget` feed as a source, add it to a `nuget.config` in your project. The config could look like this:

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <packageSources>
    <add key="public-nuget" value="https://«proget-server»/nuget/public-nuget/v3/index.json" />
  </packageSources>
</configuration>
```

### In JetBrains Rider

To add your `public-nuget` feed as a source, open "NuGet Settings" by navigating to "File" > "Settings", and then "Build, Execution, Deployment" > "NuGet". Next, under the "Package Sources" tab, click on the + (Add) button to create a new package source.

In the Name field, enter a name for your source (e.g., `internal-nuget`), and then in the URL field, enter the URL of your `internal-nuget` feed. 

## Step 3: Installing NuGet Packages

Once you have created your `public-nuget` feed and configured it with either Visual Studio or CLI, you can install packages. For Visual Studio you can do this from the "NuGet Package Manager" by navigating to "Tools" > "NuGet Package Manager" > "Manage NuGet Packages for Solution".

In NuGet CLI you can use the `dotnet nuget install` command:

```bash
$ dotnet nuget install «package-name» -Source public-nuget
```

## Step 4: (Optional) Authenticating to Your NuGet Feed

By default your `public-nuget` feed does not need to be authenticated to, and can be viewed anonymously. However, you may want to make your repository private and [authenticate to it](/docs/proget/feeds/nuget#authenticating-to-nuget-feeds) using either an API key or a username/password combination. You may need to do this when pushing internal NuGet packages to your feed, to consume either solely or in combination with OSS packages. 

## (Optional) Creating a Package Approval Flow

In this guide, we covered how to proxy packages from [NuGet.org](https://www.nuget.org/). However, this lets developers to use any OSS package from this OSS repository without oversight. In organizations, it's recommended that you include some form of approval to make sure only compliant packages are used in production. 

This can be achieved by introducing a ["Package Approval Flow"](/docs/proget/packages/package-promotion). To set up a package approval flow, refer to [HOWTO: Approve and Promote Open-source Packages](/docs/proget/packages/package-promotion/proget-howto-promote-packages). 

After creating your "Unapproved" and "Approved" feeds, add your "Approved" feed to either [Visual Studio](#add-visual-studio) or [CLI](#add-cli) to add the "Approved" feed (e.g. `approved-nuget`) as a custom repository.

## Troubleshooting

### Authentication Error

An error may occur when trying to browse the authenticated NuGet feed in Visual Studio.

![visualstudio-nugetfeed-error.png](/resources/docs/visualstudio-nugetfeed-error.png)

The window does not fill with packages and the error list says "API Key ... does not exist"

There may have been an error entering the personal API key while connecting to the server, or the API key may have been deleted in ProGet.

**To resolve**, in Visual Studio, close all your instances.

Then, in Windows, open Control Panel and navigate to Credential Manager.

![windows-credentialmanager.png](/resources/docs/windows-credentialmanager.png)

Under Windows Credentials, delete the one named as the ProGet host (in this demonstration, *3.144.230.132*)

![windows-credentialmanager-windowscredentials.png](/resources/docs/windows-credentialmanager-windowscredentials.png)

Under Generic Credentials, find and remove the one named “VSCredentials_<progethost>” (in this demonstration, *VSCredentials_3.144.230.132*)

![windows-credentialmanager-genericcredentials.png](/resources/docs/windows-credentialmanager-genericcredentials.png)

Now, In Visual Studio open your instance and navigate to the Package Manager again as in Step 6. 

You will be prompted again to enter a Personal API key, as in Step C. 

![visualstudio-connecttoserver.png](/resources/docs/visualstudio-connecttoserver.png)

Repeat Step C to reconnect to the authenticated NuGet feed. 