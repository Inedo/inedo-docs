---
title: Extending ProGet
sequence: 120
keywords: proget, packages
show-headings-in-nav: true
---

Some of ProGet's functionality is delivered through extensions, a type of pluggable component library that provides both core capabilities as well as integrations with third-party tools. You can see what extensions are loaded, as well as install new or upgrade extensions from the Admin > Extensions Page from within your instance of ProGet.

## Built-in and Required Extensions {#built-in data-title="Built-in Extensions"}

ProGet ships with the following extensions: [Inedo.SDK](/docs/various/inedo-sdk/the-sdk), [InedoCore](https://proget.inedo.com/feeds/Extensions/inedox/InedoCore), [Amazon AWS](https://proget.inedo.com/feeds/Extensions/inedox/AWS), and [Azure](https://proget.inedo.com/feeds/Extensions/inedox/Azure).

Although we recommend keeping all of these built-in extensions installed, Inedo.SDK and InedoCore are the only ones that are required. When they are not installed, you will receive warnings in the UI, and the software will be missing core functionality. If you've configured cloud storage those feeds will be inoperable.

## New Extensions and Updates {#extensions-updates data-title="New Extensions and Updates"}

ProGet will automatically connect to the [proget.inedo.com/feeds/Extensions](https://proget.inedo.com/feeds/Extensions) feed, and notify you of newly available extensions and updates.  You can download and install these from the same extensions overview page.

:::attention {.analogy}

  A connection to inedo.com is recommended, but not required. If your ProGet instance cannot connect to [https://proget.inedo.com/feeds/Extensions](https://proget.inedo.com/feeds/Extensions), then you will need to manually install and update extensions.
  :::

## Creating Your Own Extensions {#creating-extension data-title="Creating your Own Extensions"}

With the Inedo SDK, you can create your own ProGet extension. Check out the following documents to get started:

{.docs}
- [Inedo SDK Overview](/docs/various/inedo-sdk/the-sdk)
- [Creating an Extension using the SDK](/docs/various/inedo-sdk/creating)
- Infragistics' example: [Using ProGet: Package Level Filtering for Infragistics Customers](https://www.infragistics.com/community/blogs/b/infragistics/posts/using-project-package-level-filtering-for-infragistics-customers)

To install your custom extension, follow the [Offline Extension Installation](#offline-install) steps.

## Manual Installation {#manual-install data-title="Manual Installation"}

For installations that are restricted from accessing the internet, or if you wish to install a version other than the latest, extensions can also be installed manually.

### Default Extension Installation

If your product installation can access the internet (specifically the https://proget.inedo.com/feeds/Extensions URL), simply navigate to the *Admin* > *Extensions* page to browse and install the latest available extension versions.

### Offline or Previous Version Extension Installation {#offline-install}

1. Download the desired version of the extension from the public ProGet feed
2. Copy the extension file (e.g. `Sonatype.upack`) to the extensions path. Visit the *Admin* > *Advanced Settings* page within ProGet to determine where extensions are stored by examining the `Extensions.ExtensionsPath` setting.
3. Ensure the file name follows the format `<extension-name>.upack` in the extensions directory, and that no other files with the same name exist in that directory, even with a different extension. For example, make sure to delete an existing `Windows.inedox` file if you are installing `Windows.upack`
4. Restart ProGet's Windows service (INEDOPROGETSVC): *Admin* > *Service* > click *Restart*
5. Restart the ProGet web application, either the IIS application pool or the Integrated Web Server service: *Admin* > *Service* > *Restart Web App*
6. Verify that the new extension has been loaded by ensuring it's listed in the table on the *Admin* > *Extensions* page

### Pre-Releases

To access prerelease versions of this extension, you can configure ProGet to use a prerelease extensions feed. To configure your product installation to point to the prerelease feed, visit the *Admin* > *Advanced Settings* page within the product installation and set the `Extensions.UpdateFeedUrl` value to:

```
https://proget.inedo.com/feeds/PrereleaseExtensions
```

This feed has a connector configured to the default URL (i.e. `https://proget.inedo.com/feeds/Extensions`), so if there are no prerelease versions, the latest stable version will also be available.
