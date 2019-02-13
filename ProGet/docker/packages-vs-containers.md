---
title: Packages vs Containers
subtitle: Packages vs Containers
sequence: 200
keywords: proget, docker, containers
---
  Packages are simple: they are uniquely identified by a name and version, which allows for not only simple identification, but ordering and comparison to determine which is latest, which is newer, etc. A name and version are an integral part of the package, and are as immutable as the contents.

  Unfortunately, Docker did not design containers with versioning in mind; a container image is uniquely identified by a sha-256 cryptographic hash, and it's up to the image's author to identify which hashes represent which image. This is accomplished with tags: a human-readable string associated with a hash.

## Docker Tags are Not Immutable    

The challenge with tags is that they are ever-changing, and it's entirely up to the author to determine which tag points to which image. Most authors use a form of semantic versioning when tagging images: [4.3.0],[2.3.4-latest], etc.

However, because the Docker client doesn't "understand" versioning, there's no way to request a minor version (such as "4.3.* ") or even request the latest version.

To get around this limitation, authors will use a quasi-versioning scheme and " reuse " the same tag to identify a latest version; for example, [latest], [v5-latest], [4.3stable], [apache-stable-6] are all ways to get the latest version of a product or a release. The image those tags reference will change over time.

The mutable nature of tags means that an author can also change [hdards-3.2.4] (which appears to be a specific version) to point to any image, at any time.  

:::attention {.best-practice}
![](/resources/images/icons/best-practices.png)Best practice: Use Multiple Registries for Maintaining Quality-Because you can't rely on a tag to reference a specific container image, this means you can't be assured that the container you tested is the container that will be deployed in production.  As such, you should use multiple registries, and have a "trusted" registry that only limited personnel may push images to.
:::
