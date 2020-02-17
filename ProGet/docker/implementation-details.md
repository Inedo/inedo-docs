---
title: Docker Terminology & Concepts
sequence: 200
keywords: proget,feeds,docker,containers
show-headings-in-nav: true
---

While Docker users rarely need to worry about the implementation details, these may become important when you manage and troubleshoot a private Docker registry.

Here's a brief guide to help familiarize yourself with basic Docker terminology and concepts.

| Term| Description |
|---|---|
| Repository    | a named collection of _images_ and _tags_|
| Registry      | a host or server that stores Docker _repositories_|
| Image         | a _manifest_ and series of _layers_ described in the manifest; this is assembled by the Docker client and instantiated as a _container_|
| Layer         | either a _digest_ that references a _blob_ stored in the same registry, or a _url_ where a blob should be downloaded|
| Blob          | the low-level storage mechanism used for _layers_ and _manifests_, this is a collection of binary data that is referenced by its _digest (hash)_|
| Digest (hash) |the identification of a particular blob; this is just the hash of its contents|
| Container     | a runtime instance of an _image_|
| Tag           | a human-readable alias of a _digest (hash)_ that generally used to version _images_ within a _repository_|
| Manifest      | a JSON-based description of an _image_ and the _layers_ it contains; this is referenced by its _digest_ and stored as a _blob_|
| Dockerfile    | a file written in a procedural language that is used by the Docker client to construct an _image_; essentially, this is used to generate a _manifest_ |

Conceptually, a registry is like a feed, a repository is like a package name, and an image is like a specific package version. However, the differences are a bit more nuanced, so make sure to also read [Packages vs Containers](/docs/proget/docker/packages-vs-containers).
