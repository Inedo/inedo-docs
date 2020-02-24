---
title: Extending BuildMaster
subtitle: BuildMaster Extensions
keywords: buildmaster
sequence: 50
show-headings-in-nav: true
---

A lot of BuildMaster's functionality is delivered through extensions, a type of pluggable component library that provides both core capabilities and integrations with third-party tools. You can see what extensions are loaded, install new extensions, or upgrade extensions from the *Admin* > *Extensions* page within your instance of BuildMaster.  

## Built-in Extensions {#built-in data-title="Built-in Extensions"}

BuildMaster ships with three required extensions: *InedoCore*, *Windows*, and *Linux*. When these are not installed, you will receive warnings in the UI, and the software will be missing core functionality.

## New Extensions and Updates {#extensions-updates data-title="New Extensions and Updates"}

BuildMaster will automatically connect to the [Inedo Den](/den), and notify you of newly available extensions and updates. You can download and install these from the same extensions overview page.

::: {.attention .best-practice}
A connection to inedo.com is recommended, but not required. If your BuildMaster instance cannot connect to proget.inedo.com, then you will need to [install and update extensions manually](#manual-install).
:::

## Creating Your Own Extensions {#creating-extension data-title="Creating your Own Extensions"}

You can extend BuildMaster's functionality by [creating an extension](/docs/various/inedo-sdk/creating) that's built against the [Inedo SDK](/docs/various/inedo-sdk/the-sdk). This extension can also be used in Inedo's other tools. Here's what you can extend in BuildMaster:

| Name | Description |
| ---- | ----------- |
| Secure Credential | Used to represent logins and access keys containing secrets (i.e. encrypted fields that are visible only to privileged users) |
| Secure Resource | Information that is used to provide access to a particular resource (such as a Git repository), and may be associated with a Secure Credential |
| Operation | Used in an OtterScript plan to perform a high-level automation task, such as collecting information about a server, deploying files to a server, or ensuring a particular set of configuration |
| Raft Repository | A self-contained abstract file system that is used as a backing store for plans, assets, and other files. |
| User Directory | Responsible for authenticating users and groups. The default implementation uses users and groups stored in the product's database. |
| Variable Function | A function with zero or more input parameters that returns a value when evaluated by the execution engine, and often appears to be a "built-in" variable |
| Issue Source | Provides a filtered list of issues from an external source, such as Jira, for a given release context |

Note that, prior to BuildMaster 6.2, "Resource Credentials" were used instead of Secure Credentials and Secure Resources. They will still function, but should not be used in new extensions.

## Script Assets vs. Custom Extensions {#script-assets data-title="Script Assets vs. Custom Extensions"}

Scripts are significantly easier to develop and manage: just write, test, then upload, and your script will be available for execution in a plan as any other operation. For most things you'd need to do in a plan, that's perfectly adequate.

Extensions are a bit more involved, but allow you to build components other than operations, and provide maximum flexibility for build operations. You can also incorporate third-party libraries into your extension, and use the powerful libraries already built in to the Inedo SDK.

{.docs}
 - **Direct Agent Access** - with components from [Inedo.Agents](/sdk/inedosdk/Inedo.Agents), you can build Operations that will be faster and scale better than using scripting runtime.
 - **Rich Descriptions** - the [Inedo.Documentation](/sdk/inedosdk/Inedo.Documentation) components enable you to describe Operations in a plan using a consistent, visually-rich description so that anyone reading it will see, at a glance, what the operation is configured to do.

Additionally, extensions may be easier to unit test and mock using the testing framework of your choice.

### Extensions in BuildMaster v5 {#v5 data-title="Extensions in BuildMaster v5"}

If you'd like to create an extension that supports BuildMaster 5.8 or earlier, you'll need to use the BuildMaster SDK. Although extensions created with the BuildMaster SDK are not compatible with BuildMaster 2.0 and later, they are managed in the same way (under Admin > Extensions).

Creating an extension is quite similar: just create a .NET 4.5 project that references the `Inedo.BuildMaster.SDK` NuGet package from nuget.org and implement the desired extensibility points.

See the [BuildMaster v5 SDK Reference](https://inedo.com/support/sdk-reference/buildmaster/) for more information.

To deploy the extension to BuildMaster v5, add the compiled extension .dll to a zip file of the same name, with the extension .bmx. note that the .bmx file must have the same file name as the primary assembly it contains; for example, MyExtension.bmx must contain MyExtension.dll

You can verify that your extension has loaded correctly by viewing the Extensions Overview page from within the BuildMaster v5 Administration section. From there, you will see all the extensions that are loaded in BuildMaster v5, and the services they provide.

## Legacy Components {#legacy-components data-title="Legacy Components"}

::: {.attention .technical}
All legacy components will be removed as of BuildMaster v6.2. Visit the [v6.2 Upgrade Notes](https://inedo.com/support/kb/1766/buildmaster-6-2-upgrade-notes) for more information.
:::

BuildMaster v5 and v6 have a lot of [legacy features](https://inedo.com/support/kb/1144/buildmaster-legacy-features) from previous versions that remain functional but are generally hidden from new installations.

To extend the functionality of some of these features in BuildMaster v6, your extension will need to reference the [BuildMaster Legacy Components SDK NuGet package](https://www.nuget.org/packages/Inedo.BuildMaster.SDK/) instead of the Inedo SDK. This package references the Inedo SDK, so the resulting extension will still be considered a cross-product, Inedo extension.

The following legacy components are supported: Actions, Build Importers, Extension Configurers, Predicates, Source Control Providers, Issue Tracking Providers, Legacy Variable Functions.

## Manual Installation {#manual-install data-title="Manual Installation"}

For installations that are restricted from accessing the internet, or if you wish to install a version other than the latest, extensions can also be installed manually.

### Default Extension Installation

If your product installation can access the internet (specifically the https://proget.inedo.com/upack/Extensions URL), simply navigate to the *Admin* > *Extensions* page to browse and install the latest available extension versions.

### Offline or Previous Version Extension Installation

1. Download the desired version of the extension from the public ProGet feed
2. Copy the extension file (e.g. `Windows.upack`) to the extensions path. Visit the *Admin* > *Advanced Settings* page within BuildMaster to determine where extensions are stored by examining the `Extensions.ExtensionsPath` setting.
3. Ensure the file name follows the format `<extension-name>.upack` in the extensions directory, and that no other files with the same name exist in that directory, even with a different extension. For example, make sure to delete an existing `Windows.inedox` file if you are installing `Windows.upack`
4. Restart BuildMaster's Windows service (INEDOBMSVC): *Admin* > *Service* > click *Restart*
5. Restart the BuildMaster web application, either the IIS application pool or the Integrated Web Server service: *Admin* > *Service* > *Restart Web App*
6. Verify that the new extension has been loaded by ensuring it's listed in the table on the *Admin* > *Extensions* page

### Pre-Releases

To access prerelease versions of this extension, you can configure BuildMaster to use a prerelease extensions feed. To configure your product installation to point to the prerelease feed, visit the *Admin* > *Advanced Settings* page within the product installation and set the `Extensions.UpdateFeedUrl` value to:

```
https://proget.inedo.com/upack/PrereleaseExtensions/
```

This feed has a connector configured to the default URL (i.e. `https://proget.inedo.com/upack/Extensions`), so if there are no prerelease versions, the latest stable version will also be available.
