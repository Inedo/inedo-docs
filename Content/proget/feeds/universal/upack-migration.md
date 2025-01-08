---
title: "Migrating Artifacts to Universal Packages"
order: 4
---

Managing a large number of artifacts is a challenge when trying to maintain some sense of order or accessibility. [Universal Packages](/docs/proget/feeds/universal/universal-packages) offer a solution to organize your varied file formats into a unified system.

Such kind of artifacts could include:
* Individual files stored haphazardly in folders.
* Bundled files archived as `.zip` or `.tgz` on disk.
* NuGet Packages created for deployment, often using tools like Octopus Deploy.

Universal Packages in ProGet are designed to standardize and centralize artifact management. They provide a consistent format that simplifies storage, sharing, and deployment across teams.

## Artifacts vs. Packages

To migrate artifacts to Universal Packages effectively, it’s useful to understand the shift from managing artifacts to managing packages.

### Artifacts:

* Individual files or groups of files
* Stored in arbitrary locations, such as folders on shared drives or archives. 
* Often lack structure or metadata

While artifacts serve as useful outputs of build processes or deployment tasks, they can become unwieldy over time due to inconsistent naming, duplication, or unclear context.

### Packages:

* Structured, self-contained units of files and metadata. 
* Designed for distribution and reuse
* Often include metadata that documents purpose, versioning, and dependencies. 

This standardization not only enhances discoverability but also simplifies storage, deployment, and consumption processes.

### Why Migrate Your Artifacts to Universal Packages?

Universal Packages offer a package-centric approach to managing artifacts, offering several advantages:

* Standardized Structure: Files are bundled together in a clearly-defined format.
* Metadata: Packages include details like version numbers, descriptions, and other key information.
* Centralized Management: Stored in feeds, letting teams easily access and share them.
* Automation-Friendly: Packages can be easily created and deployed in automated workflows.

## How to Migrate Artifacts to Universal Packages

ProGet supports migration by allowing you to import your existing artifacts into Universal Packages. This can be done either through the ProGet UI or through the ["Upload Universal Packages"](/docs/proget/reference-api/universal-feed/upload)" endpoint, part of the [Universal Feed API](/docs/proget/reference-api/universal-feed).

### Creating a Universal Feed

In order to host your Universal Packages, you will need to create a Universal Feed. Navigate to "Feeds" and select "Create New Feed". From here select "Universal Packages".

![](/resources/docs/proget-upack-createfeed.png){height="" width="50%"}

From here, select "No Connectors", and then name your feed. Finally select "Create Feed".

### Creating a Universal Package in the ProGet UI

Navigate to your Universal feed and select "Add Package" from the drop down menu. Then select "Create a Package". From here enter metadata of your package, such as name, version and description, and then select "Add Files".

![](/resources/docs/proget-upack-createpackage-metadata.png){height="" width="50%"}

Now you can use the UI to either drag and drop or select files. 

![](/resources/docs/proget-upack-createpackage.png){height="" width="50%"}

Selecting "Create Package" will then create the Universal Package and upload it to your Universal feed.

### Creating a Universal Package with the Universal Package API

The [Upload Universal Packages](/docs/proget/reference-api/universal-feed/upload) endpoint in the [Universal Feed API](/docs/proget/reference-api/universal-feed) let's you create Universal Packages by converting a `.zip` or `.tgz` of artifacts and auto-generating the metadata `upack.json` based on parameters set. To use this, you will need to create an [API Key](/docs/proget/reference-api/proget-apikeys) with appropriate permissions.

To create a Universal Package, first archive your artifacts into either a `.zip` or `.tgz`. Then, simply `POST` to the `/upack/«feed-name»/upload` endpoint with the `application/zip content` type. 

For example, to upload the contents of `home/user/upacks.zip` as a Universal Package `MyPackage` version `1.2.3`, to the feed `universal-feed`, authenticating with the API key `abc12345`:

```bash
$ curl -X POST -H "Content-Type: application/zip" -H "X-ApiKey: abc12345" --data-binary "home/user/upacks.zip" "http://proget.corp.local/upack/universal/upload?name=MyPackage&version=1.2.3"
```
:::
