---
title: "Creating an Extension using the SDK"
order: 2
---

An Inedo Extension is a .NET library that implements certain abstract classes defined in the Inedo.SDK library, and is packaged in a universal package zip file.

:::(Info) (👉 PROTIP: Copy/Paste an Inedo Extension to Start)
Check out our [repositories on GitHub](https://github.com/inedo) to find the extension that's the most similar what you'd like. And then just copy/paste, then modify as needed. This is pretty much how we create new extensions ourselves.
:::

This documentation will assume you're using Visual Studio and NuGet to create an extension. For a complete type reference of the SDK, please visit the [Inedo SDK Reference](https://inedo.com/support/sdk-reference/inedosdk).

## Creating the Project

Create a new .NET 6 class library project in Visual Studio, then use NuGet to add a reference to the **Inedo.SDK** package.

:::(Info)
After adding the Inedo.SDK NuGet package, edit the `.csproj` file and make sure the Inedo.SDK `PackageReference` element has `ExcludeAssets` configured as follows:
````xml
<PackageReference Include="Inedo.SDK" Version="2.0.1" ExcludeAssets="runtime" />
````
This will prevent the Inedo.SDK assemblies themselves from being published as part of your extension.
:::


## Creating and Adding Components 

After creating your project, you can start creating classes that inherit from extensible components.

See [Writing a Simple Create File Operation.](/docs/inedosdk/extending-inedo-tools-using-the-sdk/inedosdk-extending-writing){target="_blank"}

## Building and Deploying the Extension 

Once you've added all the desired components, and your project compiles, it is now ready to be packaged as an extension, and deployed to your Inedo product.

### Using Inedo.ExtensionPackager
The easiest way to package your extension is to use the `inedo.extensionpackager` dotnet tool. Install it locally or globally using `dotnet tool install inedo.extensionpackager`. This is a command line tool that will build your extension with all required dependencies and create a .upack file with all required metadata. It will also do some basic validation and warn you if there is anything that might prevent the extension from loading. For example, if your extension `.csproj` file is in the `C:\MyExtensionSrc` directory:
````
C:\MyExtensionSrc> dotnet tool install inedo.extensionpackager
C:\MyExtensionSrc> dotnet inedoxpack pack --build Release
````
This will create `<ExtensionName>.upack` in the `C:\MyExtensionSrc` directory. This package can be copied into a product's extensions path directly, or it can be uploaded to a Universal Package Feed in ProGet.

## Verifying and Testing the Extension

On the Admin > Extensions page, you should now see your custom extension. If you click on it, you will be shown all the types that are loaded by the product. These types should appear in the appropriate parts of the software.
