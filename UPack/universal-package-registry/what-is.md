---
title: What is the Universal Package Registry?
subtitle: What is the Universal Package Registry?
sequence: 10
keywords: proget, romp, universal-packages
show-headings-in-nav: true
---
[Universal Feeds and Packages](/support/documentation/proget/core-concepts/packages#universal-package) are "lightweight" and, on their own, have very few built-in features. This design has allowed them to be utilized for all sorts of packaging problems such as application delivery, Inedo's product extensions, and even private Bower packages.

When developing a package-based solution, one question that often comes up is "what packages are installed or used in this particular context?" That's where the Universal Package Registry comes in; it's a local package registry designed specifically for Universal Packages.

## Background: Other Local Package Registries {#background data-title="Background: Other Local Package Registries"}

All packages, [universal format](/support/documentation/upack/universal-packages/package-format) or otherwise, are essentially zip files with content, (i.e. the actual files you want packaged) and a metadata file that describes the content. Once the contents of a package are extracted and *installed* to a directory, there’s no easy way to know which package those contents came from, or if they came from a package at all. This is where a local package registry comes in.

Different package managers use different mechanisms to represent a local registry. Some store the entire package, others store only metadata about the package, and each approach has advantages and disadvantages.

For example:

{.docs}
- The local registry for MSI (Microsoft Installer) is comprised of an installation cache directory (containing the MSI files installed) as well as various entries in the Windows registry.
-   The NuGet local registry is comprised of various package cache directories and a packages.config file stored in the root of a project file that describe which packages are used.
- APT (Advanced Package Tool for Debian) maintains a directory of package-related files in /var/lib/dpkg/info, including MD5sums of each of various installed files and the executable scripts that would be run before and after installation.

However, in all cases, the *packages registered* vs *packages installed* are not enforced by the package manager or operating system. You can consolidate the data in the local registry if needed, or delete files that were originally installed by the package manager.

## Designing the Universal Package Registry {#designing data-title="Designing the Universal Package Registry"}

The Universal Package Registry was designed with the realities of other local package registries in mind.

{.docs}
- **Well-defined specification** - this allows a variety of tools (not just an API or CLI) to read or write data
- **Data only** - there is no attempt to automatically reconcile *registered* vs *installed* packages
- **Compliance-driven metadata** - provides a common mechanism to indicate what installed a package and why
- **Any additional metadata** - for compliance or other reasons, you can add additional package metadata

A Universal Package Registry may also contain a package cache. There are three common uses cases for this:

{.docs}
- **Auditing** - to verify or compare the original contents of the package registered versus the files installed on disk
- **Repairing installation** - in the event an installed package was *corrupted* (a file in the install location was modified or deleted), the package could be reinstalled without needing to connect to a universal feed
- **Staging packages** - packages files could be copied to the cache ahead of installation to allow for rapid deployment of newer versions
