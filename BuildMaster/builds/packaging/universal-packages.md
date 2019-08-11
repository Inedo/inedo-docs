---
title: Universal Packages
subtitle: Create and Deploy Universal Packages
Docs URL: builds/packaging/universal-packages
sequence: 100
keywords: upack, universal-packages, buildmaster, packages, build, deploy
---

BuildMaster can create and manage build artifacts for any deployment target, from simple ZIP files to complex container orchestrations in the cloud. In some cases you may want to deploy a [Universal Package](https://inedo.com/docs/upack/universal-packages/package-format) to a ProGet feed. This documentation will describe exactly how to accomplish this. 

###Universal Package Format
The Universal Package format ([UPack](https://inedo.com/upack)) is very simple, and can be used to package applications and components built with any technology: .NET web applications, NodeJS applications, Windows services, plug-ins for your applications, system configuration scripts, and so on. It's designed for both general-purpose use, and as a platform for creating a new proprietary package format. UPack is a technology-neutral packaging platform that allows you to uniformly distribute your applications and components across environments to enable consistent deployment and testing.


###Create a Universal Package.
To create a Universal Package you will need to:
1. In your BuildMaster plan add an [Create Package](https://inedo.com/docs/buildmaster/reference/operations/general/create-package) `Create-Package` operation.
2. Enter all requried fields

Sample Plan:
```
Create-Package
(
    Output: <applicationName>,
    From: C:\Project\,
    Include: **,
    Exclude: ...,
    Group: <group>,
    Name: $applicationName,
    Version: $releaseNumber,
    Metadata: %(...)
);
```

###Deploy a Universal Package to a ProGet Feed.
To Deploy your newly created Universal Package to a ProGet feed you'll need to complete the following tasks.
1. In the ProGet administration area (administration/api-keys) you will need to create a new [api key](https://inedo.com/docs/proget/administration/security/api-keys) with _Grant access to Feed Management API_ selected. 
2. In the BuildMaster administration area (/administration/credentials) add a new _Inedo Product_ credential and fill in all the required information that pertains and use your API key from ProGet that was created in step 1.
3. In your BuildMaster plan add a [Push Package](https://inedo.com/docs/buildmaster/reference/operations/proget/push-package) `ProGet::Push-Package` operation and use the name of the credentials created in step2. The remaining fields should be customized to the package you've created in the Execute Process operation above. 

Sample Plan:
```
ProGet::Push-Package
(
    Credentials: ProGet,
    Feed: Universal,
    FilePath: C:\temp\$applicationName-$releaseNumber.upack,
    Name: $applicationName,
    Version: $releaseNumber
);
```

Additional Options:
- Group, Package, Version: A universal package can be uniquely identified it's group, name, and version. These are different properties in the [manifest file](https://inedo.com/docs/upack/universal-packages/metacontent-guidance/manifest-specification).	
- Description - Describe the contents of this package. This field supports [Markdown syntax](https://www.markdownguide.org/basic-syntax/).
- Title	- Full Title of this package used when displaying a package and not part of its unique identification. 
- Icon - A string of an absolute url pointing to an image to be displayed in the ProGet UI (at both 64px and 128px); if package:// is used as the protocol, ProGet will search within the package and serve that image instead
- Dependencies: Dependencies should be supplied as a list, each consisting of a package identification string; this string is formatted as follows: «group»:«package-name»
«group»:«package-name»:«version» When the version is not specified, the latest is used.
- ProGet server URL: in lieu of the values in credentials	
- ProGet user name: The name of a user in ProGet that can access the specified feed.
- ProGet password: The password of a user in ProGet that can access the specified feed.





