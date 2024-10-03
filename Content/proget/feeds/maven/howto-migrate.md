---
title: "HOWTO: Migrate Maven Artifacts from Another Repository"
order: 4
---

Migrating Maven artifacts from another repository to ProGet will depend on where you are migrating from, be it Artifactory, Azure DevOps, MyGet, etc. If you are migrating from another instance of ProGet, this can be done easily following the steps in our [Maven Feed documentation](/docs/proget/feeds/maven#maven-classic-feed-types). For other repositories there are three ways of migrating:

* Downloading artifacts directly from other repository
* Importing artifacts from a folder on a ProGet server
* Uploading artifacts to ProGet using a script

In this article we will give guidance on which method is best, based on your Maven repository source.
 
## How to Choose the Right Migration Technique

Choosing the right migration technique depends on the configuration and capabilities of your repository or environment:

* [Direct download](#direct-download): Requires that the repository is using the [Maven Indexer](https://maven.apache.org/maven-indexer/).
*Examples: JFrog Artifactory, Sonatype Nexus*

* [Importing from a Folder](#folder-import): Recommended when the repository does not have a Maven Indexer.
*Examples: MyGet, Azure DevOps/Azure Artifacts, GitHub*
 
* [Uploading using a script](#script-upload): Recommended when ProGet does not have access to your Maven repository and it's inconvenient or complicated to put files on a ProGet server or a network share.

## Downloading Directly from Another Repository { #direct-download }

Direct Download requires that the repository is using the [Maven Indexer](https://maven.apache.org/maven-indexer/). ProGet will download the index file, then download the artifacts listed in that file. You can also restrict or specify which artifacts to download.

### How to Migrate from Artifactory

To migrate a Maven repository in Artifactory, follow the steps in our [HOWTO: Migrate from Jfrog Artifactory to ProGet](/docs/proget/installation/migrating-to-proget/proget-jfrog-artifactory-feed-migration) guide. However, you will also need to enable Maven Indexer. To do this, navigate to "Administration" and then select "Maven Indexer" under "Artifactory Settings".

You will then need to check the "Enabled" box under "Maven Indexer Settings", select "Run Indexing Now" and then select "Save". Note that Indexing will not occur immediately. Refer to the "Next Indexing Time". You will not be able to migrate your Maven repository until this has run. 

![Maven Indexer](/resources/docs/artifactory-maven-indexer.png){height="" width="50%"}

In your ProGet instance, follow the [remaining steps](/docs/proget/installation/migrating-to-proget/proget-jfrog-artifactory-feed-migration#step-1-create-a-new-feed) in the guide to create a feed in ProGet and migrate your artifacts. 

When selecting the repository to migrate from, make sure you select the local repository to avoid public artifacts.

![Migrate](/resources/docs/proget-maven-migrate.png){height="" width="50%"}

### How to Migrate from Sonatype Nexus

To migrate a Maven repository from Sonatype Nexus you will need to [generate a token](https://central.sonatype.org/publish/generate-token/) and get the repository URL. These would be the same as you would use in the `settings.xml`.

Then in your ProGet instance, follow the steps in our [HOWTO: Migrate from Another Repository to ProGet](/docs/proget/installation/migrating-to-proget/proget-other-feed-migration) guide, selecting "Maven Artifacts" instead of NuGet.

![Maven](/resources/docs/proget-maven-createfeed.png){height="" width="50%"}

### Other Maven Indexer types
For other repositories that include a Maven Indexer you will need to follow their own documentation to generate an API key and get a repository URL. When doing this you will need to make sure that remote repositories are returned in results.

Then in your ProGet instance, follow similar steps shown in our [HOWTO: Migrate from Another Repository to ProGet](/docs/proget/installation/migrating-to-proget/proget-other-feed-migration) guide, but select other repository/server types instead of NuGet.

### What if I use a repository that does not support a Maven Indexer?
If your repository does not support Maven Indexer, start by specifying all of your group/artifactid names. ProGet will then to find the file list using directory listing

If ProGet can not find a list of files, it will then try the common file types (e.g. `.jar`, `.war`, `.ear`, and their included sources and javadocs).

:::(info) (Azure Artifacts)
For Azure Artifacts, ProGet will call to the API to get the ArtifactIds and pre-populate them in the exact names. It will then follow the exact name process.
:::

## Migrating by Importing from a Folder { #folder-import }
This is a best practice when your repository does not have a Maven indexer. To import from a folder you will need to:

* Download all artifacts
* Organize them in a folder structure, either:
    * `group.name/artifactId/version/file`
    * `group/name/artifactid/version/file`
    (Note: You can specify group with periods or creating a folder for each segment of the group name, like the local maven repository does (Eg. `com.my.group` or `com/my/group`)
* Copy them to the configured drop path or import them from a folder on the server by navigating to "Add Artifact" > "Bulk Artifact Import/File Copy"

## Uploading Artifacts Using a Script { #script-upload }
This is a best practice when:
* ProGet does not have access to your maven repository, either because it's cloud hosted and your maven repository isn't, or due to other networking complications.
* It's' either inconvenient or complicated to put files on ProGet server or a network share
 
Instead, you can simply upload files on disk using a very basic `PUT` command for each artifact. Just `PUT` it to the desired path (e.g. `group/name/artifactid/version/file` )

ProGet ignores `maven-metadata.xml` and hashfiles (`.sha1`, `.md5`, etc), so you will not need to worry about uploading them. ProGet will also warn/error if invalid files are uploaded.
 
#### Example Script:
```
  $EndpointUrl = "https://...maven/" //url endpoint for ProGet

  Get-ChildItem -Recurse -File | ForEach-Object { 
    $path = $_.FullName.Substring($PWD.Path.Length + 1).Replace("\", "/")
    Invoke-RestMethod -Uri "$($EndpointUrl.TrimEnd("/"))/$path" -Method Put -InFile $_.FullName
  }
```