---
title: ASP.NET
subtitle: Building an ASP.NET Web Application
sequence: 200
keywords: buildmaster, builds, dot-net, asp-net
---

You can use BuildMaster to build any web application, including ASP.NET MVC, its predecessor ASP.NET WebForms, and even ASP.NET Core.

Behind the scenes, BuildMaster uses the `MSBuild::Build-Project` operation to do this. When you add it to your plan, you can build any kind of ASP.NET application, targeting any platform or build configuration.

## MSBuild Overview & Pre-requisites

[MSBuild (Microsoft Build Engine)](https://docs.microsoft.com/en-us/visualstudio/msbuild/msbuild) is a platform for building applications, and it's used internally by Visual Studio to to build your project and solutions. 

MSBuild doesn't require Visual Studio to be installed, but you will need to install and configure the following:
{.docs}
 - [Visual Studio Build Tools](https://visualstudio.microsoft.com/downloads) installed
 - Ensure that ".NET desktop build tools" and "Web development build tools" options are chosen during installation/modification

## How to use the `MSBuild::Build-Project` Operation

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

### MSBuild Output & _PublishedWebsites 

One important argument is `To`, which indicates where the build output would go. In the above example, the contents of the `~\Output` folder will be the build output from MSBuild.

However, web applications are often poublished to a directory within the output folder, in the following format:

```
~\Output\_PublishedWebsites\<projectName>\
```

This directory is where you can capture the website that you will eventually deploy as an artifact.

```
Create-Artifact Web
(
    From: ~\Output\_PublishedWebsites\<projectName>
);
```

## Example: Building from Branches with Optional Tests

The following example shows how to build an ASP.NET website at the solution level, using two optional [release template variable prompts](/support/documentation/buildmaster/releases/templates#components):

{.docs}
 - `$Branch` - the specified branch to pull from
 - `$ExecuteTests` - a checkbox variable allowing the option to build without running unit tests

```
# Get Source
# Fetch code changes from your source control system in order to build and run tests against them
{
    Git::Get-Source
    (
        RepositoryUrl: https://github.com/kramerica-industries/Accounts.git,
        Branch: $Branch
    );
}

# Compile Project
# Run a familiar build tool such as MSBuild, Maven, make, etc., pull packages & libraries, lint your code, and/or set version numbers.
{
    NuGet::Restore-Packages();

    Write-AssemblyVersion
    (
        Version: $ReleaseNumber.$BuildNumber,
        FromDirectory: ~\Src
    );

    MSBuild::Build-Project Accounts.sln
    (
        To: ~\Output
    );
}

# Run Tests
# Execute unit tests, static analysis tools, or capture reports.
if $ExecuteTests
{
    WindowsSdk::Execute-VSTest
    (
        TestContainer: ~\Output\Accounts.Tests.dll
    );
}

# Capture Artifact
# Create a build artifact from the build output once all tests pass.
{
    Create-Artifact Website
    (
        From: ~\Output\_PublishedWebsites\Accounts.Web
    );
}
```

{.attention .analogy} To see an example of these plans, visit the [ProfitCalc-AspNet](https://buildmaster.inedo.com/applications/35/) application in our public instance of BuildMaster, and navigate to Plans.
