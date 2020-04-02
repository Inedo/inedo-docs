---
title: Package & Container Sources
sequence: 300
---

To help keep track of externally-hosted packages and container images, BuildMaster uses **Package Sources** and **Container Sources**. They essentially represent external repository, such as a ProGet server hosting a Universal or NuGet package feed, or a ProGet server hosting a private Docker container registry.

{.attention .best-practice} Starting in BuildMaster 6.2, [Secure Resources](secure-resources) have taken the place of package and container sources.

### Creating a Package Source or Container Source

1. Go to the Admin->Package & Container Sources page.
2. Click the "Create Package Source" button.
3. Provide a unique name for the package source, and supply its repository URL. Note that this is the package API URL, not the public "browse" URL. To get this URL in ProGet, visit the feed's page and copy the value from the API Endpoint URL textbox.
4. If the repository requires authentication, create Username & Password credentials, and specify them in the same dialog.

You can create a Container Source in the same manner, but using the "Create Container Source" button.

### Using Package Sources and Container Sources

Operations that work with Universal packages, NuGet packages, or Docker container images have a **Source** property that accepts the name of a configured Package Source or Container Source. Specifying a source provides visibility for these packages and containers created and/or used by a build, and directly links the appropriate repository on the build overview page in BuildMaster. 

The simplest way to add a package to a build is to use the `Attach-Package` operation:

```
Packages::Attach-Package
(
	Name: <packageName>
	Version: [packageVersion]
	Source: <packageSource>
);
```

This operation verifies that the specified package exists in the package source, and associates it with the current build. The `Containers::Attach-Container` operation works in the same manner.

### Viewing Packages & Containers on Builds

Packages and Containers attached to a build are displayed on the build overview page in BuildMaster.

### Deleting/Deactivating Packages & Containers

A package or container associated with a build may be *deactivated* by clicking on the small delete button next to its name on the build overview page. 

A deactivated package or container is essentially deleted, and will not be considered or displayed in BuildMaster in most cases. Once deactivated, clicking the delete button again will delete the package or container attachment permanently. 

Note that this does **not** delete the package or container image itself from its external repository.

