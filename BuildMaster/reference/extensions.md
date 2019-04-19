---
title: Extending BuildMaster
subtitle: BuildMaster Extensions
keywords: buildmaster
sequence: 50
show-headings-in-nav: true
---

A lot of BuildMaster’s functionality is delivered through extensions, a type of pluggable component library that provides both core capabilities and integrations with third-party tools. You can see what extensions are loaded, install new extensions, or upgrade extensions from the Admin > Extensions Page within your instance of BuildMaster.  

## Built-in Extensions {#built-in data-title="Built-in Extensions"}

BuildMaster ships with three required extensions: *InedoCore*, *Windows*, and *Linux*. When these are not installed, you will receive warnings in the UI, and the software will be missing core functionality.

## New Extensions and Updates {#extensions-updates data-title="New Extensions and Updates"}

BuildMaster will automatically connect to the [Inedo Den](/den), and notify you of newly available extensions and updates. You can download and install these from the same extensions overview page.

:::attention {.best-practice}
A connection to inedo.com is recommended, but not required. If your BuildMaster instance cannot connect to inedo.com/den, then you will need to manually install and update extensions. Like folders, projects require a unique name within their containing project. But aside from that, there are no restrictions as to what kinds of contents a project can contain. This means you ought to be considerate when creating your project structure, and specially with nested, sub-projects.
:::

## Creating your Own Extensions {#creating-extension data-title="Creating your Own Extensions"}

You can extend BuildMaster's functionality by [creating an extension](/support/documentation/various/inedo-sdk/creating) that's built against the [Inedo SDK](/support/documentation/various/inedo-sdk/the-sdk). This extension can also be used in Inedo's other tools. Here's what you can extend in BuildMaster:

| Name | Description |
| ---- | ----------- |
| Credential | Information that is used to provide access to a particular resource (such as a name and password to a specific service), and may contain secrets (i.e. encrypted fields that are visible only to privileged users) |
| Operation | Used in an OtterScript plan to perform a high-level automation task, such as collecting information about a server, deploying files to a server, or ensuring a particular set of configuration |
| Raft Repository | A self-contained abstract file system that is used as a backing store for plans, assets, and other files. |
| User Directory | Responsible for authenticating users and groups. The default implementation uses users and groups stored in the product's database. |
| Variable Function | A function with zero or more input parameters that returns a value when evaluated by the execution engine, and often appears to be a "built-in" variable |
| Issue Source | Provides a filtered list of issues from an external source, such as Jira, for a given release context |

## Script Assets vs Custom Extensions {#script-assets data-title="Script Assets vs Custom Extensions"}

Scripts are significantly easier to develop and manage: just write, test, then upload, and your script will be available for execution in a plan as any other operation. For most things you'd need to do in a plan, that's perfectly adequate.

Extensions are a bit more involved, but allow you to build components other than operations, and provide maximum flexibility for build operations. You can also incorporate third-party libraries into your extension, and use the powerful libraries already built in to the Inedo SDK.

{.docs}
- **Direct Agent Access** - with components from [Inedo.Agents](/support/sdk-reference/inedosdk/Inedo.Agents), you can build Operations that will be faster and scale better than using scripting runtime.
- **Rich Descriptions** - the [Inedo.Documentation](/support/sdk-reference/inedosdk/Inedo.Documentation) components enable you to describe Operations in a plan using a consistent, visually-rich description so that anyone reading it will see, at a glance, what the operation is configured to do.

Additionally, extensions may be easier to unit test and mock using the testing framework of your choice.

### Extensions in BuildMaster v5 {#v5 data-title="Extensions in BuildMaster v5"}

If you'd like to create an extension that supports BuildMaster 5.8 or earlier, you'll need to use the BuildMaster SDK. Although extensions created with the BuildMaster SDK are not compatible with BuildMaster 2.0 and later, they are managed in the same way (under Admin > Extensions).

Creating an extension is quite similar: just create a .NET 4.5 project that references the Inedo.BuildMaster.SDK NuGet package from nuget.org and implement the desired extensibility points.

See the [BuildMaster v5 SDK Reference](/support/sdk-reference/buildmaster/) for more information.

To deploy the extension to BuildMaster v5, add the compiled extension .dll to a zip file of the same name, with the extension .bmx. note that the .bmx file must have the same file name as the primary assembly it contains; for example, MyExtension.bmx must contain MyExtension.dll

You can verify that your extension has loaded correctly by viewing the Extensions Overview page from within the BuildMaster v5 Administration section. From there, you will see all the extensions that are loaded in BuildMaster v5, and the services they provide.

## Legacy Components {#legacy-components data-title="Legacy Components"}

BuildMaster v5 and v6 have a lot of [legacy features](/support/kb/1144) from previous versions that remain functional but are generally hidden from new installations.

To extend the functionality of some of these features in BuildMaster v6, your extension will need to reference the [BuildMaster Legacy Components SDK NuGet package](https://www.nuget.org/packages/Inedo.BuildMaster.SDK/) instead of the Inedo SDK. This package references the Inedo SDK, so the resulting extension will still be considered a cross-product, Inedo extension.

The following legacy components are supported: Actions, Build Importers, Extension Configurers, Predicates, Source Control Providers, Issue Tracking Providers, Legacy Variable Functions.
