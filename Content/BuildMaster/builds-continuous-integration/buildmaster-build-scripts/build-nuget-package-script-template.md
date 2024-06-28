---
title: "Build NuGet Package Script Template"
order: 2
---

A script template is a simple, no-code solution for performing common build or deployment operations and can be converted to OtterScript at any time. 

The Build NuGet Package script template will:
* [check out code from Git](#checking-out-from-git)
* [compile a .NET project or solution](#buildingcompiling-net-project)
* [optionally run tests](#running-unit-tests)
* [pack your project into a NuGet package](#packaging-into-nuget)
* [publish your NuGet package to a Package Source](#push-to-feed)

This article will detail the options for these steps, as well as how they're performed.

## Using This Script Template
A [synchronized Git repository](/docs/buildmaster/builds-continuous-integration/buildmaster-git-source-control#synchronized-git-repositories) is required for this template. If your application does not have one, you will not be able to add or edit this script template.

To add this script template to your application, navigate to Scripts, click  Add Script, and select Build NuGet Package from the list of Build Script Templates. After doing so, you'll see a dialog like this:

![buildmaster-dotnet-build-template](/resources/docs/buildmaster-create-nuget-script.png){height="" width="50%"}

There are a lot of options available. This article will discuss how these options are used in different steps of the build process.

Note that the [dotnet SDK](https://get.dot.net) is required on the server you'll run this script on (i.e. your build server). However, you can check the "Ensure dotnet SDK is installed" box in the "dotnet Options" to automatically install the required SDK as part of the build process.

## Checking Out from Git
First, the script template checks out code from a Git repository. The branch (or specific commit) is selected when the build is created, before the script is even run. The script will then check out code from that exact commit.

:::(Info) (🛠 OtterScript Notes)
This step uses the `Git::Checkout-Code` operation without any parameters.
:::

## Building/Compiling .NET Project
Next, the script template will use the [.NET CLI](https://learn.microsoft.com/en-us/dotnet/core/tools/) to build/compile your project using `dotnet build`.

### Project or Solution
Select either a solution (.sln) file or a project file (.csproj) from your source repository that will be built. In most cases, it won't make a difference which one you select—but for this template, it's generally recommended to use the project file to keep things simple.

### NuGet Package Source
Select where to fetch the NuGet packages needed to build your project. This list will typically contain NuGet Feeds from your connected ProGet instance and an option for Not Specified, which will use the public NuGet.org repository. 

You can also enter in a custom URL to a feed, though this isn't recommended, as you can create a [NuGet feed resource](/docs/buildmaster/configuring-for-your-team/buildmaster-administration-resource-credentials) and use that instead.

:::(Internal) (TODO)
Link to ProGet Integration once those docs are written
:::

### Build Configuration
By default, this is set to `Release`, which will perform a standard optimized build. You can set it to `Debug` or any other build configuration if needed.

### Other Options
Settings for other common operations using [dotnet build](https://learn.microsoft.com/en-us/dotnet/core/tools/dotnet-build) are also included:

* **Assembly version**: Ensures that the built .NET assembly has this version. The default value is the variable `$ReleaseNumber`, which will supply the release number associated with the current build.
* **Additional arguments**: These are passed to `dotnet` exactly as specified

:::(Info) (🛠 OtterScript Notes)
This step uses the `DotNet::Build` operation, the parameters will depend on the options you've entered.
:::

## Running Unit Tests
If any unit test projects are detected, you can select one to run as part of your build script. You can also enter the name of a project if one wasn't detected.

Select a test project will call [dotnet test](https://learn.microsoft.com/en-us/dotnet/core/tools/dotnet-test) and record the results in BuildMaster. See our [unit tests](/docs/buildmaster/builds-continuous-integration/automated-testing-verification/buildmaster-ci-cd-testing-and-verification-unit-tests)  documentation to learn more.

:::(Info) (🛠 OtterScript Notes)
This step uses the `DotNet::Test` operation with the `Project` parameter set to the value of the "Test project".
:::

## Packaging into NuGet
The script template will again use the [.NET CLI](https://learn.microsoft.com/en-us/dotnet/core/tools/), this time calling `dotnet pack`.

### Package ID
The NuGet Package ID can be set, but will otherwise use the default value that `dotnet pack` uses, which is the project name.

### Package Version
The default value for the package version is `$ReleaseNumber-ci.$BuildNumber`; later in the pipeline, the package can be repackaged into a stable release version.

:::(Info) (🛠 OtterScript Notes)
This step uses the `DotNet::Pack` operation, and the parameters will depend on the options you've entered. The `DotNet::Set-ProjectVersion` operation is used to set the package and assembly version. This is run prior to building/compiling the project.
:::

## Push to Feed
The final step will be pushing the package to a NuGet feed, and then attaching a link to that package on the build.

Similar to the NuGet package source, this "Push to feed" list will typically contain NuGet Feeds from your connected ProGet instance and other [NuGet feed resource](/docs/buildmaster/configuring-for-your-team/buildmaster-administration-resource-credentials) that you've configured.

:::(Info) (🛠 OtterScript Notes)
This step uses the `NuGet::Push-Package` operation, and then the `Packages::Attach-Package` operation.
:::




