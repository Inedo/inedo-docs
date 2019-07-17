---
title: Creating an Extension using the SDK
sequence: 20
keywords: inedo, inedo sdk
show-headings-in-nav: true
---

An Inedo Extension is a .NET library that implements certain abstract classes defined in the Inedo.SDK library, and is packaged in a zip file.

There are two ways to get started with the Inedo SDK:

{.docs}
- Install the [Inedo.SDK NuGet package from nuget.org](https://www.nuget.org/packages/Inedo.SDK/) into your .NET project
- Download the assemblies and reference them manually in your .NET project

This documentation will assume you're using Visual Studio and NuGet to create an extension. For a complete type reference of the SDK, please visit the [Inedo SDK Reference](/support/sdk-reference/inedosdk).

## Creating the Project {#creating-project data-title="Creating the Project"}

First, create a new class library project in Visual Studio, and target .NET 4.5.2. Use NuGet to add a reference to the **Inedo.SDK** package.


:::attention{.best-practice}
![](/resources/images/icons/best-practices.png)

Make sure that the SDK assemblies added by the NuGet package have the Copy Local property set to False. SDK assemblies should not be included in the extension.
:::

## Creating and Adding Components {#creating-adding data-title="Adding Components"}

After creating your project, you can start creating classes that inherit from extensible components.

See [Writing a Simple Create File Operation](writing).

## Building and Deploying the Extension {#building-deploying data-title="Building and Deploying"}

Once you've added all the desired components, and your project compiles, it is now ready to be packaged as an extension, and deployed to your Inedo product. There are two deployment options.

### Direct Deployment (Zip File) {#zip-file}

An extension can be directly deployed to an Inedo product's extension root as a simple zip file. To do this, create a zip file with the same name as the assembly, and with an `.inedox` extension.

:::attention {.analogy}
![](/resources/images/icons/analogy.png)

For example, if the assembly name is `MyExample.dll`, create a zip file called `MyExample.zip`. Next, add `MyExample.dll` file to the zip file, then rename the zip file to `MyExample.inedox`.
:::

Copy the `.inedox` file into the Product extensions directory. By default, this will usually be in `C:\ProgramData\«product-name»\Extensions`, but you can verify the exact location by going to the Admin->All Settings page and looking for the `ExtensionsPath` value.

Finally, restart the product services (and application pool if hosting in IIS).

### Extension Gallery Deployment (Universal Package) {#uni-package data-title="Extension Gallery Deployment"}

You can also package extensions in a [universal package](/docs/proget/core-concepts/packages#universal-package), deploy them to a ProGet feed, and configure your Inedo product to use your private feed instead. Such a package has three metadata requirements:

{.docs}
- `group` - must be "inedox"
- `name` - must be the same name as the assembly name
- `_inedoSdkVersion` - this additional property must be present, and a string with the three-party version number of the Inedo SDK used to build the extension

You can also specify property named `_inedoProducts` that is an array comprised of:

{.docs}
- *string* values; `BuildMaster`, `Otter`, `Hedgehog`, or `ProGet`
- *object* values;  a key/value pair with the product name and a string semantic version, representing the [SDK version compatibility](/docs/inedosdk/versions-release-notes#compatibility-ranges)

For example, the metadata for an assembly named `MyExample.dll` might look like this:

```
{
  "group": "inedox",
  "name": "MyExample",
  "version": "1.0.1",
  "_inedoSdkVersion": "1.4.0",
  "_inedoProducts": [ "BuildMaster", { "Otter": "1.2.0" } ]
}
```

  Note that the `_inedoSdkVersion` and `_inedoProducts` properties are only used for filtering what's shown on the Extensions page in the product. If the values are specified incorrectly, the extension may still be downloadable yet unloadable.

## Verifying and Testing the Extension {#verifying-testing data-title="Verifying and Testing the Extension"}

On the Admin > Extensions page, you should now see your custom extension. If you click on it, you will be shown all the types that are loaded by the product. These types should appear in the appropriate parts of the software.
