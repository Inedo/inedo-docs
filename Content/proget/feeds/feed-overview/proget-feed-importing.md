---
title: "Importing/Downloading from Other Feeds"
order: 4
---

:::(Warning)
This feature is currently considered experimental.
:::

Feed Importers, available in version 6.0.6, allows any package in a remote feed (optionally filtered with Connector Filters) to be downloaded to a local feed. It is intended for migration and is not supported for public open-source repositories, which have an extremely large number of packages.

You can access this feature by navigating to Manage Feed > Connectors & Replication > Connectors Used By This Feed and then clicking the "Import Icon" to the left of the delete icon.

The icon is only available for supported feed types and non-public repositories (e.g. nuget.org, npmjs.org).

## Feed Support

Feed downloaders are intended to work with any remote repository (not just ProGet), so they are limited to using the operations available for each repository type to list and download packages. For this reason, some feed types are not supported because they have no way to retrieve a list of all packages.

### Supported Feed Types

•	NuGet
•	Chocolatey
•	PowerShell
•	npm
•	PyPI
•	RubyGems
•	Universal
•	Vsix
•	RPM (supported indirectly as a separate execution, as RPM has no connectors)
•	Conda (Conda is not released as of writing this document, but will be supported)

### Unsupported Feed Types

Contact us if you are interested in this feature
•	Docker
•	Debian
•	Maven
•	Helm
•	Bower

## Technical Details Implementation

Feed downloaders are implemented as a generic version that performs the following tasks regardless of the type of feed:

1.	Fetch a list of all packages to download
2.	Download and install each package

Since there can be a large number of packages, the fetch step can take a lot of time, although it should generally take much less time than the actual downloads. To reduce the use of server resources, the fetch step stores information about each package to be downloaded in local temporary storage.

Once the packages have been fetched, a progress bar is displayed because ProGet now knows how many packages it will download. The list of packages retrieved in the first step will be downloaded one by one until either all packages have been downloaded or the user cancels the execution.