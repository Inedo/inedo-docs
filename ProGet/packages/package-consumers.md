---
title: Package Consumers (Usage Tracking)
subtitle: Package Consumers
sequence: 600
show-headings-in-nav: true
---

Starting in ProGet 5.3.0, the Package Consumers feature lets you track what applications and components are consuming specific DLL package versions.

Package Consumers uses [pgscan](#pgscan) or [an API connection](#API) to let you know precisely where any given package is being consumed. This gives you invaluable insight into which specific versions of applications or components are consuming specific versions of your packages. If you discover a critical bug or security vulnerability, you can quickly identify the consumers and fix them or notify responsible parties, or simply delete it if it's not in use.

Inside ProGet, Package Consumers are listed under the "Usage & Statistics" tab for each package and are configured and viewed there.

## Purpose {#purpose data-title="About Package Consumers"}

NuGet, npm, PyPI, and other package types are often built using other packages. These "dependencies" are noted in that package's manifest file. Modern applications are often built with dozens (or even hundreds) of packages, and each of those can be built with any number of packages themselves. This means that the actual packages that make up the bulk of the shipped code in any given application may change by simply rebuilding the application, even if you don't change a *single line* of your own code.

After building your application (and resolving all of those complicated dependencies), ProGet's Package Consumers feature uses [pgscan](#pgscan) to scan the build output, search for the specific package versions consumed by the application, and publish that data to ProGet along with your application's name and version.

## Limitations in the Free Edition {#free data-title="Limitations in Free"} 

Those using the ProGet Free edition can only add records manually.

## Automatically Scan with pgscan {#pgscan data-title="Automatically Scan with pgscan"}

[pgscan](https://github.com/Inedo/pgscan) is a simple, open-source, command-line tool for publishing dependencies used by a package when it is built. 

ProGet can use pgscan for [NuGet](/docs/proget/feeds/nuget), [npm](/docs/proget/feeds/npm), and [PyPI](/docs/proget/feeds/pypi) packages. You can extend package consumers further [via the API](#API).

### Using pgscan for .NET applications

Azure DevOps users can execute pgscan using the [Command Line Task](https://docs.microsoft.com/en-us/azure/devops/pipelines/tasks/utility/command-line?view=azure-devops&tabs=yaml) immediately after building the project. 

In [BuildMaster](https://inedo.com/buildmaster/), pgscan can be added to an OtterScript plan to publish the following information:

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

## Register Consumers via the Deployments API {#API data-title="Register Consumers via API"}

You can also use the API endpoint to register package consumers, extending the feature to meet your needs.

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
