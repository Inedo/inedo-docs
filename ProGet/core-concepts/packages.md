---
title: Packages
subtitle: Packages in ProGet
sequence: 100
keywords: proget, packages
show-headings-in-nav: true
---
Packages help you uniformly distribute your applications and components.

::: attention {.analogy}

![](/resources/images/icons/analogy.png)

Think of a package like a uniform-sized shipping box with uniform, machine- and human-readable labels describing the package. Inside the box are the things you want to deliver, and the box may even include assembly, installation, delivery or other instructions on what to do with the contents.
:::

Packages have become a unifying concept across a DevOps toolchain because they are built once and deployed consistently across environments. This means everyone can be certain that what goes to production, is exactly what was built and tested.

<iframe width="600" height="337" src="https://www.youtube.com/embed/Znflf98ahzQ" frameborder="0" allowfullscreen="true"></iframe>

## Package Formats: Universal and Third-party {#package-formats data-title="Package Formats: Universal and Third-party"}

There's not a whole lot to a package: it's just a zip file containing the files you actually want to distribute, as well as a manifest file that describes the package itself. The specific layout of the zip file and manifest is referred to as a package format. ProGet supports the [universal package format](/support/documentation/upack/universal-packages/package-format), as well as a variety of third-party formats.

### Universal Package Format {#universal-package data-title="Universal Package Format"}

The Universal Package format is very simple, and can be used to package applications and components built with any technology: ASP.NET websites, NodeJS applications, Windows services, plug-ins for your applications, system configuration scripts, and so on. It's designed for both general-purpose use, and as a platform for creating a new proprietary package format. You can also extend a universal package's manifest file with additional metadata (and then search using that metadata).

### Third-party Package Formats {#third-party data-title="Third-party Package Formats"}

Third-party package formats were each designed with a very specific use case in mind and, as such, are more complex. For example, the NuGet package format was designed specifically for open source .NET libraries used by developers, and must take into account multiple versions of the .NET framework and dependency trees with multi-platform targeting.

While you could certainly package .NET libraries in a universal package, they wouldn't be usable by Visual Studio's NuGet plugin, or any of the existing NuGet tooling. Although you could use the [UPack tooling](/upack) instead, because they aren't designed specifically for .NET libraries, they won't offer as rich of a user experience as the NuGet tooling.

::: attention{.best-practice}

![](/resources/images/icons/best-practices.png)

**Best practice**: if there is a third-party package format designed for your specific use case, consider it instead of universal packages. But for most purposes, universal packages provide the best combination of simplicity, utility, and extensibility.
:::

## Creating and Publishing Packages {#create-package data-title="Creating and Publishing Packages"}

There are a lot of options for creating and publishing universal packages to ProGet, either from a developer's workstation, a build server, or anywhere else:

{.docs}
- [Universal Package Explorer](/support/documentation/upack/upe)
- [Command-line Interface](/support/documentation/upack/upack-cli)   
- [UPackLib.NET library](/support/documentation/upack/upacklib-net)
- [Inedo ProGet Jenkins Plugin](/den/proget/jenkinsplugin)
- [Push to ProGet Visual Sutdio Extension](/den/proget/visualstudio)
- Upload hand-crafted package files from the UI
- Simple HTTP Post with your own tool/scripts using the [Universal Feed API](/support/documentation/upack/feed-api/endpoints)

To learn how to create and package using a third-party package format, refer to the appropriate third-party feed documentation.

<iframe width="600" height="337" src="https://www.youtube.com/embed/ylb0AGnr6AA" frameborder="0" allowfullscreen="true"></iframe>

## Package Identification and Verification {#package-id data-title="Package Identification and Verification"}

One of the most important aspects of a package is that it is uniquely identifiable using a name and version. This simple, human-readable identification is what makes packages so easy to distribute and consume.

For example, “HDars-API 1.0.4” is version 1.0.4 of HDars-API, which is newer than “HDars-API 1.0.2”, older than “HDars-API 1.3.0”, and different than “HDars-Web 1.0.4”. “HDars-API” by itself is fairly meaningless, because it could refer to any version of HDars-API.

Universal packages (as well as some third-party packages) use the [SemVer specification](http://www.semver.org) to describe the version number.

<iframe width="600" height="337" src="https://www.youtube.com/embed/Si3eWq1yHXs" frameborder="0" allowfullscreen="true"></iframe>
