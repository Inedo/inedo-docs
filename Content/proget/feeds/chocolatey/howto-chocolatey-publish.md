---
title: "HOWTO: Create and Push Chocolatey Packages to a Private Repository in ProGet"
order: 2
---

ProGet can be configured as a private [Chocolatey](https://chocolatey.org) repository and host all your Chocolatey packages, allowing you to push, store and share then within your organization.

Hosting your Chocolatey packages in ProGet allows you to bypass [Chocolatey's rate-limiting policies](https://docs.chocolatey.org/en-us/community-repository/community-packages-disclaimer), especially if you enable [metadata caching](/docs/proget/feeds/connector-overview#connector-caching) and internalize your packages.

This guide will walk you through the process of setting up a Chocolatey ["Feed"](/docs/proget/feeds/feed-overview) in ProGet as a private, custom repository. We'll cover how to create, push, and install Chocolatey packages from this repository, as well as how to internalize packages to let your Chocolatey packages be used in air gapped or offline environments.

## Step 1: Create and Name a Chocolatey Feed { #create-feed }

Start by creating a Chocolatey feed to host your internal packages. Navigate to "Feeds" and "Create New Feed". Then select "Chocolatey Packages".

![](){height="" width="50%"}

Now select "No Connectors (Private packages only)" as this feed will be intended to host private packages.

![](){height="" width="50%"}

From here, we name our feed. For this example, we will call it `internal-Chocolatey`, and then click "Create Feed".

![](){height="" width="50%"}

You'll then see several options related to ProGet's [Vulnerability Scanning and Blocking](/docs/proget/sca/vulnerabilities) features. These are only for users looking to use third-party packages. Leave these boxes unchecked, and select "Set Feed Features". You will then be directed to the new `internal-chocolatey` feed, currently empty.

![](/resources/docs/proget-Chocolatey-internal-empty.png){height="" width="50%"}

## Step 2: Create a Chocolatey Package

If you are using Visual Studio, you can create a Chocolatey package by following these steps:

1. Create a class library project in Visual Studio by selecting the Class Library template, choosing a suitable framework, then building the project to ensure it's set up correctly.
2. Configure the Chocolatey package properties by accessing project settings, ensuring the Package ID is unique and other details are filled out.
3. Use the Pack command in Release configuration to generate a `.nupkg` file, checking the Output window for its location

For more detail you can read [Microsoft's Official Documentation](https://learn.microsoft.com/en-us/Chocolatey/quickstart/create-and-publish-a-package-using-visual-studio?tabs=netcore-cli).

In this guide we will cover creating a project in dotnet as it is easier to demonstrate, and also lets us cover the individual files that make up a Chocolatey package. To start, create the project in `dotnet` CLI by entering:

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
    <description>A simple example Chocolatey package.</description> 
    <language>en-US</language> 
    <tags>example</tags> 
  </metadata> 
</package> 
```

Then run the following to create the package: 

```bash
$ dotnet pack --configuration Release 
```

## Step 3: Adding the Feed to Chocolatey CLI { #add-feed }

To add your `internal-Chocolatey` feed to either Visual Studio or Chocolatey CLI, you will need the feed URL. This is found on the top right of the feed page:

![](/resources/docs/proget-Chocolatey-internal-url.png){height="" width="50%"}

### Using Visual Studio { #add-visualstudio }

To add your feed as a Package Manager in Visual Studio, navigate to "Tools" > "Chocolatey Package Manager" > "Package Manager Settings". Then uncheck the box to the left of *Chocolatey.org*

![visualstudio-packagesources-highlightedChocolateyorg.png](/resources/docs/visualstudio-packagesources-highlightedChocolateyorg.png)

This prevents Visual Studio from scanning both Chocolatey.org and ProGet for packages. If you configure Visual Studio to search only ProGet instead of Chocolatey.org and ProGet, you avoid problems such as bad licenses, vulnerable packages, and [dependency confusion](https://blog.inedo.com/software-supply-chain-security/three-things) in your packages when you use multiple sources.

Now you will need to create a new package source. Click the green `+` in the top right of the window, and then name the new package source. Then paste in your `internal-Chocolatey` feed URL.

![visualstudio-packagesources-configureproget.png](/resources/docs/visualstudio-packagesources-configureproget.png)

Now, click the "Update" button, followed by the "OK" button.

![visualstudio-packagesources-updateandok.png](/resources/docs/visualstudio-packagesources-updateandok.png)

Visual Studio and ProGet are now connected.

:::(Warning) (Be sure to click "Update" prior to clicking "OK")
If you click "OK" without clicking "Update" your package source configuration will not be saved in Visual Studio.
:::

To confirm the connection in Visual Studio, right-click on a project in the Solution Explorer and select “Manage Chocolatey Packages…” from the menu. In the Package Manager window under "Browse", your should see a window populated with packages from the `internal-Chocolatey` feed.

![visualstudio-connectedprogetfeed.png](/resources/docs/visualstudio-connectedprogetfeed.png)

### Using Chocolatey CLI { #add-cli }
Then add the feed as a source to your Chocolatey client by entering:

```bash
$ dotnet Chocolatey add source https://«proget-server»/Chocolatey/internal-Chocolatey/v3/index.json --name internal-Chocolatey
```

## Using Other Chocolatey Clients { #add-others }

ProGet can be added as a source in a number of other popular clients, including [VS Code](https://code.visualstudio.com/) and [JetBrains Rider](https://www.jetbrains.com/rider/).

### In VS Code

To add your `internal-Chocolatey` feed as a source, add it to a `Chocolatey.config` in your project. The config could look like this:

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <packageSources>
    <add key="internal-Chocolatey" value="https://«proget-server»/Chocolatey/internal-Chocolatey/v3/index.json" />
  </packageSources>
</configuration>
```

### In JetBrains Rider

To add your `internal-Chocolatey` feed as a source, open "Chocolatey Settings" by navigating to "File" > "Settings", and then "Build, Execution, Deployment" > "Chocolatey". Next, under the "Package Sources" tab, click on the + (Add) button to create a new package source.

In the Name field, enter a name for your source (e.g., `internal-Chocolatey`), and then in the URL field, enter the URL of your `internal-Chocolatey` feed. 

## Step 4: Setting Up Authentication to Your Chocolatey Feed.

To push packages to a feed you will need to configure authentication using a "Chocolatey API Key". While you can authenticate with a "psedo key" (`«username»:«password»`), we strongly recommend using an [API Key](/docs/proget/reference-api/proget-apikeys) for this, with `api` as the username, and then API Key as the password. To learn more about creating one, read [Authenticating to Chocolatey Feeds](/docs/proget/feeds/Chocolatey#authenticating-to-Chocolatey-feeds). 

## Step 5: Pushing Your Package to Your Chocolatey Feed

You can push your Chocolatey package to your Chocolatey feed in either Visual Studio or Chocolatey CLI. First make sure you have [added the feed as a source](#add-feed) with [authentication](/docs/proget/feeds/Chocolatey#authenticating-to-Chocolatey-feeds).

### Pushing a Package in Visual Studio

To push your Chocolatey package using Visual Studio, open the Package Manager Console by navigating to "Tools" > "Chocolatey Package Manager" > "Package Manager Console". Then. run the following Push command:

```bash
$ Chocolatey push «path-to-package» -Source «source-name» -ApiKey api:«apikey»
```

#### Example:

Pushing the package `MyPackage` to a configured source named `internal-Chocolatey`, you would enter:

```bash
$ Chocolatey push bin\Release\MyPackage.1.0.0.nupkg -Source internal-Chocolatey -ApiKey api:abc12345
```

### Pushing a Package with Chocolatey CLI

To push a Chocolatey package to ProGet in Chocolatey CLI, use the `dotnet Chocolatey push` command:

```bash
$ dotnet Chocolatey push «package-name» -Source «source-name»
```

### Examples:

Pushing the package `MyPackage` to a configured source named `internal-Chocolatey`, you would enter:

```bash
$ dotnet Chocolatey push bin/Release/MyPackage.1.0.0.nupkg -Source internal-Chocolatey
```

Your package will then be uploaded and appear in your Chocolatey feed.

![](/resources/docs/proget-Chocolatey-internal-uploaded.png){height="" width="50%"}

