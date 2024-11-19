---
title: "Integrating NuGet Client Tools with ProGet"
order: 7
---

ProGet's NuGet feeds allow you to manage and distribute NuGet packages across your development environment. Once you've created a feed in ProGet, integrating it with popular NuGet client tools is straightforward. Using the Feed Endpoint URL, you can connect ProGet to Visual Studio, the NuGet CLI, and other tools like [VS Code](https://code.visualstudio.com/) and [JetBrains Rider](https://www.jetbrains.com/rider/).  This guide will walk you through the integration steps for each tool, ensuring you can manage packages efficiently in your development workflow.

:::(Info) (⚙ NuGet Feed Endpoint URL)

```plaintext
https://«proget-server»/nuget/«feed-name»/v3/index.json
```
:::

### Integrating Visual Studio

To use the Feed Endpoint URL in Visual Studio, navigate to  "Tools" > "NuGet Package Manager" > "Package Manager Settings". Then click the green `+` in the top right of the window, and then name the new package source and paste in your URL:

![visualstudio-packagesources-configureproget.png](/resources/docs/visualstudio-packagesources-configureproget.png)

:::(Warning) (Be sure to click "Update" prior to clicking "OK")
If you click "OK" without clicking "Update" your package source configuration will not be saved in Visual Studio.
:::

Visual Studio and ProGet will now be connected.

To confirm the connection in Visual Studio, right-click on a project in the Solution Explorer and select “Manage NuGet Packages…” from the menu. In the Package Manager window under "Browse", your should see a window populated with packages from your ProGet NuGet feed.

![visualstudio-connectedprogetfeed.png](/resources/docs/visualstudio-connectedprogetfeed.png)

### Integrating the NuGet CLI

You can also use the NuGet CLI to add a source:

```bash
$ dotnet nuget add source --name "Internal NuGet" https://«proget-server»/nuget/«feed-name»
```

Once you've added the NuGet feed as a source, you'll be able to install and upgrade NuGet packages.

### Using Other NuGet Clients { #add-others }

ProGet can also be added as a source in a number of other popular clients, including [VS Code](https://code.visualstudio.com/) and [JetBrains Rider](https://www.jetbrains.com/rider/).

#### In VS Code

To add your NuGet feed in ProGet as a source, add it to a `nuget.config` in your project. The config could look like this:

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <packageSources>
    <add key="proget" value="https://«proget-server»/nuget/«feed-name»/v3/index.json" />
  </packageSources>
</configuration>
```

#### In JetBrains Rider

To add your NuGet feed in ProGet as a source, open "NuGet Settings" by navigating to "File" > "Settings", and then "Build, Execution, Deployment" > "NuGet". Next, under the "Package Sources" tab, click on the `+` (Add) button to create a new package source.

In the `Name` field, enter a name for your source (e.g., `internal-nuget`), and then in the URL field, enter the URL of your feed. 