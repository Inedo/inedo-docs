---
title: Package Sources
subtitle: Access and Publish to Package Repositories Consistently
Docs URL: builds/packaging/package-sources
sequence: 200
keywords: buildmaster, packages, build, package-sources
---

A Package Source represents an external package repository, such as a ProGet server hosting a Universal or NuGet package feed.

### Creating a Package Source

1. Go to the Admin->Package & Container Sources page.
2. Click the "Create Package Source" button.
3. Provide a unique name for the package source, and supply its repository URL. Note that this is the package API URL, not the public "browse" URL. To get this URL in ProGet, visit the feed's page and copy the value from the API Endpoint URL textbox.
4. If the repository requires authentication, create Username & Password credentials, and specify them in the same dialog.

### Using a Package Source

Operations that work with Universal and NuGet packages have a **Source** property that accepts the name of a configured Package Source. Specifying a package source provides visibility
for packages created and/or used by a build, and directly links the package's repository on the build overview page in BuildMaster. The simplest way to add a package to a build is to use the
`Attach-Package` operation:

```
Packages::Attach-Package
(
	Name: <packageName>
	Version: [packageVersion]
	Source: <packageSource>
);
```

This operation verifies that the specified package exists in the package source, and associates it with the current build.

### Viewing Build Packages

Packages attached to a build are displayed on the build overview page in BuildMaster.

### Deleting/Deactivating Build Packages

A build package may be *deactivated* by clicking on the small delete button next to its name on the build overview page. A deactivated package is essentially deleted, and will
not be considered or displayed in BuildMaster in most cases. Once deactivated, clicking the delete button again will delete the package attachment permanently. Note that this
does **not** delete the package itself from its external repository.

