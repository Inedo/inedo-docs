---
title: "HOWTO: Bulk Import Packages using Drop Paths"
order: 6
---

ProGet users come into the software with several packages to import—whether from their local drive, a package hosting service, or an alternative private repository.

It's entirely possible to write a script to publish your package via the feed's API endpoints, but it's often easier to import these packages from a local disk or network. This ProGet feature configures a feed to use a local or network path that ProGet periodically scans for files to import.

In this guide, we will look at how to import packages in bulk into your ProGet feeds through a local or network path and how to troubleshoot common errors that can occur during the import process.

:::(Info)
If you are looking to migrate packages between two ProGet feeds, please see our [Migrating a ProGet feed](/docs/proget/feeds/feed-overview/proget-administration-migrating-a-proget-feed) guide.
:::

## Step 1: Create a bulk import folder on your server

In this guide, we will be importing from a folder named `bulk-import`, and importing it to a feed we have created named `nuget`.

On our server, we need to start by navigating to our drive and selecting a folder for the bulk import process. This can be an existing folder, or a new one.

Note: If you are using a folder on a server other than the one on which ProGet is installed, you must use the UNC path of your shared network folder.

:::(Warning) (Note about Drop Path Folder)
The ProGet service account must have full control (read and delete files) over this folder. This step is a good opportunity to configure this permission.
:::

![Name Folder "name-folder"](/resources/docs/server-folder-bulkimport.png){height="" width="50%"}

We will copy this folder path for later use.

## Step 2: Navigate to Add Packages

In ProGet, we start by navigating to "Feeds", and selecting our "NuGet" feed.

![Feed Select "feed-select"](/resources/docs/proget-feed-nugetselect.png){height="" width="50%"}

From here, we will select "Add Package" from the drop-down menu on the right of the page.

![Add Package "feed-select"](/resources/docs/proget-nuget-feed-addpackage.png){height="" width="50%"}

## Step 3: Select the package source<a id="step-3"></a>

For this example in which we stored our packages in a folder on our server, we will select "Bulk Import/File Copy":

![Bulk Import "bulk-import"](/resources/docs/proget-nuget-addpackage-bulkimport.png){height="" width="50%"}

From here, we'll need to enter the path of the folder in the "Disk path on server" box, which will autocomplete as the associated folder we referenced earlier is present on our server.

The following options can also be selected:

* **Search subdirectories of path** - When searching for packages in the specified path, Proget will additionally search in all of its subdirectories. This can be useful when your resource is particularly large with many nested directories.
* **Overwrite packages in feed** - This will permit Proget to automatically overwrite any existing packages that share the same name as those you are importing from your network or local path. This can be especially useful for older feeds in which multiple packages may have become outdated and require the latest versions.
* **Delete package files from path after import** - This will permit Proget to automatically delete any packages imported to the feed once the operation has been completed. When selecting this it is recommended that you have a backup of your packages.

Note that the appropriate permissions must be set for them to function.

![Bulk Import "bulk-import"](/resources/docs/proget-nuget-bulkpackageimport.png){height="" width="50%"}

Finally, we select "Import". The packages are imported successfully, and are now visible on our "NuGet" feed’s page.

![Imported "imported"](/resources/docs/proget-nuget-feed.png){height="" width="50%"}

## Troubleshooting

Some warnings or errors may occur during bulk import. You can view these by navigating to the "Settings" page of ProGet (you must be logged in as an administrator to view the settings).

From here, navigate to the "Additional Logs & Events" section located in the bottom right, and select "Executions".

![Admin "admin"](/resources/docs/proget-settings-executions.png){height="" width="50%"}

For warnings and errors associated with importing packages look for executions titled "Import Packages".

![Admin "admin"](/resources/docs/proget-executions-importerror.png){height="" width="50%"}

### Issue: “The Package Already Exists on the Feed”

This warning will display as such in the Execution Logs window:

```plaintext
WARN: The package Microsoft.Extensions.DependencyInjection.8.0.0-preview.3.23174.8 already exists and will not be installed because the current configuration does not support overwriting packages.
```

This warning means that the package already exists in the ProGet feed, so it will not be imported/installed a second time in ProGet.

There are several common reasons for this issue:

* The user is attempting to import a package that already exists in the feed
* Two identical packages exist in the import path with different file names, and one is attempting to overwrite the other during the same bulk import.
* The package file exists within two different folder locations, and one is attempting to overwrite the other during the same bulk import.

There are two ways to fix this warning.

#### Option 1: Delete the file from the import path

Deleting the file from the import path folder does not delete it from ProGet. ProGet has already imported the data and files to its server, so the information will not be lost.

You can check if a file is still accessible by navigating to Feed > Package > Package Version > Files

![ProGet CSharp Package](/resources/docs/proget-csharp-packagefilesview.png){height="" width="50%"}

#### Option 2: Allow overwriting (_not recommended_)

In general, we don't recommend this method, since most developers don't want to overwrite packages and instead version their packages.

However, if your organization is comfortable with overwriting packages, you can fix this warning by checking the box "Overwrite packages in feed" when importing packages (see [Step 3](#step-3))

### Issue: “An error occurred in the web application: Could not find file …”

This warning may occur if the package was deleted from the drive for some reason.

You will see this page when you navigate to the Files tab of the deleted package:

![ProGet could not find a file.](/resources/docs/bulkimportviadroppath-unexpectederror-missingfile.png){height="" width="50%"}

The error will display in the Diagnostic Center like so:

!["could not find file" error](/resources/docs/bulkimportviadroppath-serviceoutput-couldnotfinderror.png){height="" width="50%"}

ProGet stores package **data** on ProGet, but package **files** on disk. Since ProGet saved the package data, it might think that the package is still there, even though it's not; therefore, it won't import the package to reinstall it.

This situation is very unlikely, as it means that internal (possibly hidden) files on the hard disk have been deleted.

To solve the problem, delete the package in ProGet.

The Drop Path configuration will then re-import and install the package.

### Issue: "Error deleting imported package..."

This warning will display as such in the Execution Logs window:

```plaintext
WARN: Error deleting imported package C:\bulk-import\microsoft.extensions.dependencyinjection.8.0.0-preview.3.23174.8.nupkg from bulk import path.
```

This warning occurs when ProGet does not have permission to read/delete from the Drop Path folder on the server or network share.

You can resolve this warning by opening the properties of the import path on the server or locally, and navigating to the Security tab in the pop-up window. Then configure your security settings so that ProGet has full control over this folder.

After configuration, ProGet is now able to properly delete a package both in ProGet and on the server.

### Issue: "End of Central Directory record could not be found."

This warning occurs when Proget fails to read a package file from the import path.

This warning will display as such in the Execution Logs window:

```plaintext
WARN: Error installing package: End of Central Directory record could not be found.
```

This is usually because either the file is corrupt, or is not a genuine package file, such as being a foreign file type with the wrong extension.

To solve this problem, delete the file from the import path source, and reacquire it from a verified resource, such as [nuget.org](https://nuget.org)
