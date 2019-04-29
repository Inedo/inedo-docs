---
title: ASP.NET
subtitle: Building an ASP.NET Web Application
sequence: 200
keywords: buildmaster, builds, dot-net, asp-net
---

### Pre-requisites: 

{.docs}
 - [Visual Studio Build Tools](https://visualstudio.microsoft.com/downloads) installed
 - Ensure that ".NET desktop build tools" and "Web development build tools" options are chosen during installation/modification
 
You can use BuildMaster to build any web application, including ASP.NET MVC, its predecessor ASP.NET WebForms, and even ASP.NET Core.

With the `MSBuild::Build-Project` operation to a plan, you can build any kind of ASP.NET application, targeting any platform or build configuration.

##### Operation example:

```
Build-Project <pathToProject>\<projectName>.csproj
(
    To: ~\Output
);
```

This plan effectively runs the following MSBuild command:

```
msbuild <pathToProject>\<projectName>.csproj "/p:Configuration=Release" "/p:OutDir=C:\...<buildmaster-temp>...\Output\\"
```

The contents of the `~\Output` folder after running this command will be the build output from MSBuild which for web applications without additional MSBuild properties passed in, follows the format:

```
~\Output\_PublishedWebsites\<projectName>\
```

This directory can be used to capture the website that will eventually be deployed into an artifact:

```
Create-Artifact Web
(
    From: ~\Output\_PublishedWebsites\<projectName>
);
```

#### Sample plan:

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

{.attention .analogy} To see an example of this plan, visit our public instance of BuildMaster: https://buildmaster.inedo.com/applications/35/plans