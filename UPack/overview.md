---
title: UPack Overview
subtitle: UPack Overview
sequence: 10
keywords: upack

---
UPack is a technology-neutral packaging platform that allows you to uniformly distribute your applications and components across environments to enable consistent deployment and testing.

[![](http://img.youtube.com/vi/Xq12ahmisDk/0.jpg)](https://www.youtube.com/embed/Xq12ahmisDk_blank)

## Versioning {#versioning data-title="Versioning"}

Universal Package Feeds were designed to be provide immediate, out-of-the-box functionality for most packaging needs, and also to serve as a platform that you can easily extend or interact with.

### Universal Feed API Versions {#feed data-title="Universal Feed API Versions"}

| Version           | Notes |
| ------------------|--------------- |
| 1.2.0             |Added Feed Metadata Endpoint, Added Virtual Packages Specification, Added `tags` to packages, Added Repackaging, Added package hash to package identification string (dependencies, package history)|         
| 1.1.1             | Increased `group` length from 50 to 250 (ProGet 5.1) |
| 1.1.0             | Added Download Package File Endpoint (ProGet 4.9) |
| 1.0.0             | Introduction (ProGet 4.0) |

### Universal Package Versions {#package data-title= "Universal Package Versions"}

The Universal Package Files themselves are designed to be both backwards- and forwards-compatible. This is easily accomplished because the minimum requirements for what makes a universal package are incredibly minimal. Thus, changes will always be additive and their usage will be optional.

[![](http://img.youtube.com/vi/Si3eWq1yHXs/0.jpg)](https://www.youtube.com/embed/Si3eWq1yHXs_blank)

In the extremely unlikely event that a future version of this specification is not compatible (we can think of absolutely no reason this would ever be), these would likely be exposed as a totally different format (like a .upack2 extension).

## UPack Specifications {#specifications data-title="UPack Specifications"}

All of the UPack tools adhere to the following set of specifications. The specifications were designed to be easy to understand and implement for your own use cases.

### [Universal Packages](/support/documentation/upack/universal-packages/package-format) {#upackages data-title="[Universal Packages](/support/documentation/upack/universal-packages/package-format)"}

  A ZIP archive containing any sort of content, along with a simple, JSON-formatted manifest file describing that content using built-in or additional metadata.

### [Virtual Packages](/support/documentation/upack/virtual-packages/what-is) {#virtual data-title="[Virtual Packages](/support/documentation/upack/virtual-packages/what-is)"}

A JSON-formatted file that behaves like a universal package, but with contents that are downloaded and assembled at install time.

### [Universal Package Registry](/support/documentation/upack/universal-package-registry/what-is) {#registry data-title="[Universal Package Registry](/support/documentation/upack/universal-package-registry/what-is)"}
A JSON-formatted file that describes what packages are installed in a particular context (on a server, in an application), as well as when it was installed, who installed it, and why it was installed. It also provides for a package cache, in case packages need to be reinstalled or audited.

### [Universal Feed](/support/documentation/upack/feed-api) {#feed data-title="[Universal Feed](/support/documentation/upack/feed-api)"}

An HTTP-based API used to list, download, and publish universal package files to a web-based package manager like ProGet.
