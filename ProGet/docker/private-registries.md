---
title: Private Docker Registry
subtitle: Private Docker Registry
sequence: 100
keywords: proget,feeds,docker,containers
---

Using ProGet's Docker Registries, you can manage your own and third-party Docker images in a uniform manner, while defining fine-grained access control. ProGet works transparently with the Docker client, with images created internally or downloaded from remote Docker resources such as Docker Hub.

Internally, Docker registries are represented as feeds, which means:

 - feed-scoped privileges can be applied on Docker registries
 - you can configure Amazon S3 and Azure blob storage
 - the Docker registry name cannot be the same as another feed name

## Multiple Docker Registries

ProGet lets you define as many Docker registries as you wish. This enables you to manage each project in a distinct registry and exercise better access control over your Docker images.

## Creating and using a Docker Registries in ProGet

<div class="attention technical">

![](/resources/images/icons/technical.png)

Docker requires an SSL connection, so you will need to configure ProGet to use IIS rather than its integrated web server, and configure the web site to use SSL (https). See [testing an insecure registry](https://docs.docker.com/registry/insecure/) in the Docker documentation for some help on configuring Docker to use a self-signed certificate.

</div>

To create a Docker registry in ProGet, go to Containers > Create New Docker Registry, then enter a registry name.

## Setting up Docker Client

ProGet uses token-based authentication, which means you will need to use [Docker 1.11.0](https://github.com/moby/moby/blob/master/CHANGELOG.md#1110-2016-04-13) or newer.

First, you will need to log in to Docker. To do this, use the following command, where _progetsv1_ is the name of your ProGet server:

    [~]$ sudo docker login progetsv1

As mentioned previously, Docker needs an SSL connection, so this operation will fail if ProGet is only accessible via http.

## Publishing an Image

To publish a Docker image to ProGet, you first need to **tag** the image using Docker in a special format. For example, if we have an image locally called `ubuntu:trusty`, we need to retag it as follows:

    [~]$ sudo docker tag ubuntu:trusty progetsv1:443/dmc/ubuntu:trusty

Or, more generally, the format is: _server/feed/image:tag_.

Once tagged, the image can now be pushed to ProGet:

    [~]$ sudo docker push progetsv1:443/dmc/ubuntu:trusty

### Pulling in Images

You can then pull the image in the same fashion:

    [~]$ sudo docker pull progetsv1:443/dmc/ubuntu:trusty

## Implementation Details: Registries, Repositories, and More

While Docker users rarely need to worry about the implementation details, these may become important when you manage and troubleshoot a private Docker registry.

First, makes sure to familiarize yourself with the following Docker terminology and concepts.

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

Conceptually, a registry is like a feed, a repository is like a package name, and an image is like a specific package version. However, the differences are a bit more nuanced, so make sure to also read [Packages vs Containers](/support/documentation/proget/docker/packages-vs-containers).

### Chunked and Monolithic Uploading

To support uploading large files over a single HTTP request, the Docker client will generally use a [chunked upload process](https://github.com/docker/distribution/blob/master/docs/spec/api.md#chunked-upload): send an upload initiation, a series of chunks (partial blob files), then an upload completion. ProGet supports this process, and assembles chunks as specified by the Docker API.

During the "FeedCleanUp" scheduled job, ProGet will purge incomplete uploads (i.e. chunks that were sent without an upload completion).

### Garbage Collection

Unlike packages, a Docker image is not self-contained: it is a reference to a manifest blob, which in turn references a number of layer blobs. These layer blobs may be referenced by other manifests in the registry, which means that you can't simply delete referenced layer blobs when deleting a manifest blob.

This is where garbage collection comes in; it's the process of removing blobs from the package store when they are no longer referenced by a manifest. ProGet performs garbage collection on Docker registries through the "FeedCleanUp" scheduled job.

### Connectors

As of ProGet v5.1, docker feeds support [connectors](/support/documentation/proget/core-concepts/feeds/connectors) to other docker registries.

## Private Registry Limitations

### Windows Integrated Authentication

The Docker client does not support Windows Integrated Authentication, which means that you won't be able to setup "Anonymous" access to a ProGet instance that has this enabled. To workaround this, you can set-up a second site in IIS without Windows Integrated Authentication enabled that points to the same path on disk.

### Other Limitations

Docker is designed to tightly integrate with the publicly-hosted hub.docker.com. Private registries are supported to some extent, but the Docker client and related tooling always assume you will be using their public registry, or at the very least, the official private [Docker Registry](https://docs.docker.com/registry) that they built and support. This can make working with private registries a bit awkward at times, but Docker client support is gradually improving.
