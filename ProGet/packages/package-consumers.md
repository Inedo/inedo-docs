---
title: Package Consumers (Usage Tracking)
subtitle: Track Applications & Components Using Your Package
sequence: 600
keywords: BuildMaster, manual
show-headings-in-nav: true
---

Developer library packages (e.g. NuGet, npm, Pypi, etc.) are often built using *other* library packages, and this usage is noted as a "dependency" in that package's manifest file. For example, the popular [Moq library](https://www.nuget.org/packages/Moq/) has a dependency on the [Castle.Core library](https://www.nuget.org/packages/Castle.Core/), and hundreds of other library packages take dependencies on both of those.

These dependencies allow tools like Visual Studio to simplify library package consumption when you build your own applications and components. For example, if used Castle.Core in your application, but then later added Moq, you could potentially end up with two different and conflicting versions of the Castle.Core: the version that you're using, and the version that Moq requires. This is solved with "version ranges", and library authors will typically specify a wide range of versions when defining dependencies. For example, the [Moq 4.8.0 package](https://www.nuget.org/packages/Moq/4.8.0) will work with any version of Castle.Core that's greater than 4.2.1.

But things get quite a lot more complicated in the real world. Modern applications are often built with dozens (or even hundreds) of libraries, and each of those can be built with any number of libraries themselves. Resolving all of the versions across all of the dependencies is not a trivial (for example, see [Microsoft's NuGet Package Dependency Documentation](https://docs.microsoft.com/en-us/nuget/concepts/dependency-resolution)), and the results are often different, depending on what library packages are available at any given time.  This means, the actual libraries -- which make up the bulk of the shipped code in any given application -- may change by simply rebuilding the application; even if you don't change a single line of your own code.

This is where ProGet's Package Consumer feature comes in. After building your application (and resolving all of those complicated dependencies), ProGet's [pgscan tool](#pgscan) will scan the build output, search for the specific library package versions consumed by the application, then publish that data to ProGet, along with your application's name and version.

As a library author, this gives you invaluable insight into which specific applications or components versions are consuming specific versions of your library. If you discover a critical bug or security vulnerability in your library, you can quickly identify the consumers and fix them or notify the responsible parties. Or, if no one is consuming the bad version of your library, you can simply delete it; no need to keep around a potentially buggy or dangerous version.

### Automatically Scan with pgscan {#pgscan data-title="Automatically Scan with pgscan"}

[pgscan](https://github.com/Inedo/pgscan) is a simple open-source command-line tool for publishing dependencies used by a package when it is built.


#### Using pgscan for .NET applications

It can be trivially added to an OtterScript plan in BuildMaster to publish this information:

    # Build MyLibrary
    DotNet::Build MyLibrary.csproj
    (
        Configuration: Release
    );

    # Publish dependencies of MyLibrary to the proget.local server
    Exec
    (
        FileName: pgscan.exe
        Arguments: >> publish 
            --input=MyLibrary.csproj 
             --package-feed=Libraries 
            --proget-url=https://proget.local 
            --consumer-package-source=Libraries 
            --consumer-package-version=$ReleaseNumber >>
    );

### Register Consumers via the Deployments API {#pgscan data-title="Register Consumers via API"}

You can also use the API endpoint to register package consumers. 

```
POST /api/dependencies/dependents
{
  "feed": "shared-libraries",

  "packageName": "kramerica-lib",
  "groupName": "",
  "version": "1000.0.0",

  "dependentPackageName": "hdars-web",
  "dependentGroupName": "",
  "dependentVersion": "44.2.1",

  "comments": "This field *supports* markdown and [URLS](https://my-server/)"
}
```
