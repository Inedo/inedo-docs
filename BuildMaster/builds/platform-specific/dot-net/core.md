---
title: .NET Core
subtitle: Building Cross-platform .NET Core Applications
sequence: 200
keywords: buildmaster, builds, dot-net
---

BuildMaster can help you build applications for Windows, Linux, MacOS, containers, and so on using Microsoft's .NET Core platform.

## What is .NET Core

[.NET Core](https://docs.microsoft.com/en-us/dotnet/core/) is an open-source, cross-platform environment that is evolved from the .NET Framework. Compared to the .NET Framework, it offers a simpler toolchain, modular deployment, and support for multiple platforms rather than just Windows. 

With some development and coding effort, you can convert some of your existing .NET projects to .NET Core so they can run anywhere.

## Building .NET Core Projects with BuildMaster

You can build your .NET Core projects just as easily as your regular .NET projects. In fact, .NET Core uses the same underlying tools (MSBuild) and operations within BuildMaster (`MSBuild::Build-Project`).

[MSBuild (Microsoft Build Engine)](https://docs.microsoft.com/en-us/visualstudio/msbuild/msbuild) is a platform for building applications, and it's used internally by the .NET Core CLI (dotnet.exe) to build your project and solutions. 

MSBuild doesn't require Visual Studio to be installed, but you will need to install and configure the following:
{.docs}
 - [Visual Studio Build Tools](https://visualstudio.microsoft.com/downloads) installed
 - Ensure that ".NET Core build tools" is chosen during installation/modification.


### Using MSBuild to build your .NET Core Projects

After getting your code from source control, you can use the MSBuild in your plan is as follows.

```
Build-Project <pathToProject>\<projectName>.csproj
(
    To: ~\Output
);
```

Behind the scenes, BuildMaster will effectively run the following MSBuild command:

```
msbuild.exe <pathToProject>\<projectName>.csproj "/p:Configuration=Release" "/p:OutDir=C:\...<buildmaster-temp>...\Output\\"
```

After running MSBuild, you can capture the compiled code as an artifact that you will eventually deploy.

```
Create-Artifact Web
(
    From: ~\Output
);
```

{.attention .analogy} To see an example of MSBuild in a plan, visit the [ProfitCalc-AspNet](https://buildmaster.inedo.com/applications/35/) application in our public instance of BuildMaster, and navigate to Plans.


## MSBuild vs .NET Core CLI (`dotnet.exe build`)

As part of Microsoft's efforts to simplify development with .NET Core, they introduced a new CLI tool called `dotnet`. This CLI tool has a number of commands, and the `dotnet build` command is used to build projects. 

While `dotnet build` is considerably simpler to use than MSBuild, it uses MSBuild behind the scenes. According to the [`dotnet build` documentation](https://docs.microsoft.com/en-us/dotnet/core/tools/dotnet-build):

> Running `dotnet build` is equivalent to `dotnet msbuild -restore -target:Build`

There are different arguments to `dotnet build` and `msbuild` (with different default values), but the behavior of these commands are identical. Of course, running `dotnet msbuild` is the equivelant to running `msbuild` directly. 

It's a bit confusing, and to make matters worse, there were some bugs in early versions of `msbuild` and `dotnet` that made these commands behave slightly differently; but that's been fixed through various Visual Studio 2017 updates.

Ultimately, using `dotnet build` or `msbuild` really comes down to personal preference. 

## Running `dotnet.exe` Within BuildMaster

Although you can use the `MSBuild::Build-Project` operation to build your .NET Core projects, you may wish to execute `dotnet build` instead. Perhaps because of a strange bug or edgecase in the version of those tools.

To do this, you can use the `Exec` operation:

````
Exec
(
    FileName: C:\Program Files\dotnet\dotnet.exe,
    Arguments: build
    WarnRegex: \bwarning\b
);
````

This will also capture dotnet.exe's warnings as proper warnings in BuildMaster's logs. 

To make it even easier to use `dotnet.exe`, you can create an OtterScript module such as this one:

```
module Build-DotNetCore<$Configuration, $In = default, $OutputDirectory = default>
{
    set $srcPath = $WorkingDirectory;
    if $In != default
    {
        set $srcPath = $In;
    }
    
    set $args = "build --configuration $Configuration";
    if $OutputDirectory != default
    {
        set $args = '$args --output "$OutputDirectory"';
    }
    
    Exec
    (
        FileName: C:\Program Files\dotnet\dotnet.exe,
        Arguments: $args,
        WorkingDirectory: $srcPath,
        WarnRegex: \bwarning\b
    );
}
```
Once created as a global module, you can now use `Build-DotNetCore` easily from any OtterScript plan in BuildMaster:

```
call Build-DotNetCore
(
    Configuration: Release,
    In: C:\Projects\Accounts
);
```
