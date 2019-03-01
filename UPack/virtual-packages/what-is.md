---
title: What is a Virtual Package?
subtitle: What is a Virtual Package?
sequence: 10
keywords: proget, universal-packages, virtual
---
A virtual package is just like a regular package, except without any actual content (i.e. your application files and components). It can contain all of the same metadata that a regular package has, but the contents themselves are external to the package file.

Most of the time, you won't even know you're using a virtual package. They appear as regular packages in a feed (unless you look carefully at a metadata parameter), and they will download/install as a regular package, with all of the externally-referenced content included.

There are two main key usecases for virtual packages:

### Usecase: Bundling Multiple Packages

There are several instances where you may want to bundle a set of packages into a single, logical package.

:::attention {.best-practice}
![](/resources/images/icons/best-practices.png) For example, let's say you've built an application that is customized for six different customers by overwriting logos, javascript files, or any other resources within the application. The "base" application is totally unusable on its own for anything other than testing/QA purposes, and the "resources" are even more useless. They require being combined with a specific version of the "base" application to even be tested.
:::

This is easily solved with a virtual package that "bundles" a specific version of the base application (CrmAppBase) and the client customization (InitechApp) into a single, logical package (Initech.Crm).

When you deliver the package, it will be exactly what is needed. But you can easily see the versions/components used to assemble it by looking at the metadata.

### Usecase: Logical Packaging of External Files

Another way to use Virtual Packages is by pointing to well-known, external files.

:::attention {.best-practice}
![](/resources/images/icons/best-practices.png) For example, if your package will contain large, unchanging, multi-gigabyte assets (like movies, or map files), maintaining and transferring multiple versions of those packages will be slow and require a lot of disk resources. Instead, you can use file references to logically package these resources in a package.
:::

You can also bundle these files alongside your regular package contents through bundling.
