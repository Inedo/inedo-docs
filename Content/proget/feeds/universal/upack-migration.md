---
title: "Migrating Artifacts to Universal Packages"
order: 3
---

Managing a large number of artifacts is a challenge when trying to maintain some sense of order or accessibility. [Universal Packages](/docs/proget/feeds/universal/universal-packages) offer a solution to organize your varied file formats into a unified system.

Such kind of artifacts could include:
* Individual files stored haphazardly in folders.
* Bundled files archived as `.zip` or `.tgz` on disk.
* NuGet Packages created for deployment, often using tools like Octopus Deploy.

Universal Packages in ProGet are designed to standardize and centralize artifact management. They provide a consistent format that simplifies storage, sharing, and deployment across teams.

## How to Migrate Artifacts to Universal Packages

ProGet supports migration by allowing you to import your existing artifacts into Universal Packages. This can be done either through the ProGet UI or through the ["Upload Universal Packages"](/docs/proget/reference-api/universal-feed/upload)" endpoint, part of the [Universal Feed API](/docs/proget/reference-api/universal-feed).

### Creating a Universal Feed

In order to host your Universal Packages, you will need to create a Universal Package feed. Navigate to "Feeds" and select "Create New Feed". From here select "Universal Packages".

![](/resources/docs/proget-upack-createfeed.png)

From here, select "No Connectors", and then name your feed. Finally select "Create Feed".

### Creating a Universal Package in the ProGet UI

Navigate to your Universal feed and select "Add Package" from the drop down menu. Then select "Create a Package". From here enter metadata of your package, such as name, version and description, and then select "Add Files".

![](/resources/docs/proget-upack-createpackage-metadata.png)

Now you can use the UI to either drag and drop or select files. 

![](/resources/docs/proget-upack-createpackage.png)

Selecting "Create Package" will then create the Universal Package and upload it to your Universal feed.

### Creating a Universal Package Through the Universal Package API

The [Upload Universal Packages](/docs/proget/reference-api/universal-feed/upload) endpoint in the [Universal Feed API](/docs/proget/reference-api/universal-feed) let's you create Universal Packages by packaging files located in a specified directory. To use this, you will need to create an [API Key](/docs/proget/reference-api/proget-apikeys) with appropriate permissions.

To create a package, simply `POST` to the `/upack/«feed-name»/upload` endpoint with a feed name and `application/zip` as the content header. For example, to upload the contents of `home/user/upacks/` as a Universal Package, to the feed `universal-feed`, authenticating with the API key `abc12345`:

```bash
$ curl -X POST -H "Content-Type: application/zip" --header "X-ApiKey: abc12345" --data-binary "home/user/upacks/" "http://«proget-url»/upack/«feed-name»/upload"
```