---
title: "Customizing Usage/Installation Instructions"
order: 2
---

Starting with ProGet v6, feed usage and package installation instructions can be customized by administrators.

## Feed Usage Instructions

Feed usage instructions are details on how to set up your client tools to work with this feed. Feed usage instructions can be customized for each feed in ProGet. You can create a custom instruction, duplicate an existing instruction, hide an instruction, or delete a custom instruction. The usage instructions are displayed in the multi-button at the top of the page, where the feed's packages are listed.

### Create a Custom Usage Instruction

To create a usage instruction for a feed:
1. Navigate to your feed
1. Click the _Manage Feed_ button in the upper right corner
1. Click the _create_ link to the right of _Feed Usage Instructions_
1. Enter the Title 
1. Enter the Instructions using MarkDown syntax
1. Click Save

The title will be shown in the multi-button on the package list page. You can also duplicate an existing instruction by clicking on the instruction in the manage feed page and then clicking the _Duplicate_ button in the bottom left corner.

### Hiding/Showing a Usage Instructions

Usage instructions can be hidden from users. This is useful if you only want to use custom usage instructions instead of using the built-in instructions.

You can hide a usage instruction on the Manage Feed page by clicking the red "X" to the right of the instruction, or open the instruction by clicking the title and then checking the `Hide from Users` option.

You can show a hidden usage instruction by clicking the undo button to the right of the instruction or open the instruction by clicking on the title and then unchecking the `Hide from Users` option.


### Deleting a Usage Instruction

You can delete a custom instruction by clicking on the red "X" to the right of a hidden instruction.

:::(INFO)
Built-in usage instructions can only be hidden, they cannot be deleted.
:::

## Package Installation Instructions

Package installation instructions are details on how to install a particular package from your feed. Starting with ProGet v6, you can now customize package installation instructions for each feed. Package installation instructions are displayed on the package detail pages. You can create a custom instruction, duplicate an existing instruction, hide any instruction, or delete a custom instruction.

### Create a Custom Installation Instruction

To create an installation instruction for a feed:
1. Navigate to your feed
1. Click the _Manage Feed_ button in the upper right corner
1. Click the _create_ link to the right of _Package Installation Instructions_
1. Enter the Title 
1. Enter the Instructions
1. Click Save

The title will be shown as a tab on the package details page. You can also duplicate an existing instruction by clicking on the instruction in the manage feed page and then clicking the _Duplicate_ button in the bottom left corner.

Instructions are written using MarkDown syntax and support a sub-set of [variables](#package-installation-instruction-variables).

### Hiding/Showing an Installation Instructions

Package installation instructions can be hidden from users. This is useful if you only want to use custom installation instructions instead of using the built-in instructions.

You can hide a package installation instruction on the Manage Feed page by clicking the red "X" to the right of the instruction, or open the instruction by clicking the title and then checking the `Hide from Users` option.

You can show a hidden package installation instruction by clicking the Undo button to the right of the instruction or open the instruction by clicking the title and then unchecking the `Hide from Users` option.


### Deleting an Installation Instruction

You can delete a custom package installation instruction by clicking the red "X" to the right of a hidden instruction.

:::(INFO)
Built-in package installation instructions can only be hidden, they cannot be deleted.
:::

<h3 id="package-installation-instruction-variables">Package Installation Instructions Variables</h3>

When customizing a package installation instruction, you can use MarkDown syntax for formatting and a specific set of variables. Included variables are:

| Variable | Supported Feed Types | Example |
|------|----| ---- |
| `$PackageName` | All (except Docker & Assets) | Inedo.SDK |
| `$PackageVersion` | All (except Bower, Docker, & Assets) | 1.13.0-ci.11 |
| `$PackageGroup` | Upack, Maven | InedoReleases |
| `$PackagePlatform` | RubyGems | Ruby |
| `$PackageFullName` | Upack | InedoReleases/ProGet |
| `$DownloadUrl` | Upack | https://proget.inedo.com/upack/Products/download/InedoReleases/ProGet/5.3.38 |
| `$PackageArchitecture` | Debian | amd64 |
| `$AddArchitectureCommand` | Debian | sudo dpkg --add-architecture amd64 |
| `$RepositoryName` | Docker | inedo/proget |
| `$TagName` | Docker | 6.0.0 |
| `$RegistryBaseUrl` |  Docker | proget.inedo.com/productimages/ |
| `$Path` | Assets | my-subdirectory/myfile.txt |
| `$Name` | Assets | my-file.txt |

Note that Assets are only supported in ProGet 2022+.