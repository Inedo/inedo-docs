---
title: .NET Core
subtitle: Building .NET Core Applications
sequence: 300
show-headings-in-nav: true
---

[.NET Core](https://docs.microsoft.com/en-us/dotnet/core/) is an open-source, cross-platform environment that is evolved from the .NET Framework. Compared to the .NET Framework, it offers a simpler toolchain, modular deployment, and support for multiple platforms rather than just Windows. 

With some development and coding effort, you can convert some of your existing .NET projects to .NET Core so they can run on other platforms including Linux and MacOS.

## Building .NET Core Applications {#building data-title="Build Process"}

.NET Core applications are typically built using MSBuild or `dotnet.exe`. MSBuild and `dotnet.exe` don't require Visual Studio to be installed, but in order to use them, the following prerequisites are required on a build server:

{.docs}
 - [Visual Studio Build Tools](https://visualstudio.microsoft.com/downloads) installed
 - Ensure that ".NET Core build tools" is chosen during installation/modification

A further [comparison between the tools](#comparison) is documented below.

### Building with MSBuild

Refer to the [Building .NET Console Applications](console-app#building) documentation to see how to use MSBuild. .NET Core projects can be built exactly the same way.

### Building with `dotnet.exe`

Another method for building is using the [`dotnet.exe` command line tool](https://docs.microsoft.com/en-us/dotnet/core/tools/dotnet). An example command is as follows:

```
dotnet.exe build MyProject.csproj --configuration Release --source https://proget.kramerica.corp/nuget/internal
```

The output from this build will be an application that can be executed on a machine with .NET Core installed. However, this command alone cannot generate a standalone executable for all platforms. The [`dotnet publish`](https://docs.microsoft.com/en-us/dotnet/core/tools/dotnet-publish) command must be used in conjunction with the build command in order to produce build output that is executable in your desired environment.

## Building .NET Core Applications with BuildMaster {#buildmaster data-title="Automation with BuildMaster"}

You can build your .NET Core projects just as easily as your regular .NET projects, using either MSBuild (`MSBuild::Build-Project`) or the newer .NET Core CLI, `dotnet.exe` (`DotNet::Build`).

The general process for building a .NET Core application is as follows:

{.docs}
 - Get source code from the source control repository
 - Compile project with MSBuild or `dotnet.exe`
 - Capture artifact for deployment

A rough example plan of this would be:


```
Git::Get-Source
(
    RepositoryUrl: https://github.com/Inedo/ProfitCalc.git,
    Branch: master
);

DotNet::Build ProfitCalc.NetCore\ProfitCalc.NetCore.csproj
(
    Configuration: Release,
    PackageSource: InternalNuGet
);

Create-Artifact
(
    From: ProfitCalc.NetCore\bin\Release
);
```

The `DotNet::Build` operation will effectively run the following `dotnet` command:

```
dotnet.exe build "c:\...<buildmaster-temp>...\ProfitCalc.NetCore.csproj" --configuration Release --source https://proget.kramerica.corp/nuget/internal
```
### Restoring NuGet Packages

By default, MSBuild does not restore NuGet packages during a build, which is often the cause of "are you missing an assembly reference" errors. To install NuGet packages before running MSBuild, use the `NuGet::Restore-Packages()` as follows:

```
NuGet::Restore-Packages
(
    Target: ~\Src\<project-name>,
    Source: https://proget.corp/nuget/InternalNuGet/
);
```

This will essentially call `nuget.exe install`, and instruct it to look for a `packages.config` file in the `SourceDirectory`, and packages in the target `Source`.

The `dotnet.exe` CLI however will automatically restore NuGet packages at build time (i.e. when `dotnet build` is run). To use a custom source, use the `--source` argument, which is supplied automatically when the `PackageSource` property of the `DotNet::Build` operation is executed.

### Unit Tests

Unit tests for .NET Core applications are normally executed using the [`dotnet test`](https://docs.microsoft.com/en-us/dotnet/core/tools/dotnet-test) command. Alternatively, there is a [`dotnet vstest`](https://docs.microsoft.com/en-us/dotnet/core/tools/dotnet-vstest) command that executes the VSTest.Console application under-the-hood.

In order to view individual test results via BuildMaster, [VSTest](/docs/buildmaster/builds/tests/unit-tests/vstest-runner) should be used. An example operation to execute and capture unit test results against a compiled library is as follows:

```
WindowsSdk::Execute-VSTest
(
    TestContainer: ~\Output\ProfitCalc.Tests.dll
);
```

## MSBuild vs .NET Core CLI (`dotnet.exe build`) {#comparison data-title="MSBuild vs dotnet.exe"}

While `dotnet build` is considerably simpler to use than MSBuild, it uses MSBuild behind the scenes. According to the [`dotnet build` documentation](https://docs.microsoft.com/en-us/dotnet/core/tools/dotnet-build):

> Running `dotnet build` is equivalent to `dotnet msbuild -restore -target:Build`

There are different arguments to `dotnet build` and `msbuild` (with different default values), but the behavior of these commands are identical. Of course, running `dotnet msbuild` is the equivalent to running `msbuild` directly. 

It's a bit confusing, and to make matters worse, there were some bugs in early versions of `msbuild` and `dotnet` that made these commands behave slightly differently; but that's been allegedly fixed through various Visual Studio 2017 updates.

Ultimately, using `dotnet build` or `msbuild` is supposed to come down to personal preference, but you may find that one just works better than another.

## Deploying ASP.NET Applications to IIS {#buildmaster data-title="Deploying to IIS"}

While ASP.NET Core applications can be hosted in a variety of web servers, Microsoft's Internet Information Services (IIS) is the most common. See [Deploying an IIS Website](/docs/buildmaster/deployments/targets/windows/iis) to learn how to accomplish this with BuildMaster.
