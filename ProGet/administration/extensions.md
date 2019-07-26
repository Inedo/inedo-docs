---
title: Extending ProGet
sequence: 120
keywords: proget, packages
---

Some of ProGet's functionality is delivered through extensions, a type of pluggable component library that provides both core capabilities as well as integrations with third-party tools. You can see what extensions are loaded, as well as install new or upgrade extensions from the Admin > Extensions Page from within your instance of ProGet.

### Built-in and Required Extensions

ProGet ships with the following extensions: [InedoCore](/den/proget/inedocore), [Amazon](/den/proget/amazon), [Azure](/den/proget/azure), [VorSecurity](/den/proget/vorsecurity), [Whitesource](/den/proget/whitesource).

Although we recommend keeping all of these built-in extensions installed, InedoCore is the only one that's required. When it's not installed, you will receive warnings in the UI, and the software will be missing core functionality. If you've configured cloud storage, or a Whitesource or Vor account, those feeds will be inoperable.

## New Extensions and Updates

ProGet will automatically connect to the [inedo.com/den](/den), and notify you of newly available extensions and updates.  You can download and install these from the same extensions overview page.

:::attention {.analogy}

  A connection to inedo.com is recommended, but not required. If your ProGet instance cannot connect to [inedo.com/den](/den), then you will need to manually install and update extensions.
  :::

### Creating your Own Extension

With the Inedo SDK, you can create your own ProGet extension. Check out the following documents to get started:

{.docs}
- [Inedo SDK Overview](/docs/various/inedo-sdk/the-sdk)
- [Creating an Extension using the SDK](/docs/various/inedo-sdk/creating)

## Extensions in ProGet v4

If you'd like to create an extension that supports ProGet 4.8 or earlier, you'll need to use the ProGet SDK. Although extensions created with the ProGet SDK are not compatible with ProGet 5.0 and later, they are managed in the same way (under Admin > Extensions).

]Creating an extension is quite similar too: just create a .NET 4.5 project that references the [Inedo.ProGet.SDK](/packages/Inedo.ProGet.SDK)NuGet package from nuget.org, and implement the desired extensibility points [PackageStore](/support/sdk-reference/proget/Inedo.ProGet.Extensibility.PackageStores/PackageStore), [PackageFilter](/support/sdk-reference/proget/Inedo.ProGet.Extensibility.PackageFilters/PackageFilter), or [UserDirectoryBase](/support/sdk-reference/proget/Inedo.ProGet.Extensibility.UserDirectories/UserDirectoryBase).

See the [ProGet v4 SDK Reference](/support/sdk-reference/proget) and the [Sample ProGet v4 Extension](https://github.com/Inedo/SampleProGetExtension) for more information on how these extensibility points are used.

To deploy the extension to ProGet v4, add the compiled extension .dll to a zip file of the same name, with the extension .progetx. Note that the .progetx file must have the same file name as the primary assembly it contains; for example, MyExtension.progetx must contain MyExtension.dll.

You can verify that your extension has loaded correctly by viewing the Extensions Overview page from within the ProGet v4 Administration section. From there you will see all of the extensions that are loaded in ProGet v4 and the services they provide.
