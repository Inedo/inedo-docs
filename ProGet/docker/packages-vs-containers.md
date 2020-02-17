---
title: Packages vs Containers
subtitle: Packages vs Containers
sequence: 300
keywords: proget, docker, containers
show-headings-in-nav: true
---

Packages are generally simple: they are essentially an archive (i.e. zip file) that contains contents (code libraries, application executables, etc.) and a manifest file (json document, xml file, etc) that describes those contents with a package name and version number (at a minimum). Because everything is contained within the package file itself, the name of the package file doesn't matter; while the file name should reflect the name and the version, it doesn't need to. Everything you need to know about a package is contained within the contents itself.

Package versioning is equally simple: packages are uniquely identified by a name and version, which allows for not only simple identification, but ordering and comparison to determine which is latest, which is newer, etc. Because they are stored in the manifest file (i.e. within the package itself), the package's name and version are an integral part of the package, and are as immutable (unchanging) as its contents.


## Container Image Basics {#image-basics data-title="Container Image Basics"}

Container images are also simple, but they're more like an archive (i.e. a zip file) than a package. While container images do contain a manifest file, that manifest is essentailly like a zip file's internal directory index (i.e. the instructions for how to reconstruct a directory of files from a bunch of compressed bytes).

Container images don't have names; instead, they are identified by a "cryptographic hash" of their contents. This is a 64-character string that, to a human reader, may as well be a bunch of random numbers and letters. In other words, it's like a bunch of zip files named things like `40510175845988f13f6162ed8526f0b09f73384467fa855e1e79b44a56562a58.zip`. To make container images useful to humans, a "tag" is created to alias those cryptographic hashes. It's basically the same way that a file system works; a file's name is not a part of the file itself (but the file system's meta data), and that file system has that name point to a bunch of bytes on disk.

:::attention {.best-practice}
Tags are like file names; you can rename files at any time, and the file name has no relation to the contents
:::

And like file systems with "hard links", you can have multiple tags point to the same container image.

## Container Image Versioning {#image-versioning data-title="Container Image Versioning"}

Just like a bunch of random files on disk, container images are unversioned. It's up the publisher to determine which container images have which tags, and when those tags change. 

For example, some publishers may use `[v6]` to refer to the last version, whereas another may use `[4.2-stable]` to indicate a non-beta version of a container image. And then there's tags like `[latest-2]`, `[old-1]`, `[new-test11-new-new]`, etc., which are exactly what one might expect when using the file system for versioning instead of packages.

Many publishers use a form of semantic versioning when tagging images (such as `[4.3.0]`, `[2.3.4-latest]`, etc., but mutable nature of tags means that a publisher can also change `[hdards-3.2.4]` (which appears to be a specific version) to point to any container image, at any time. 

Because publishers can change their tags at any time, this leads to lots of confusion when containers versions change depending on the day you install them. Ultimately this leads to lots of lost productivity and even production failures in organizations when the wrong containers are tested and deployed. 

:::attention {.best-practice}
![](/resources/images/icons/best-practices.png)Best practice: treat tags like semantic version numbers, and use Multiple Registries for Maintaining Quality-Because you can't rely on a tag to reference a specific container image, this means you can't be assured that the container you tested is the container that will be deployed in production.  As such, you should use multiple registries, and have a "trusted" registry that only limited personnel may push images to.
:::

## Private Registries & Container Naming {#container-naming data-title="Private Registries & Container Naming"}
One of Docker design quirk is the peculiar relationship between a container's name (technically, the "repository name") and private container registries. Unless you specify the server's network address (IP or DNS name) when building, pulling, or pushing containers, Docker assumes that the image is hosted at `hub.docker.org`.

For example, `proget.kramerica.local\registry-name\kramerica\my-app-name` is the only way to reference a container image that's not hosted at `hub.docker.org`.

This is the *only* way to use Docker with a private registry. When you build, push, or pull an image, the Docker client will search for a `.` or a `:` in the first part of the repository name (i.e. before the first `/`). If neither of those characters exist, then, Docker will use `hub.docker.com`; otherwise, Docker pulls or pushes to the server address specified in that first part.

This behavior is important to note, because unlike package managers, there isn't an option when you build, pull, or push container images that instructs Docker to "use this private registry instead of the Docker hub".  

:::attention {.best-practice}
**Note:** when creating your own container images, you must use the server address and registry name each and every time you reference the container image (which is always).
:::

While it's unfortunate that you need to always specify the source, ProGet will help quite a bit by ignoring the first part of the repository name (i.e. the server address) and treating the second part as the registry (feed) name. This way, if you change ProGet's server address or rename your registry, your repositories will work the same. 

## Registry Mirroring {#registry-mirroring data-title="Registry Mirroring"}
There is the ability to "mirror" `hub.docker.org`, but this feature was really only designed to enable Docker, Inc. to host public registries in sensitive regions like China (so you can mirror to `registry.docker-cn.com` instead). 

Unfortunately, the Docker client will only use the configured "mirror" for building and pulling; images are always pushed to `hub.docker.org`. 

Docker, Inc. does not seem to be interested in enabling this, and we aren't particularly keen on forking the Docker client to enable this. Thus, it seems there's no sense in ProGet supporting this anytime soon... but feel free to [discuss this on the forums](https://forums.inedo.com/).