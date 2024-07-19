---
title: "Sample: Gradle"
order: 2
---

[Gradle](https://gradle.org/) is a Java-based build automation tool focused on flexibility and performance. Gradle's build scripts can be written in [Groovy](https://groovy-lang.org/) or [Kotlin](https://kotlinlang.org/. In this sample, we built a simple Java library that demonstrates integrating Gradle with ProGet. Our sample is written using a Groovy-based build file but should be easy to convert to Kotlin if needed.

## Gradle Hello World Library
Our sample is located on our GitLab repository [https://gitlab.com/inedo-samples/gradlehelloworld](https://gitlab.com/inedo-samples/gradlehelloworld).  This is a sample java library that is built using gradle, pull dependencies from ProGet, creates a Software Bill of Materials (SBOM) using CycloneDX, pushes a package to ProGet.

## Prerequisite
- Java JDK 8+
- [Gradle](https://gradle.org/releases/)
- ProGet 
    - If you are going to use the Software Bill of Materials portion, you will need ProGet v2022 or newer.

## Building and Publishing the Project
This project is setup to use the following properties:
- `version`: Version of the package
- `progetRepository`: URL to the Maven repository
- `progetApiKey`: API Key to publish and/or pull packages
- `systemProp.org.gradle.internal.publish.checksums.insecure`: Tells gradle to not push checksums for SHA256 and SHA512
    - To use this as a command line parameter, you will need to use `-Dorg.gradle.internal.publish.checksums.insecure=true`
    - ProGet v2022.26 and above has added support for SHA256 and SHA512 checksums

These properties can be specified in the `gradle.properties` file (stored in teh project folder or the user's .gradle folder).

### Configuring gradle to use ProGet
The main components needed to integrate ProGet into gradle are configuring the `repositories` and `publishing` properties of the `build.gradle`.  First, gradle is configured to use ProGet to pull dependencies.  This is done by setting the `repositories` property to use a `maven`  repository.  The `url` in this example uses a variable named `progetRepository` for the feed URL and `progetApiKey` to specify the API Key repository access.  The `credentials` and `authentication` properties can be removed if the feed is publicly available.  By default, gradle requires HTTPS connections for maven repositories.  If your ProGet instance is not configured to use HTTPS, you will need to add the `allowInsecureProtocol true` property to allow HTTP connections.

Example:
```groovy
repositories {
    maven {
        url progetRepository
        allowInsecureProtocol true
        credentials(HttpHeaderCredentials) {
            name = "X-ApiKey"
            value = progetApiKey
        }
        authentication {
            header(HttpHeaderAuthentication)
        }
        metadataSources {
            mavenPom()
            artifact()
        }
    } 
}
```

Next, gradle is configured to push this package to ProGet.  This is done by setting the `publishing` property to use a `maven`  repository.  Just as the repositories, the url in this example uses a variable named `progetRepository` for the feed URL and `progetApiKey` to specify the API Key repository access.
```groovy
publishing {
    publications {
        gradleHelloWorld(MavenPublication) {
            from components.java
        }
    }

    repositories {
        maven {
            name 'ProGet'
            url progetRepository
            allowInsecureProtocol true
            credentials(HttpHeaderCredentials) {
                name = "X-ApiKey"
                value = progetApiKey
            }
            authentication {
                header(HttpHeaderAuthentication)
            }
        }
    }
}
```

### Building the Package
To build the project use the included gradle wrapper:
```sh
./gradlew build
```

If you want to use parameters instead of the `gradle.properties` file, run the following:
```sh
./gradlew build "-PprogetApiKey=MyApiKey" "-PprogetRepository=http://my.proget.server/maven2/feed-name/" "-Dorg.gradle.project.version=1.0"
```

### Generating the SBOM.xml
This sample uses the `org.cyclonedx.bom` plugin to generate an SBOM.xml.  

You will first need to build your project, then run the `cyclonedxBom` task.  To run this:

```sh
./gradlew build cyclonedxBom
```

If you want to use parameters instead of the `gradle.properties` file, run the following:
```sh
./gradlew build cyclonedxBom "-PprogetApiKey=MyApiKey" "-PprogetRepository=http://my.proget.server/maven2/feed-name/" "-Dorg.gradle.project.version=1.0"
```

Once the SBOM file has been generated, it can be pushed to ProGet using `curl` with the import endpoint on the [SCA API](/docs/proget/reference-api/proget-api-sca).

### Publish Your Project
To publish your project, you will need to build, optionally generate the SBOM, and then publish.  To do this:
```sh
./gradlew build cyclonedxBom publish
```

If you want to use parameters instead of the `gradle.properties` file, run the following:
```sh
./gradlew build cyclonedxBom publish "-PprogetApiKey=MyApiKey" "-PprogetRepository=http://my.proget.server/maven2/feed-name/" "-Dorg.gradle.internal.publish.checksums.insecure=true" "-Dorg.gradle.project.version=1.0"
```