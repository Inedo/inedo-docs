---
title: Secure Resources
sequence: 310
---

To help keep track of externally-hosted packages and container images, BuildMaster uses **Secure Resources**. They essentially represent external repository, such as a ProGet server hosting a Universal or NuGet package feed, or a ProGet server hosting a private Docker container registry.

### Creating a Secure Resources

1. Go to the _Administration > Secure Resources_ page.
2. Click the "Create Secure Resource" button.
3. Select the Secure Resource Type
4. Provide a unique name for the package source and supply the required information for that resource type.
5. If the repository requires authentication, create secure credentials, and specify them in the same dialog.

### Using Package Sources and Container Sources

Operations that work with Universal packages, NuGet packages, or Docker container images have a **Source** property that accepts the name of a secure resource. Specifying a source provides visibility for these packages and containers created and/or used by a build, and directly links the appropriate repository on the build overview page in BuildMaster. 

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

