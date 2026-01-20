---
title: "Importing/Downloading from Other Feeds"
order: 4
---

Feed Importers, available in version 6.0.6, allows any package in a remote feed (optionally filtered with Connector Filters) to be downloaded to a local feed. It is intended for migration and is not supported for public open-source repositories, which have an extremely large number of packages.

You can access this feature by navigating to the dropdown and selecting "Import Packages" in a local feed (e.g. `internal-nuget`).

![Feed Import](/resources/docs/proget-importpackages.png){height="" width="50%"}

Then select "Download Package From Another Service" to connect to a remote feed.

![Remote Feed Connect](/resources/docs/proget-downloadpackage-azure.png){height="" width="50%"}

## Feed Support

Feed importers are intended to work with any remote repository (not just ProGet), so they are limited to using the operations available for each repository type to list and download packages. For this reason, some feed types are not supported because they have no way to retrieve a list of all packages.

### Supported Feed Types

•	NuGet
•	Chocolatey
•	PowerShell
•	npm
•	PyPI
•	RubyGems
•	Universal
•	Vsix
•	RPM
•	Conda
•	Docker
•	Debian
•	Maven
•	Helm
•	CRAN
•	Dart/Flutter
•	Cargo
•	Composer
•	Conan
•	Alpine
•	Terraform

### Unsupported Feed Types

•	Bower

## Technical Details Implementation

Feed importers are implemented as a generic version that performs the following tasks regardless of the type of feed:

1.	Fetch a list of all packages to download
2.	Download and install each package

Since there can be a large number of packages, the fetch step can take a lot of time, although it should generally take much less time than the actual downloads. To reduce the use of server resources, the fetch step stores information about each package to be downloaded in local temporary storage.

Once the packages have been fetched, a progress bar is displayed because ProGet now knows how many packages it will download. The list of packages retrieved in the first step will be downloaded one by one until either all packages have been downloaded or the user cancels the execution.