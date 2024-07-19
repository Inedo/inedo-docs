---
title: "Build PyPI Project"
order: 3
---

:::(Internal) (TODO)
- Link to ProGet Integration once those docs are written
- Retake Screenshot w/ No whitespace
:::

A script template is a simple, no-code solution for performing common build or deployment operations and can be converted to OtterScript at any time. 

The Build PyPI Project script template will:
* [check out out code from Git](#checking-out-from-git)
* [install packages using pip](#install-pypi-packages)
* [optionally run tests](#install-pypi-packages)
* [track package usage](#record-dependencies-to-proget)
* [capture build artifacts for later deployment](#capturing-build-artifacts)

This article will detail the options for these steps, as well as how they're performed.

## Using This Script Template
A [synchronized Git repository](/docs/buildmaster/builds-continuous-integration/buildmaster-git-source-control#synchronized-git-repositories) is required for this template. If your application does not have one, you will not be able to add or edit this script template.

To add this script template to your application, navigate to Scripts, click Add Script, and select Build PyPI Project from the list of Build Script Templates. After doing so, you'll see a dialog like this:

![buildmaster-python-script-template](/resources/docs/buildmaster-python-script-template.png){height="" width="50%"}

This article will discuss how these options are used in different steps of the build process.


## Checking Out from Git
First, the script template checks out code from a Git repository. The branch (or specific commit) is selected when the build is created, before the script is even run. The script will then check out code from that exact commit.

:::(Info) (🛠 OtterScript Notes)
This step uses the `Git::Checkout-Code` operation without any parameters.
:::

## Install PyPI Packages

### PyPI Index
Select where to fetch the PyPI packages needed for your project. This list will typically contain PyPI feeds from your connected ProGet instance, and an option for Not Specified, which will use the default repository.

:::(Internal) (TODO)
Link to ProGet Integration once those docs are written
:::

## Run PyUnit Tests
Select whether to run PyUnit unit tests for this project.

:::(Info) (🛠 OtterScript Notes)
This step uses the `Python::Execute-PyUnit` operation without any parameters.
:::


### Other Options
Settings for other common operations using [dotnet build/publish](https://learn.microsoft.com/en-us/dotnet/core/tools/dotnet-build) are also included:

* **Output directory**: Build output will be copied here instead of the default output directory
* **Framework**: Framework version to build, typically only used for multi-targeted projects. Uses common .NET monikers such as net6.0, net452, netstandard2.0, etc
* **Runtime**: When publishing an executable, specifies the runtime/platform to target such as win-x64, linux-x64, etc
* **Self-contained**: When publishing, include the entire .NET runtime and all required dependencies in the output
* **Additional arguments**: These are passed to `dotnet` exactly as specified

:::(Info) (🛠 OtterScript Notes)
This step uses the `DotNet::Build` or `DotNet::Publish` operation, depending on the Command you selected. The parameters will depend on the options you've entered.
:::

## Record Dependencies to ProGet
 :::(Internal) (TODO)
Link to ProGet Integration once those docs are written
:::
When a ProGet feed is selected for a NuGet package source, a checkbox to scan for package dependencies will appear. If checked, the PyPI dependencies for this build will be recorded in your ProGet instance.

:::(Info) (🛠 OtterScript Notes)
This step uses the `ProGet::Scan` operation with the `Project` parameter set to `requirements.txt`.
:::

## Capturing Build Artifacts
As the last step, BuildMaster will [capture artifacts](/docs/buildmaster/builds-continuous-integration/buildmaster-artifacts) from a path within the working directory that you specify. These are the files that you will deploy later.

 
 ### Finding the Right Subpath
 Usually, the easiest way to find the path you want is:
 1. Create a build using this template
 2. Inspect the build's artifacts
 3. Find the subpath you want within the artifact
 4. Enter that in the "Capture artifacts from:"

The build artifact going forward will only contain those files. To learn about more options with artifacts, see the [Build Artifacts](/docs/buildmaster/builds-continuous-integration/buildmaster-artifacts) documentation.

:::(Info) (🛠 OtterScript Notes)
This step uses the `Create-Artifact` operation, with the parameters depending on the options you've entered.
:::