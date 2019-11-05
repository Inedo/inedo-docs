---
title: ASP.NET MVC & WebForms
subtitle: Building an ASP.NET MVC or WebForms Application
sequence: 200
show-headings-in-nav: true
keywords: buildmaster, builds, dot-net, asp-net
---

ASP.NET MVC is the most popular web framework built for .NET that allows developers to build maintainable, scalable, cross-platform web applications. By separating concerns (i.e. not coupling the HTML views with the database backend and vice-versa), teams of developers with a variety of skillsets are able to focus on their areas of expertise without having to understand the intimate details of the underlying framework like its predecessor ASP.NET WebForms.

## Building ASP.NET Applications {#building data-title="Build Process"}

[MSBuild (Microsoft Build Engine)](https://docs.microsoft.com/en-us/visualstudio/msbuild/msbuild) is a tool for building all types of .NET applications, and it's used internally by Visual Studio to build projects and solutions. CI servers would perform similar build tasks or operations by invoking MSBuild operations.

MSBuild doesn't require Visual Studio to be installed, but you will need to install and configure the following:

{.docs}
 - [Visual Studio Build Tools](https://visualstudio.microsoft.com/downloads) are installed
 - Ensure that ".NET desktop build tools" and "Web development build tools" options are chosen during installation/modification

The simplest way to build a web application is by running MSBuild directly:

```
msbuild.exe MyWebProject.csproj "/p:Configuration=Release" "/p:OutDir=C:\tmp\website\\"
```

The difference between web applications from other types of applications (e.g. console applications, WinForms, or WPF) is that when an output directory is specified, the build output is generated in a special subdirectory in the format:

```
<outputDir>\_PublishedWebsites\<projectName>
```

The contents of this output directory are the files that would be [deployed to the IIS home directory](/docs/buildmaster/deployments/targets/windows/iis).

## Building ASP.NET Applications with BuildMaster {#buildmaster data-title="Automation with BuildMaster"}

::: {.attention .best-practice}
You can view an example BuildMaster application that builds and deploys to an IIS website by creating a new application and selecting the "ProfitCalc ASP.NET Tutorial" application.
:::

Because building ASP.NET applications simply involves running MSBuild, building ASP.NET applications is simple with BuildMaster. Behind the scenes, BuildMaster uses the `MSBuild::Build-Project` operation to run MSBuild.

The general process for building an ASP.NET application is as follows:

{.docs}
 - Get source code from the source control repository
 - Compile project with MSBuild
 - Capture artifact for deployment

A rough example plan of this would be:

```
Git::Get-Source
(
    RepositoryUrl: https://github.com/Inedo/ProfitCalc.git,
    Branch: master
);

MSBuild::Build-Project ProfitCalc.Web\ProfitCalc.Web.csproj
(
    To: ~\Output
);

Create-Artifact
(
    From: ~\Output\_PublishedWebsites\ProfitCalc.Web
);
```

The `MSBuild::Build-Project` operation in this example effectively runs the following MSBuild command:

```
msbuild.exe ProfitCalc.Web\ProfitCalc.Web.csproj "/p:Configuration=Release" "/p:OutDir=C:\...<buildmaster-temp>...\Output\\"
```

### Restoring NuGet Packages

By default, MSBuild does not restore NuGet packages during a build, often the cause of "are you missing an assembly reference" errors. This can be accomplished in BuildMaster using the `NuGet::Restore-Packages()` operation before building a project.

### Unit Tests

Unit tests for ASP.NET applications are handled by [VSTest](/docs/buildmaster/builds/tests/unit-tests/vstest-runner). An example operation to execute and capture unit test results is as follows:

```
WindowsSdk::Execute-VSTest
(
    TestContainer: ~\Output\ProfitCalc.Tests.dll
);
```
