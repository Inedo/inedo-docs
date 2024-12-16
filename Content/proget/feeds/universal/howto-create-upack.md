---
title: "HOWTO: Create Universal Packages"
order: 5
---

[Universal Packages](/docs/proget/feeds/universal/universal-packages) are a versatile way to bundle files such as programs, plugins, libraries, configuration files, or other content. Each package includes a manifest file, which provides a unique name and version, along with optional details about its contents.

In this guide, we'll look at how to create a Universal Package through ProGet's UI, which will also include creating a Universal Feed to host the package. Creating a Universal Package through the UI will let you individually select the files you want to bundle into the package, as well as create the manifest and upload the package to a ProGet feed. 

## Step 1: Create a Universal Feed

To host your Universal Package, you will need to create a Universal Feed. Navigate to "Feeds" and select "Create New Feed". Then select "Universal Packages".

![](/resources/docs/  .png){height="" width="50%"}

From here, select "No Connectors", and then name your feed. In this guide we will name our feed `internal-universal`.

![](/resources/docs/  .png){height="" width="50%"}

Now select "Create Feed". You will be directed to your new Universal Feed, currently empty.

![](/resources/docs/  .png){height="" width="50%"}

## Step 2: Create a Universal Package

To create and upload a Universal Package, navigate to your `internal-universal` feed, and select "Add Package" from the drop-down menu.

![](/resources/docs/  .png){height="" width="50%"}

Select "Create a Package" and then enter the `name` and `version` of the package. You can also optionally enter a group and/or description.

![](/resources/docs/  .png){height="" width="50%"}

Now you can select the files you wish to bundle into the Universal Package either by selecting them through the file input or dragging and dropping them into the window. When you have selected all of the files for the Universal Package, select "Create Package".

![](/resources/docs/  .png){height="" width="50%"}

Your package will now be uploaded to your `internal-universal` feed. 

![](/resources/docs/  .png){height="" width="50%"}

### Alternative: Creating with CLI

Instead of creating a Universal Package through the UI, you can also use one of the following tools:

- [`upack`](https://github.com/Inedo/upack/releases): A cross-platform command-line tool to create and install universal packages
- [`Inedo.UPack`](https://www.nuget.org/packages/Inedo.UPack): A .NET library to create, read, and install packages on a server

## Step 3: (Optional) Editing Your Universal Package

The manifest file in Universal Packages hosted in a feed can be edited through the UI. Navigate to the Universal Package's page and select "Edit Package".

![](/resources/docs/  .png){height="" width="50%"}

From here you can edit the package manifest through three tabs:

- **General**: Edit the package title and description
- **Display Options**: Add a short description, edit the project URL, and assign the package an icon
- **Advanced**: Add tags, declare dependencies, and add a custom key/value pair.

![](/resources/docs/  .png){height="" width="50%"}

## Step 4: (Optional) Downloading Universal Packages and Package Content

When downloading a Universal Package, you have two possible options:

* Download a Package in a Universal Package format, including it's metadata
* Download the only the files packaged in a Universal Package as a `zip` file. 

Both can be performed by navigating to the Universal Package's page and selecting either "Download Package" or "Download Contents as Zip". 

![](/resources/docs/  .png){height="" width="50%"}