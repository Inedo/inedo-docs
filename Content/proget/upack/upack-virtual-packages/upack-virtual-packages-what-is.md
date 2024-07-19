---
title: "What is a Virtual Package?"
order: 5
---

A virtual package is just like a regular package, but without the actual contents (i.e., your application files and components). It can contain the same metadata as a regular package, but the content itself is outside the package file.

In most cases, you won't even know you're using a virtual package. They appear as regular packages in a feed (unless you look closely at a metadata parameter), and they're downloaded/installed like a regular package, including any content referenced from outside.

There are two main use cases for virtual packages:

## Usecase: Bundling Multiple Packages

There are several cases where you may want to bundle a number of packages into a single, logical package.

:::(Info)
Let us say you have created an application that has been customized for six different clients by overwriting logos, javascript files, or other resources within the application. The "base application" is completely useless on its own, except for testing and quality assurance purposes, and the "resources" are even more useless. They must be combined with a specific version of the "base application" to be tested at all.
:::

This can be easily solved with a virtual package that "bundles" a specific version of the base application (CrmAppBase) and the client customization (InitechApp) into a single, logical package (Initech.Crm).

When you deliver the package, it will be exactly what is needed. However, you can easily see from the metadata which versions/components were used to assemble the package.

## Usecase: Logical Packaging of External Files

Another way to use Virtual Packages is to reference known external files.

:::(Info)
For example, if your package contains large, immutable, multi-gigabyte assets (such as movies or map files), maintaining and transferring multiple versions of these packages is slow and requires a lot of disk resources. Instead, you can use file references to logically pack these resources into one package.
:::

You can also bundle these files together with your regular package contents by bundling them.