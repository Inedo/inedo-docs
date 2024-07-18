---
title: "Legacy Extensions"
order: 4
---


::: (ERROR)
All legacy components were removed in BuildMaster v6.2. Visit the [v6.2 Upgrade Notes](https://inedo.com/support/kb/1766/buildmaster-6-2-upgrade-notes) for more information.
:::

BuildMaster v5 and v6 have many [legacy features](https://inedo.com/support/kb/1144/buildmaster-legacy-features) from previous versions that continue to work, but are generally hidden from new installations.

To extend the functionality of some of these features in BuildMaster v6, your extension will need to reference the [BuildMaster Legacy Components SDK NuGet package](https://www.nuget.org/packages/Inedo.BuildMaster.SDK/) instead of the Inedo SDK. This package references the Inedo SDK, so the resulting extension is still considered a cross-product, Inedo extension.

The following legacy components are supported: Actions, Build Importers, Extension Configurers, Predicates, Source Control Providers, Issue Tracking Providers, Legacy Variable Functions.

### Extensions in BuildMaster v5 

If you want to create an extension that supports BuildMaster 5.8 or earlier, you must use the BuildMaster SDK. Although extensions created with the BuildMaster SDK are not compatible with BuildMaster 2.0 and later, they are managed in the same way (at Admin > Extensions).

Creating an extension is quite similar: simply create a .NET 4.5 project that references the `Inedo.BuildMaster.SDK` NuGet package from nuget.org and implement the extension points you want.

See [BuildMaster v5 SDK Reference](https://inedo.com/support/sdk-reference/buildmaster/) for more information.

To deploy the extension in BuildMaster v5, add the compiled .dll extension to a zip file of the same name with a .bmx extension. Note that the .bmx file must have the same filename as the primary assembly it contains; for example, MyExtension.bmx must contain MyExtension.dll

You can verify that your extension loaded correctly by going to the Extensions Overview page in the BuildMaster v5 Administration area. There you can see all the extensions that are loaded in BuildMaster v5 and the services they provide.
