---
title: "Connectors in Docker Registries"
order: 3
---

ProGet's Docker Registries support connectors to other registries. Like package feed connectors, they allow you to combine multiple repositories into one. However, they have some limitations and work a little differently than package feed connectors.

## Usage

A Docker Connector makes resources from a repository accessible as part of one of the ProGet registries. For example, you could create two registries based on quality: *PrereleaseImages* and *ReleaseImages*. For simplicity, you want all images stored in the *ReleaseImages* registry to also be visible in the *ReleaseImages* registry. To achieve this, you can create a connector from *PrereleaseImages* to *ReleaseImages*. The Docker client can now transparently retrieve images from both registries as if they were all stored in the *PrereleaseImages* registry.

You can also create a connector to an external (non-ProGet) registry like Docker Hub. This could be useful to whitelist certain base images or to store them in your local cache

## Technical Details

Docker is largely *content addressable*, meaning that images and their filesystem layers are identified by unique hashes generated from their content. Since a Docker image is actually a manifest that refers to other content (referred to by Docker as layers or blobs), connectors operate at the content level. This means that an image's manifest may be stored locally in ProGet, but that manifest may reference layers stored in another repository via a connector. This means that a manifest can reference non-existent layers if the connector is removed or if the referenced layers are removed from the external registry.

When caching is enabled and a connector image is requested by a client, ProGet attempts to cache the image's manifest and all its layers.

## Limitations

Many external registries such as Docker Hub do not provide an API for searching and discovering images, so ProGet-Web UI cannot be used to search for images stored in one of these registries through the Connector.

See [How to Manage Docker Repositories in ProGet for Artifactory Users](https://blog.inedo.com/proget-migration/how-to-manage-docker-repositories-in-proget-for-artifactory-users/) on the Inedo blog for additional guidance.