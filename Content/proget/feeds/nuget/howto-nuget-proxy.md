---
title: "HOWTO: Proxy Packages from NuGet.org in Visual Studio or CLI"
order: 6
---

Connecting ProGet with Visual Studio or integrating it into `dotnet` is a great way to increase the security of the most widely used integrated development environment for Windows developers. ProGet acts as a NuGet Package Manager, as it can [detect package licenses](https://docs.inedo.com/docs/proget/sca/licenses), [scan packages for vulnerabilities](/docs/proget/sca/vulnerabilities), and control promotion between feeds.

ProGet can create a ["Feed"](/docs/proget/feeds/feed-overview) to proxy NuGet packages from [NuGet.org](https://www.nuget.org/) so that teams can consume them in their projects just as they would if pulling directly from the OSS repository. Using this feed will let you cache packages, which is useful when Nuget.org is experiencing issues. It also lets you easily see which packages are being downloaded and used frequently.

In this article, we'll explain how to proxy NuGet packages from [NuGet.org](https://www.nuget.org/) to a feed in ProGet, and then connect it to Visual Studio or other clients such as the NuGet CLI, [VS Code](https://code.visualstudio.com/), and [JetBrains Rider](https://www.jetbrains.com/rider/) and set up authentication. We’ll also look at how to set up a private repository for using internal NuGet packages, as well as how to establish a package approval process to manage which packages your team can utilize in production.

:::(Info) (📺 2-minute Video:  Proxying Packages from NuGet.org in Visual Studio or CLI with ProGet)
<iframe width="600" height="337" src="https://www.youtube.com/embed/nF0MO1xZ4fY?si=WlJ070vc4dKOkY_L" frameborder="0" allowfullscreen="true"></iframe>
:::

## Step 1: Create and Name a NuGet Feed

We'll first create a NuGet feed to proxy packages from [NuGet.org](https://www.nuget.org/). You can read more on creating feeds by reading [Creating and Managing Feeds](/docs/proget/feeds/feed-overview#creating-and-managing-feeds)

In your ProGet instance, select "Feeds" and "Create New Feed", and select "NuGet (.NET) Packages".

![Create Feed](/resources/docs/proget-feeds-nugetselect.png){height="" width="50%"}

Then select "Connect to NuGet.org" which will allow us to proxy and cache packages from NuGet.org.

![Choose Connector](/resources/docs/proget-nuget-connecttoorg.png){height="" width="50%"}

Select "No, Create One Feed", as we will only need a single feed to proxy NuGet packages. Now you will need to name the feed. For this example, we will name it `public-nuget`, and then click "Create Feed".

![Name Feed](/resources/docs/proget-nuget-nameonefeed.png){height="" width="50%"}

You'll then choose from several vulnerability and license options. More information on these can be found in the [Vulnerability Scanning and Blocking](/docs/proget/sca/vulnerabilities) and [Licensing Detection and Blocking](https://docs.inedo.com/docs/proget/sca/licenses) documentation. Select "Set Feed Features". ProGet will create The `public-nuget` feed, which will now be populated with packages proxied from NuGet.org.

![Public Feed](/resources/docs/proget-nuget-publicfeed.png){height="" width="50%"}

## Step 2: Adding ProGet As A Source  { #add-source }

To add your `public-nuget` feed as a source, you will need the feed URL. This is found on the top right of the feed page:

![Feed URL](/resources/docs/proget-nuget-public-url.png){height="" width="50%"}

To add your feed as a Package Manager in Visual Studio, navigate to "Tools" > "NuGet Package Manager" > "Package Manager Settings" > "Package Sources". Then uncheck the box to the left of *nuget.org*

![Disable NuGet Source](/resources/docs/visualstudio-disablenuget.png){height="" width="50%"}

This prevents Visual Studio from scanning both NuGet.org and ProGet for packages. If you configure Visual Studio to search only ProGet instead of NuGet.org and ProGet, you avoid problems such as bad licenses, vulnerable packages, and [dependency confusion](https://blog.inedo.com/software-supply-chain-security/three-things) in your packages when you use multiple sources.

Now you will need to create a new package source. Click the green `+` in the top right of the window, and then name the new package source. Then paste in your `public-nuget` feed URL. Now, click the "Update" button, followed by the "OK" button.

![Configure ProGet Source](/resources/docs/visualstudio-configureresource.png){height="" width="50%"}

Visual Studio and ProGet are now connected.

:::(Warning) (Be sure to click "Update" prior to clicking "OK")
If you click "OK" without clicking "Update" your package source configuration will not be saved in Visual Studio.
:::

To confirm the connection in Visual Studio, right-click on a project in the Solution Explorer and select “Manage NuGet Packages…” from the menu. In the Package Manager window under "Browse", your should see a window populated with packages from the `public-nuget` feed.

![Connector](/resources/docs/visualstudio-connectedprogetfeed.png){height="" width="50%"}

### Adding the Feed to Other NuGet Clients { #add-others }

ProGet can be added as a source in a number of other popular clients, including the NuGet CLI, [VS Code](https://code.visualstudio.com/), and [JetBrains Rider](https://www.jetbrains.com/rider/). To learn how to do this, read [Integrating NuGet Client Tools with ProGet](/docs/proget/feeds/nuget/integrate-nuget).

## Step 3: Installing NuGet Packages

Once you have created your `public-nuget` feed and configured it with either Visual Studio or CLI, you can install packages. For Visual Studio you can do this from the "NuGet Package Manager" by navigating to "Tools" > "NuGet Package Manager" > "Manage NuGet Packages for Solution".

Alternatively, you can use the `dotnet nuget install` command in the NuGet CLI:

```bash
$ dotnet nuget install «package-name» -Source public-nuget
```

## Step 4: (Optional) Authenticating to Your NuGet Feed

By default your `public-nuget` feed does not need to be authenticated to. However, you may want to make your repository private and authenticate to it, in which case you'll need to configure an *authenticated* source when adding the feed. 

::: (Info) (💡 Best Practices: Use API Keys for Authenticated Feeds)
Instead of using your ProGet username/password for a NuGet feed, we recommend [Creating a ProGet API Key](/docs/proget/api/apikeys) to authenticate. You can enter `api` as the username and your key as the password.
:::

To learn how to authenticate to your NuGet feed, read [Authenticating to NuGet Feeds](/docs/proget/feeds/feed-overview##authenticating-to-feed)

## (Optional) Creating a Package Approval Flow

In this guide, we covered how to proxy packages from [NuGet.org](https://www.nuget.org/). However, this lets developers to use any OSS package from this OSS repository without oversight. In organizations, it's [recommended that you include some form of approval](https://blog.inedo.com/nuget/nuget-package-approval-workflow/) to make sure only compliant packages are used in production. 

This can be achieved by introducing a ["Package Approval Flow"](/docs/proget/packages/package-promotion). To set up a package approval flow, refer to [HOWTO: Approve and Promote Open-source Packages](/docs/proget/packages/package-promotion/proget-howto-promote-packages). 

After creating your "Unapproved" and "Approved" feeds, add your "Approved" feed to either [Visual Studio](#add-visual-studio) or [CLI](#add-cli) to add the "Approved" feed (e.g. `approved-nuget`) as a custom repository.

## Troubleshooting

### Authentication Error

An error may occur when trying to browse the authenticated NuGet feed in Visual Studio.

![Connection Error](/resources/docs/visualstudio-nugetfeed-error.png)

The window does not fill with packages and the error list says "API Key ... does not exist"

There may have been an error entering the personal API key while connecting to the server, or the API key may have been deleted in ProGet.

**To resolve**, in Visual Studio, close all your instances.

Then, in Windows, open Control Panel and navigate to Credential Manager.

![Credentials Search](/resources/docs/windows-credentialmanager-search.png)

Under Windows Credentials, delete the one named as the ProGet host (in this demonstration, *3.144.230.132*)

![Windows Credentials](/resources/docs/windows-credentialmanager-windowscredentials.png)

Under Generic Credentials, find and remove the one named “VSCredentials_<progethost>” (in this demonstration, *VSCredentials_3.144.230.132*)

![Generic Credentials](/resources/docs/windows-credentialmanager-genericcredentials.png)

Now, In Visual Studio open your instance and navigate to the Package Manager again as in Step 6. 

You will be prompted again to enter a Personal API key, as in Step C. 

![Enter API Key](/resources/docs/visualstudio-apikey.png)

Repeat Step C to reconnect to the authenticated NuGet feed.