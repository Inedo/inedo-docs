---
title: "HOWTO: Create Universal Packages"
order: 5
---

[Universal Packages](/docs/proget/feeds/universal/universal-packages) are a versatile way to bundle files such as programs, plugins, libraries, configuration files, or other content. Each package includes a manifest file, which provides a unique name and version, along with optional details about its contents.

In this guide, we'll look at how to create a Universal Package through ProGet's UI, which will also include creating a Universal Feed to host the package. Creating a Universal Package through the UI will let you individually select the files you want to bundle into the package, as well as create the manifest and upload the package to a ProGet feed. 

## Step 1: Create a Universal Feed

To host your Universal Package, you will need to create a Universal Feed. Navigate to "Feeds" and select "Create New Feed". Then select "Universal Packages".

![](/resources/docs/proget-upack-createfeed.png){height="" width="50%"}

From here, select "No Connectors", and then name your feed. In this guide, we will name our feed `internal-universal`.

![](/resources/docs/proget-upack-createfeed-internal.png){height="" width="50%"}

Now select "Create Feed". You will be directed to your new Universal Feed, currently empty.

![](/resources/docs/proget-upack-feed-internal.png){height="" width="50%"}

## Step 2: Create a Universal Package

To create and upload a Universal Package, you can use the [pgutil](/docs/proget/api/pgutil) CLI tool in combination with the [Upload Universal Packages](/docs/proget/api/universal-feed/upload) endpoint in the [Universal Feed API](/docs/proget/api/universal-feed). pgutil will require some [minor configuration](/docs/proget/api/pgutil#sources) before use. 

Creating a package can be done using the `upack create` command. For example, if creating the package `myPackage` version `1.2.3` from files located in `.\package-files\myPackage`, and creating it in the directory `.\universal-packages`, you would enter:

```bash
$ pgutil upack create --name=myPackage --version=1.0.1 --source-directory=.\package-files\myPackage --target-directory=.\upacks
```

Once your package is created, use the `packages upload` command to upload it to your `internal-universal` feed:

```bash
$ pgutil packages upload --feed=internal-universal --input-file=.\upacks\MyPackage.1.0.1.upack
```

Your package will now be uploaded to your `internal-universal` feed. 

![](/resources/docs/proget-upack-feed-uploaded.png){height="" width="50%"}

### Alternative: Using the ProGet UI

Instead of using pgutil, you can use the ProGet UI to create and upload a package. Navigate to your `internal-universal` feed, and select "Add Package" from the drop-down menu.

![](/resources/docs/proget-upack-feed-addpackage.png){height="" width="50%"}

Select "Create a Package" and then enter the `name` and `version` of the package. You can also optionally enter a group and/or description.

![](/resources/docs/proget-upack-createpackage-details.png){height="" width="50%"}

Now you can select the files you wish to bundle into the Universal Package either by selecting them through the file input or dragging and dropping them into the window. When you have selected all of the files for the Universal Package, select "Create Package".

![](/resources/docs/proget-upack-createpackage.png){height="" width="50%"}

Your package will now be uploaded to your `internal-universal` feed. 

![](/resources/docs/proget-upack-feed-uploaded.png){height="" width="50%"}

## Step 3: (Optional) Editing Your Universal Package

The manifest file in Universal Packages hosted in a feed can be edited through the UI. Navigate to the Universal Package's page and select "Edit Package".

![](/resources/docs/proget-upack-package-edit.png){height="" width="50%"}

From here you can edit the package manifest through three tabs:

- **General**: Edit the package title and description
- **Display Options**: Add a short description, edit the project URL, and assign the package an icon
- **Advanced**: Add tags, declare dependencies, and add a custom key/value pair.

![](/resources/docs/proget-upack-edit-advanced.png){height="" width="50%"}

## Step 4: (Optional) Downloading Universal Packages and Package Content

When downloading a Universal Package, you have two possible options:

* Download a Package in a Universal Package format, including it's metadata
* Download only the files packaged in a Universal Package as a `zip` file. 

Both can be performed by navigating to the Universal Package's page and selecting either "Download Package" or "Download Contents as Zip". 

![](/resources/docs/proget-upack-package-download.png){height="" width="50%"}

## Step 5: (Optional) Installing a Universal Package

You can use [pgutil](/docs/proget/api/pgutil) to locally "install" or "unpack" Universal Packages using the `upack install` command. This will unpack the files to the specified location and create an entry in the Universal Package Registry:

```bash
$ pgutil upack install --package=myPackage --version=1.0.1 --feed=internal-universal --target=.\universal-packages\myPackage
```

The Universal Package Registry is a local system that will track and manage "installed" Universal Packages, creating a record of metadata like package name, version, installation date, installation path and the person that installed it. You can read more about the Universal Package Registry on the [Universal Feeds & Packages](/docs/proget/feeds/universal#upack-registry) page.

Once a Universal Package has been installed, pgutil offers several other commands to manage it:

* `upack list`: Will list all installed Universal Packages
* `upack update`: Will update the installation with another version from your `internal-universal` feed
* `upack remove`: Will uninstall the specified Universal Package