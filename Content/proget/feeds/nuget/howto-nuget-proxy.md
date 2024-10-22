---
title: "HOWTO: Proxy Packages from NuGet.org in Visual Studio or CLI"
order: 5
---

Connecting ProGet with Visual Studio or CLI is a great way to increase the security of the most widely used integrated development environment for Windows developers. ProGet acts as a NuGet Package Manager, as it can [detect package licenses](https://docs.inedo.com/docs/proget/sca/licenses), [scan packages for vulnerabilities](/docs/proget/sca/vulnerabilities), and control promotion between feeds.

ProGet can create a ["Feed"](/docs/proget/feeds/feed-overview) to proxy NuGet packages from [NuGet.org](https://www.nuget.org/) so that teams can consume them in their projects just as they would if pulling directly from the OSS repository. Using this feed will let you cache packages, which is useful when Nuget.org is experiencing issues. It also lets you easily see which packages are being downloaded and used frequently.

In this article, we'll explain how to proxy NuGet packages from [NuGet.org](https://www.nuget.org/) to a feed in ProGet, and then connect it to Visual Studio or CLI and set up authentication. We’ll also look at how to set up a private repository for using internal NuGet packages, as well as how to establish a package approval process to manage which packages your team can utilize in production.

## Step 1: Create and Name a NuGet Feed

We'll first create a NuGet feed to proxy packages from [NuGet.org](https://www.nuget.org/). You can read more on creating feeds by reading [Creating and Managing Feeds](/docs/proget/feeds/feed-overview#creating-and-managing-feeds)

In your ProGet instance, select "Feeds" and "Create New Feed", and select "NuGet (.NET) Packages".

![](/resources/docs/proget-newfeed-nugetselect.png){height="" width="50%"}

Then select "Connect to NuGet.org" which will allow us to proxy and cache packages from NuGet.org.

![](/resources/docs/proget-nuget-connecttoorg.png){height="" width="50%"}

Select "No, Create One Feed", as we will only need a single feed to proxy NuGet packages. 

## Step 2: Name your Feed

Now you will need to name the feed. For this example, we will name it `public-nuget`, and then click "Create Feed".

![](/resources/docs/proget-nuget-onefeedname.png){height="" width="50%"}

You'll then choose from several vulnerability and license options. More information on these can be found in the [Vulnerability Scanning and Blocking](/docs/proget/sca/vulnerabilities) and [Licensing Detection and Blocking](https://docs.inedo.com/docs/proget/sca/licenses) documentation. Select "Set Feed Features". ProGet will create The `public-nuget` feed, which will now be populated with packages proxied from NuGet.org.

![](/resources/docs/proget-publicnuget-feed.png){height="" width="50%"}

To add your `public-nuget` feed to either Visual Studio or the CLI, you will need the feed URL. This is found on the top right of the feed page:

![](/resources/docs/proget-nuget-public-url){height="" width="50%"}

## Step 3.2 Adding the Feed to Visual Studio { #add-visual-studio }

To add your feed as a source in Visual Studio, navigate to "Tools" > "NuGet Package Manager" > "Package Manager Settings". Then uncheck the box to the left of *nuget.org*

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


## Step 3.2: Adding the Feed to NuGet CLI { #add-cli }

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

### Installing NuGet Packages

Once you have created your `public-nuget` feed and configured it with either Visual Studio or CLI, you can install packages. For Visual 

```bash
$ dotnet nuget install «package-name» -Source public-nuget
```

## Step 45: (Optional) Authenticating to Your NuGet Feed

By default your `public-nuget` feed does not need to be authenticated to, and can be viewed anonymously. However, you may want to make your repository private and [authenticate to it](/docs/proget/feeds/nuget#authenticating-to-nuget-feeds) using either an API key or a username/password combination. You may need to do this when pushing internal NuGet packages to your feed, to consume either solely or in combination with OSS packages. 

## (Optional) Creating a Package Approval Flow

In this guide, we covered how to proxy packages from [NuGet.org](https://www.nuget.org/). However, this lets developers to use any OSS package from this OSS repository without oversight. In organizations, it's recommended that you include some form of approval to make sure only compliant packages are used in production. 

This can be achieved by introducing a ["Package Approval Flow"](/docs/proget/packages/package-promotion). To set up a package approval flow, refer to [HOWTO: Approve and Promote Open-source Packages](/docs/proget/packages/package-promotion/proget-howto-promote-packages). 

After creating your "Unapproved" and "Approved" feeds, add your "Approved" feed to either [Visual Studio](#add-visual-studio) or [CLI](#add-cli) to add the "Approved" feed (e.g. `approved-nuget`) as a custom repository.
