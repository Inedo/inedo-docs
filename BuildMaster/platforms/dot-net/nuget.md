---
title: NuGet Packages
subtitle: CI/CD for NuGet Packages
sequence: 400
show-headings-in-nav: true
---

[NuGet](https://nuget.org/) is the packaging platform used with .NET and provides developers easy access to NuGet.org, a public package gallery with [thousands of free and open source packages](https://www.nuget.org/packages). By discovering and incorporating these code packages into in their own applications, developers can save them time by not solving the same problem over and over. In addition, NuGet packages are much more manageable and less error-prone than "copy/pasting" code found on the Internet. They have dependency management built-in, and developers can easily find out when new version of packages are published (bug fixes, security bugs) and rebuild their application using the new version of the packages.

Although NuGet was originally designed for free and open source packages hosted at NuGet.org, with a private package repository like [ProGet](https://inedo.com/proget), you can host your own packages without making them public. This gives you the same benefits of code reuse, code sharing, within your organization. By using BuildMaster and ProGet's advanced features, you can apply Continuous Integration & Continuous Delivery (CI/CD) principles to rapidly test and deliver these packages alongside your application.

::: {.attention .analogy #template-application data-title="NuGet CI/CD Template" } 
<img src="/resources/images/icons/analogy.png" alt="Light bulb" />

**See it live!** The *NuGet CI/CD Template* is available as an application template in your own instance of BuildMaster.
:::


## NuGet Package CI/CD Pipeline {#pipeline data-title="NuGet Package CI/CD Pipeline"}

CI/CD pipelines for NuGet packages are similar to CI/CD pipelines for applications: a [Build Trigger](/docs/buildmaster/builds/continuous-integration/build-triggers-and-monitors) monitors source control and automatically creates a build that enters a deployment pipeline. However, instead of creating build artifacts at the first stage and deploying those artifacts to each environment, you will create and publish a pre-release package, publish it to a package source, then repackage at each stage.

{.docs}
- **Build**: create a pre-release Continuous Integration (CI) version of a package (e.g. `ProfitCalc v3.2.4-ci.11`) and publish to your package source (i.e. ProGet feed)
- **Test**: instruct ProGet to repackage that version into a more stable, Release Candidate (RC) version (e.g. `ProfitCalc-3.2.4-rc.11`)
- **Release**: instruct ProGet to repackage the RC version to a stable version (e.g. `ProfitCalc-3.2.4`)

If you have a lot of CI packages due to a lot of commits, you should consider publishing those packages to a different feed (a CI feed, for example), and instructing ProGet to promote the repackaged packages.

### NuGet Package CI/CD Development Workflow {#development-workflow data-title="Development Workflow"}

Teams who are new to building and using their own NuGet packages are often frustrated by the additional steps required in separating code across multiple projects. However, this package CI/CD workflow can greatly simplify package development and use for developers.

1. Develop and test package changes locally (i.e. on workstation)
2. Commit a change to a package's repository then wait for CI package to be created
3. Upgrade application to use CI package (locally), then make appropriate changes and test (locally)
4. Promote package from CI Build to Test stage in BuildMaster
5. Upgrade application to use RC package, then commit changes
6. Proceed as per any other application change

The RC package can be promoted to a stable package at anytime, and other developers can download and use RC packages as needed. And because all of this is managed as a pipeline in BuildMaster, everyone has visibility into which stages each package is in.

However, you should still never release an application that uses unstable (pre-release) packages. This means:

{.docs}
 - publish packages to production before your application is promoted
 - can use an automatic to ensure that applications don't use pre-release packages


## Creating a Package Version Number {#create-version-number data-title="Creating a Package Version"}

NuGet packages uses the [semantic version format](https://semver.org/), which is a 3-part number (MAJOR.MINOR.PATCH) that determines how backwards- and forwards-compatible changes will be. Before building a NuGet package, you will need this version number to create the package. 

This is where BuildMaster comes in: it's the source of truth for build and release numbers, which means you simply use those to generate your package version number. There's no need to commit this version number to source control, and doing so will actually make it harder to keep these numbers in sync. 

The easiest way to create a package version number is by comparing the release and build numbers in an OtterScript expression:

````
set $packageVersion = $ReleaseNumber-CI.$BuildNumber;
````

You can then use this variable in subsequent operations in your OtterScript.

::: {.attention .technical }
**Anti-pattern: Directly Building Stable Packages** 
You should never build a stable (i.e. non-prerelease) package. Always build a prerelease package, then let BuildMaster and ProGet promote and repackage.
:::

When first developing  a package for the first time, you should use - worth adding version 0.y.z should be used in development


## Building NuGet Packages {#building data-title="Building NuGet Packages"}
A NuGet package is a ZIP file that contains a [package manifest file](https://docs.microsoft.com/en-us/nuget/reference/nuspec) and compiled code (DLLs). Microsoft supports three different methods to create NuGet packages, all of which you can do with operations in BuildMaster:

- [dotnet CLI](https://docs.microsoft.com/en-us/nuget/create-packages/creating-a-package-dotnet-cli) uses the `DotNet::build` operation
- [nuget.exe CLI](https://docs.microsoft.com/en-us/nuget/create-packages/creating-a-package) uses the `NuGet::Create-Package` operation
- [MSBuild](https://docs.microsoft.com/en-us/nuget/create-packages/creating-a-package-msbuild) uses the `MSBuild::Build-Project` operation

All of these methods and operations will ultimately create the same package, but before doing that make sure to [create and set a version number](#create-version-number).

For the [NuGet CI/CD template application](#template-application), we use the `DotNet::Set-ProjectVersion` to edit the project file, and then the `DotNet::Build` operation to build the code and create the package.

    DotNet::Set-ProjectVersion
    (
        FromDirectory: ~\Src\$PackageName,
        Version: $ReleaseNumber,
        AssemblyVersion: $ReleaseNumber.0,
        FileVersion: $ReleaseNumber.$BuildNumber,
        PackageVersion: $ReleaseNumber-CI.$BuildNumber
    );
    
    DotNet::Build ~\Src\$PackageName
    (
        Configuration: Release
    );

Other methods utilize a "nuspec" file to be created (`nuget.exe spec`), which you similarly edit using the `Replace-Text operation.

## Publishing and Attaching {#publishing data-title="Publishing and Attaching"}
Instead of capturing the build output as an artifact, you can use the `NuGet::PublishPackage` operation to publish it to a [package source](/docs/buildmaster/builds/packaging/package-sources) that you've configured:

    NuGet::Publish-Package ~\Src\$PackageName\bin\Release\$PackageName.$packageVersion.nupkg
    (
        Source: CIPackages,
        AttachToBuild: true
    );

A package source is essentially a ProGet feed URL with a [resource credential](/docs/buildmaster/administration/resource-credentials) (username/password).

The second parameter (`AttachToBuild`) indicates that a package reference will be associated with the build. Think of this as a pointer of source to the that was published; you can easily navigate to it from the UI, use it later in repackaging operations.

### Publishing to NuGet.org {#nugetorg data-title="Publishing to NuGet.org"}
To publish packages to [NuGet.org](https://www.nuget.org), you first need to create an account.  Once you have a NuGet account, you can publish packages to NuGet.org from BuildMaster by simply creating [resource credentials](/docs/buildmaster/administration/resource-credentials) pointing to NuGet.org.

## Repackaging NuGet Packages {#repackaging data-title="Repackaging NuGet Packages"}

[Repackaging](/docs/proget/advanced/repackaging) is used to create a new package from an existing package using exactly the same, verified content while maintaining the integrity of the original package and providing an audit trail to show when and why the repackaging occurred.

You can use the `ProGet::Repack-Package` operation to instruct your ProGet server to perform to repackage:

    ProGet::Repack-Package
    (
        Name: ProfitCalc,
        NewVersion: $PackageVersion(ProfitCalc, stable)-RC.$BuildNumber
    );

In this case, the `ProfitCalc` package is already attached to the build from an earlier `Publish-Package`, which means BuildMaster can simply use the referenced package application to publish.

## Prevent the Release of Applications with Unstable Package References {#blocking-prerelease-packages data-title="Blocking Unstable Packages"}

Unstable (pre-release) packages are important to use when testing a package's code prior to its release, but by definition they're not suitable for release. As such, you should not deploy applications that reference the pre-release packages you create.

BuildMaster can help with this by performing [an automated check](/docs/buildmaster/verification/pipelines/approvals-and-gates/automated-checks) against a variable that you set your application's build process using the `DotNet::Get-Dependencies` operation.

### Inedo's Use Case

To prevent us from accidentally releasing a product (such as Otter) with a pre-release version of one of our libraries (like InedoLib), we use the following OtterScript in our build plan to set the `$UnstableDependencies` variable, and then we test that variable prior to deploying to the Release stage.

    set %prereleasePackages = %();
    
    foreach $projPath in @FileMask(**.csproj)
    {
        DotNet::Get-Dependencies
        (
            ProjectPath: $projPath,
            Dependencies => %nugets
        );

        foreach $k in @MapKeys(%nugets)
        {
            set $v = %nugets[$k];

            if $MatchesRegex($v, -)
            {
                set %prereleasePackages[$k] = $v;
            }
        }
    }
	
    set $buildVar = none;

    set @preList = @();

    foreach $k in @MapKeys(%prereleasePackages)
    {
        set @preList = $ListInsert(@preList, $k $(%prereleasePackages[$k]));
    }

    if $ListCount(@preList) != 0
    {
        set $buildVar = $Join(', ', @preList);
    }

    Set-BuildVariable UnstableDependencies
    (
        Value: $buildVar
    );

