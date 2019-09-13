---
title: .NET Core
subtitle: Building Cross-platform .NET Core Applications
sequence: 200
show-headings-in-nav: true
---

BuildMaster can help you build applications for Windows, Linux, MacOS, containers, and so on using Microsoft's .NET Core platform.


## What is .NET Core?  {#dotnetcore-overview data-title="What is .NET Core?"}

[.NET Core](https://docs.microsoft.com/en-us/dotnet/core/) is an open-source, cross-platform environment that is evolved from the .NET Framework. Compared to the .NET Framework, it offers a simpler toolchain, modular deployment, and support for multiple platforms rather than just Windows. 

With some development and coding effort, you can convert some of your existing .NET projects to .NET Core so they can run anywhere.

## Building .NET Core Projects with BuildMaster {#building-in-buildmaster data-title="Building in BuildMaster"}

You can build your .NET Core projects just as easily as your regular .NET projects, using either MSBuild (`MSBuild::Build-Project`) or the newer .NET Core CLI, `dotnet.exe` (`DotNet::Build`).

[MSBuild (Microsoft Build Engine)](https://docs.microsoft.com/en-us/visualstudio/msbuild/msbuild) is a platform for building applications, and it's currently used internally by `dotnet.exe` to build your project and solutions. 

MSBuild and `dotnet.exe` don't require Visual Studio to be installed, but you will need to install and configure the following:
{.docs}
 - [Visual Studio Build Tools](https://visualstudio.microsoft.com/downloads) installed
 - Ensure that ".NET Core build tools" is chosen during installation/modification.

### Using MSBuild to build your .NET Core Projects

Check out [Building a .NET console application in BuildMaster](console-app) documentation to see how to use MSBuild. It works the same with .NET Core projects.


### Using dotnet.exe to build your .NET Core Projects

After getting your code from source control, you can use `DotNet::Build` in your plan is as follows.

```
# Build ~\src\MyProject.csproj with Release configuration, restoring NuGet packages from the InternalNuGet source
DotNet::Build ~\src\MyProject.csproj
(
    Configuration: Release,
    PackageSource: InternalNuGet
);
```

Behind the scenes, BuildMaster will effectively run the following `dotnet` command:

```
dotnet.exe build "c:\...<buildmaster-temp>...\MyProject.csproj" --configuration Release --source https://proget.kramerica.corp/nuget/internal
```

### Capturing build output after building a .NET Core project 

After running either ``DotNet::Build`` or `MSBuild::Build-Project`, you can capture the compiled code as an artifact that you will eventually deploy.

```
Create-Artifact Web
(
    From: ~\Output
);
```

You can also capture it as a Universal Package with `ProGet::Create-Package` or NuGet package with `NuGet::Create-Package`.

::: {.attention .analogy } 
![Light bulb](/resources/images/icons/analogy.png)  {#example data-title="Example Application"}

**See it live!** The [ProfitCalc-AspNet Application](https://buildmaster.inedo.com/applications/35/) demonstrates how to build a .NET application with a CI/CD process. It's also available as a tutorial application in your own instance of BuildMaster.
:::

## MSBuild vs .NET Core CLI (`dotnet.exe build`) {#comparison data-title="MSBuild vs dotnet.exe"}

As part of Microsoft's efforts to simplify development with .NET Core, they introduced a new CLI tool called `dotnet`. This CLI tool has a number of commands, and the `dotnet build` command is used to build projects. 

While `dotnet build` is considerably simpler to use than MSBuild, it uses MSBuild behind the scenes. According to the [`dotnet build` documentation](https://docs.microsoft.com/en-us/dotnet/core/tools/dotnet-build):

> Running `dotnet build` is equivalent to `dotnet msbuild -restore -target:Build`

There are different arguments to `dotnet build` and `msbuild` (with different default values), but the behavior of these commands are identical. Of course, running `dotnet msbuild` is the equivalent to running `msbuild` directly. 

It's a bit confusing, and to make matters worse, there were some bugs in early versions of `msbuild` and `dotnet` that made these commands behave slightly differently; but that's been allegedly fixed through various Visual Studio 2017 updates.

Ultimately, using `dotnet build` or `msbuild` is supposed to come down to personal preference, but you may find that one just works better than another.

