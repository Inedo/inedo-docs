---
title: "[Outdated] Creating an Extension using the SDK 1.x"
order: 3
---

:::(Error) (👴 Outdated Content)
This applies to pre-2022 versions of our products, i.e. BuildMaster 7.0, ProGet 6.0, Otter 3.0, and earlier. See [Creating an Extension using the SDK](/docs/inedosdk/extending-inedo-tools-using-the-sdk/inedosdk-extending-creating) for an updated guide.
:::
An Inedo Extension is a .NET library that implements certain abstract classes defined in the Inedo.SDK library, and is packaged in a zip file.

There are two ways to get started with the Inedo SDK:
- Install the [Inedo.SDK NuGet package from nuget.org](https://www.nuget.org/packages/Inedo.SDK/) into your .NET project
- Download the assemblies and reference them manually in your .NET project

This documentation assumes you're using Visual Studio and NuGet to create an extension. For a complete type reference of the SDK, please visit the [Inedo SDK Reference](https://inedo.com/support/sdk-reference/inedosdk).

## Creating the Project

First, create a new class library project in Visual Studio, and select the appropriate target based on the edition for which you want to create an extension:

 * Windows edition (. NET Framework 4.5.2)
 * Linux (Docker) edition (. NET 5)

Then add a reference to the **Inedo.SDK** package using NuGet.

:::(Info)
Make sure that the SDK assemblies added by the NuGet package have the Copy Local property set to False. SDK assemblies should not be included in the extension.
:::


## Creating and Adding Components 

After creating your project, you can start creating classes that inherit from extensible components.

See [Writing a Simple Create File Operation.](/docs/inedosdk/extending-inedo-tools-using-the-sdk/inedosdk-extending-writing){target="_blank"}

## Building and Deploying the Extension 

Once you have added all the components you want and your project is compiled, you can package it as an extension and deploy it to your Inedo product.

### Direct Deployment 
An extension can be directly deployed to an Inedo product's extension root as a simple [universal package](/docs/proget/upack/upack-universal-packages){target="_blank"}. 

To do this for an extension named `MyExample`, first create a manifest file called `upack.json` with the following contents:

```
{
  "name": "MyExample",
  "version": "1.0.1"
}
```

Then, create a zip file called `MyExample.zip` with the compiled output in a `package` folder, and the `upack.json` at the root, like this:


```
  /package/
     /MyExample.dll
     /SomeLibrary.dll
  
  /upack.json
```

Rename the the zip file to `MyExample.upack`, and copy the `.upack` file into the Product extensions directory. By default, this will usually be in `C:\ProgramData\«product-name»\Extensions`, but you can verify the exact location by going to the Admin > All Settings page and looking for the `ExtensionsPath` value.

Finally, restart the product services (and application pool if hosting in IIS).

### Extension Gallery Deployment 

You can also package extensions in a [universal package](/docs/proget/packages/what-is-a-package#universal-package), deploy them to a ProGet feed, and configure your Inedo product to use your private feed instead. Such a package has three metadata requirements:

- `group` - must be "inedox"
- `name` - must be the same name as the assembly name
- `_inedoSdkVersion` - this additional property must be present, and a string with the three-party version number of the Inedo SDK used to build the extension

You can also specify a property named `_inedoProducts` that is an array comprised of:

- *string* values; `BuildMaster`, `Otter`, or `ProGet`
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

## Verifying and Testing the Extension

On the Admin > Extensions page, you should now see your custom extension. Clicking on it will show all the types that are loaded by the product. These types should appear in the appropriate places in the software.

## Multi-targeted Extensions 

As of Inedo SDK 1.9, many of our own extensions are "multi-targeted", which means that they work in both the Windows edition (. NET Framework 4.5.2) and the cross-platform edition (. NET 5+) of our products.

We don't recommend this for your own extensions, as you're unlikely to use both editions simultaneously in your organization, and building multi-targeted extensions adds complexity.

To multi-target an extension, simply package the built outputs for each edition into the `net452` and `net5.0` folders under the root path:

```
  /package/
     /net452 
        /MyExample.dll
        /SomeLibrary.dll
     /net5.0
        /MyExample.dll
        /SomeLibrary.dll

  /upack.json
```

If those folders exist when the extension is unpackaged, then the appropriate folder will be used instead of the root folder. 

You can also add a `_targetFrameworks` array to the metadata file, which will be used to filter what's shown on the Extensions page.

```
{
  "group": "inedox",
  "name": "MyExample",
  "version": "1.0.1",
  "_inedoSdkVersion": "1.9.0",
  "_inedoProducts": [ "ProGet" ],
  "_targetFrameworks":["net452","net5.0"]
}