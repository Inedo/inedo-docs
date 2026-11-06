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

You can extend ProGet's functionality by [creating an extension](/docs/inedosdk/extending-inedo-tools-using-the-sdk/inedosdk-extending-creating) that's built against the [Inedo SDK](/docs/inedosdk). This extension can also be used in Inedo's other tools. Here's what you can extend in ProGet:

| Name | Description |
| ---- | ----------- |
| Secure Credential | Used to represent logins and access keys that contain secrets (i.e. encrypted fields visible only to privileged users) |
| User Directory | Responsible for authenticating users and groups. The default implementation uses users and groups stored in the product's database. |
| Variable Function | A function with zero or more input parameters that returns a value when evaluated by the execution engine, and often appears to be a built-in variable |
| File System | Used by ProGet to access the underlying storage medium for package data. |

## Extension Installation/Upgrade Options

### Manual Installation 

For installations where access to the Internet is restricted, extensions can also be installed manually.

### Default Extension Installation

If your product installation can access the internet (specifically the `https://proget.inedo.com/upack/Extensions` URL), simply navigate to *Admin* > *Extensions*  to browse and install extensions.

### Previous Version Installation
Navigate to *Admin* > *Extensions* and click the extension you would like to roll back. Click *Change Version* in the dropdown list under the *Delete Extension* button.

### Upload Custom Extension
Navigate to *Admin* > *Extensions* and click the *Upload Custom Extension* button at the bottom of the page.

### Pre-Release Extensions
By default, ProGet is configured to use Inedo's Production Extension Feed, which contains only stable extension releases. If you want to use the pre-release feed instead (for example, if you are advised to try a pre-release extension to resolve an issue ), click the *Change Settings* button on the *Admin* > *Extensions* page and select *Inedo's pre-release feed* as the source for the extension.