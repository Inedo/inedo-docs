---
title: ProGet-hosted Universal Packages
subtitle: Create and Publish Universal Packages
sequence: 100
---

You can use BuildMaster to package your application in a [Universal Package](/docs/upack/universal-packages/package-format) and deploy it to a feed in ProGet. Universal Packages are essentially a zip files, but they contains a manifest file that describes the contents. 

<!--
{.attention .analogy} For a complete example of how to use Universal Packages in BuildMaster, just create a new application using the "CI/CD for Universal Packages" template in your BuildMaster instance, or browse the [UniversalPackageCiCD](https://buildmaster.inedo.com/applications/XXX/) application in our public instance of BuildMaster.
-->

The main advantage to using Universal Packages instead of BuildMaster-hosted artifacts are to have self-contained application or component packages that may be distributed and deployed without BuildMaster. 

### About Universal Package Format
The Universal Package format ([UPack](https://inedo.com/upack)) is very simple, and can be used to package applications and components built with any technology: .NET web applications, NodeJS applications, Windows services, plug-ins for your applications, system configuration scripts, and so on. 

### How to Create a Universal Package
The [`Create-Package`](/docs/buildmaster/reference/operations/proget/create-package) will build a universal package file. For example, to create a package file (ProfitCalc-$ReleaseNumber.upack) in the working directory from the contents of the working directory, you can just do this: 

```
ProGet::Create-Package
(
    Name: ProfitCalc,
    Version: $ReleaseNumber
);
```

### Push a Universal Package to a ProGet Feed
After creating a [package source](package-sources), you can simply use the [`ProGet::Push-Package`](/docs/buildmaster/reference/operations/proget/push-package) operation to push the package to that feed. For example, to push a newly-created package, you can do this:
```
ProGet::Push-Package
(
    PackageSource: ApplicationPackages,
    FilePath: ProfitCalc-$ReleaseNumber.upack
);
```
