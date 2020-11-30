---
title: Connectors in Docker Registries
sequence: 300
show-headings-in-nav: true
---

ProGet's Docker Registries support connectors to other registries. Like package feed connectors, these allow you to aggregate multiple repositories into one. However, they have some limitations and work a little differently compared to package feed connectors.

## Usage

A Docker Connector makes resources from one repository accessible as part of one of ProGet's registries. For example, you could create two registries based on quality: *PrereleaseImages* and *ReleaseImages*. For simplicity, you would like all images stored in the *ReleaseImages* registry to be visible in the *ReleaseImages* registry. To accomplish this, you can create a connector from *PrereleaseImages* to *ReleaseImages*. The Docker client will now transparently be able to pull images from both as if they are all stored in the *PrereleaseImages* registry.

You can also create a connector to an external (non-ProGet) registry such as Docker Hub. This could be useful to whitelist certain base images, or to cache them on your local infrastructure.

## Technical Details

Docker is mostly *content-addressable*, meaning that images and their filesystem layers are identified by unique hashes which are generated from their content. Because a Docker image is actually a manifest that refers to other content (referred to by Docker as layers or blobs), connectors operate at the content-level. This means that an image's manifest may be stored locally in ProGet, but that manifest may refer to layers that are stored in another repository via connector. This means that it's possible for a manifest to refer to nonexistent layers if the connector is removed or if the referenced layers are removed from the external registry.

When caching is enabled and a connector image is requested by a client, ProGet will attempt to cache the image manifest as well as all of its layers.

## Limitations

Many external registries such as Docker Hub do not expose an API for searching and discovery of images, so the ProGet web UI cannot be used to search or browse for images stored in one of these registries via connector.
