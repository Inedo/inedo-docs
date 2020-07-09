---
title: Package Consumers (Usage Tracking)
subtitle: Track Applications & Components Consuming Your Package
sequence: 600
keywords: BuildMaster, manual
show-headings-in-nav: true
---

Packages (e.g., NuGet, npm, PyPI, etc.) are often built using *other* packages. This usage is noted as a "dependency" in that package's manifest file. Modern applications are often built with dozens (or even hundreds) of packages, and each of those can be built with any number of packages themselves. This means that the actual packages that make up the bulk of the shipped code in any given application may change by simply rebuilding the application, even if you don't change a *single line* of your own code.

After building your application (and resolving all of those complicated dependencies), ProGet's Package Consumers feature will use [pgscan](#pgscan) to scan the build output, search for the specific package versions consumed by the application, and publish that data to ProGet along with your application's name and version.

This gives you, the package author, invaluable insight into which specific versions of applications or components are consuming specific versions of your packages. If you discover a critical bug or security vulnerability, you can quickly identify the consumers and fix them or notify responsible parties, or simply delete it if it's not in use.

### Automatically Scan with pgscan {#pgscan data-title="Automatically Scan with pgscan"}

[pgscan](https://github.com/Inedo/pgscan) is a simple, open-source, command-line tool for publishing dependencies used by a package when it is built.

### Viewing Consumers {#view-consumers data-title="Viewing Consumers"}

Package consumers are listed under the Usage & Statistics tab for the package. Note that those with ProGet Free licenses can only add these records manually.

#### Using pgscan for .NET applications

pgscan can be added to an OtterScript plan in BuildMaster to publish this information:

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
