---
title: Semantic Versioning
sequence: 500
keywords: proget,feeds,docker,containers
show-headings-in-nav: true
---

In a Docker repository, container images are not versioned; instead, they are "tagged", and it's up the publisher to determine which container images have which tags, and when those tags change. For example, some publishers may use "v6" to refer to the last version, whereas another may use "4.2-stable" to indicate a non-beta version of a container.

To make matters worse, publishers can change their tags at any time, which has caused lots of confusion when containers versions change depending on the day you install them. Ultimately this leads to lots of lost productivity and even production failures in organizations when the wrong containers are tested and deployed.

This is where ProGet's Container Versioning feature comes in. Instead of allowing publishers to specify arbitrary version numbers, they are limited to using standardized version numbers and cannot change those versions once published. ProGet manages this behind the scenes by defining three types of tags:

- **Release Tags** are three-part SemVer-formatted tags (e.g. 3.4.1, 4.0.1, 2.4.1-beta.1, etc.) and are intended to be the permanent identifier of a particular container image. Think of them like a package version to a package; you can technically edit a package file and republish it under the same version, but that defeats the whole purpose of using version numbers to begin with. Like with packages, ProGet discourages this behavior and restricts retagging release tags to users with Package_Overwrite permission.

- **Virtual Tags** let consumers request the latest stable major or minor release of container. They are one- and two-part version number tags (e.g. 3.4 or 4) that ProGet automatically assigns to images when a container image is tagged with a stable release tag. For example, if you tag a container image with 3.4.1, ProGet will also tag that image with 3.4 and remove 3.4 from all other container images (such as 3.4.0).

- **Informational Tags** are a small set of pre-defined tags (e.g. latest, production, integration) that are defined by ProGet administrators and may be freely used by container publishers. By default, "latest" is the only allowed tag, and list should be kept relatively short.

Container Versioning is available paid versions of ProGet and may be enabled on a registry-by-registry basis. Enabling this will not impact container images stored in the registry; it's only enforced on tags applied through the API (i.e. tags applied via replicated repositories are not impacted).

