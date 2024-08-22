---
title: "Sample: sbt (Scala)"
order: 3
---

[sbt](https://www.scala-sbt.org/) is built for Scala and Java projects.  In this sample, we built a simple Java library that demonstrates integrating sbt with ProGet.

## sbt Hello World Library

Our sample is located on our GitLab repository [https://gitlab.com/inedo-samples/sbthelloworld](https://gitlab.com/inedo-samples/sbthelloworld).  This is a sample java library that is built using sbt that pulls dependencies from ProGet, creates a Software Bill of Materials (SBOM) using CycloneDX, pushes a package to ProGet.

## Prerequisite

- Java JDK 8+
- [sbt](https://www.scala-sbt.org/)
- ProGet 
    - If you are going to use the Software Bill of Materials portion, you will need ProGet v2022 or newer.

## Building and Publishing the Project

This project is set up to use the following properties:
- `version`: Version of the package
- `feed`: The name of the feed in ProGet
- `host`: The host address for ProGet (ex: `http://localhost:81`)
- `key`: The API Key to publish and/or pull packages

### Configuring sbt to use ProGet

The main components needed to integrate ProGet into sbt are configuring the `externalResolvers` and `publishTo` properties of the `build.sbt`.  First, sbt is configured to use ProGet to pull dependencies.  This is done by setting the `externalResolvers` property to use a maven repository.  This example uses the `host` and `feed` to build a feed URL and `key` to specify the API Key repository access.  The `credentials` properties can be removed if the feed is publicly available.  If you are using a secured feed, the Credentials need to be added where the realm is `ProGet Feed <FEED_NAME>`.

Example:

```scala
val progetFeed = sys.props.getOrElse("feed", default = "my-maven-feed")
val progetHost = sys.props.getOrElse("host", default = "http://localhost")
val apiKey = sys.props.getOrElse("key", default = "key")
ThisBuild / credentials += {
    val credHost = new java.net.URL(progetHost)
    Credentials("ProGet Feed " + progetFeed, credHost.getHost(), "api", apiKey)
}
ThisBuild / externalResolvers := Vector("ProGet Feed " + progetFeed at progetHost + "/maven2/" + progetFeed)
```

Next, sbt is configured to push this package to ProGet.  This is done by setting the `publishTo` property to use a maven  repository.  Just as the repositories, the url in this example uses the `host` and `feed` to build a feed URL and `key` to specify the API Key repository access.
```scala
ThisBuild / publishTo := Some("ProGet Feed " + progetFeed at progetHost + "/maven2/" + progetFeed)
ThisBuild / publishMavenStyle := true
```

### Building the Package

To build the project runr:

```sh
sbt '-Dversion=0.1.0' '-Dhost=http://localhost:81' '-Dfeed=public-maven' '-Dkey=myApiKey' clean compile
```

### Generating the SBOM.xml

This sample uses the [sbt-bom](https://github.com/siculo/sbt-bom) plugin to generate an SBOM.xml.  

You will first need to build your project, then run the following:

```sh
sbt '-Dversion=0.1.0' '-Dhost=http://localhost:81' '-Dfeed=public-maven' '-Dkey=myApiKey' clean compile makeBom
```
Once the SBOM file has been generated, it can be pushed to ProGet using `curl` with the import endpoint on the [SCA API](/docs/proget/reference-api/proget-api-sca).


### Publish Your Project

To publish your project, you will need to build, optionally generate the SBOM, and then publish.  To do this:

```sh
sbt '-Dversion=0.1.0' '-Dhost=http://localhost:81' '-Dfeed=public-maven' '-Dkey=myApiKey' clean compile makeBom publish
```