---
title: "Extending ProGet"
order: 2
---

Some of ProGet's functionality is provided by extensions, a kind of pluggable component library that provides both core functionality and integrations with third-party tools. You can see which extensions are loaded, as well as install new or update extensions from the Admin > Extensions Page from your instance of ProGet.

## Built-in and Required Extensions

ProGet ships with the following extensions: [Inedo.SDK](/docs/inedosdk), [InedoCore](https://proget.inedo.com/feeds/Extensions/inedox/InedoCore), Amazon AWS, Google Cloud, and Azure.

Although we recommend installing all of these built-in extensions, Inedo.SDK and InedoCore are the only ones required. If they are not installed, you will receive warnings at UI, and the software will lack core functionality. If you have configured cloud storage, these feeds are not functional.

## New Extensions and Updates 

ProGet will automatically connect to the [proget.inedo.com/feeds/Extensions](https://proget.inedo.com/feeds/Extensions) feed, and notify you of newly available extensions and updates.  You can download and install these from the same extensions overview page.

:::(INFO)

A connection to inedo.com is recommended, but not required. If your ProGet instance cannot connect to [https://proget.inedo.com/upack/Extensions](https://proget.inedo.com/feeds/Extensions), then you will need to manually install and update extensions.
  :::

## Creating Your Own Extensions

With the Inedo SDK, you can create your own ProGet extension. Read the following documents to get started:


- [Inedo SDK Overview](/docs/inedosdk)
- [Creating an Extension using the SDK](/docs/inedosdk/extending-inedo-tools-using-the-sdk/inedosdk-extending-creating)
- Infragistics' example: [Using ProGet: Package Level Filtering for Infragistics Customers](https://www.infragistics.com/community/blogs/b/infragistics/posts/using-project-package-level-filtering-for-infragistics-customers)

To install your custom extension, follow the [Offline Extension Installation](#offline-install) steps.

## Manual Installation 

For installations where access to the Internet is restricted, or if you want to install a version other than the latest version, extensions can also be installed manually.

### Default Extension Installation

If your product installation can access the Internet (specifically the URL https://proget.inedo.com/upack/Extensions), simply navigate to the *Admin* > *Extensions* page to search for and install the latest available extension versions.

### Offline or Previous Version Extension Installation 

1. Download the desired version of the extension from the ProGet public feed. _Note: The ProGet public feed includes the version in the filename. You must remove this version in step 3. For example, 'InedoCore-1.7.11.upack" must become "InedoCore.upack'._
2. Copy the extension file (e.g. 'Sonatype.upack') to the extension path. Visit the *Admin* > *Advanced Settings* page within ProGet to determine where the extensions are stored by checking the 'Extensions.ExtensionsPath' setting.
3. Make sure that the file name in the extensions directory matches the '.upack' format and that no other files with the same name exist in that directory, even with a different extension. For example, make sure that you delete an existing 'Windows.inedox' file when you install "Windows.upack
4. Restart the Windows service of ProGet (INEDOPROGETSVC): *Admin* > *Service* > Click *Restart*
5. Restart the ProGet web application, either the IIS application pool or the integrated web server service: *Admin* > *Service* > *Restart Web App*
6. Verify that the new extension has been loaded by making sure it is listed in the table on the *Admin* > *Extensions* page

### Pre-Releases

To access pre-release versions of this extension, you can configure ProGet to use a feed for pre-release extensions. To configure your product installation to point to the pre-release feed, visit the *Admin* > *Advanced Settings* page within the product installation and set the `Extensions.UpdateFeedUrl` value to:

```
https://proget.inedo.com/upack/PrereleaseExtensions
```

This feed has a connector configured to the default URL (i.e. `https://proget.inedo.com/upack/Extensions`), so if there are no pre-release versions, the latest stable version will also be available.