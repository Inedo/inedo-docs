---
title: "Maven"
order: 1
---


BuildMaster can help you build your Maven-based Java projects, which you can then deploy to Tomcat, WildFly/JBoss EAP, Jetty, or other services like Azure.

:::(Info) (👨‍🏫 What is Maven?)
[Maven](https://maven.apache.org/) is a command-line tool that's used to compile Java projects and package them into an artifact (JAR, WAR, EAR, etc.). It's used by all of the major IDEs (Eclipse, Netbeans, IntelliJ), as well as BuildMaster. 
:::

This article focuses primarily on how to work with Maven in your [build script](/docs/buildmaster/builds-continuous-integration/buildmaster-build-scripts), so that you can create the artifacts you need to deploy later on in your [pipeline](/docs/buildmaster/deployment-continuous-delivery/buildmaster-pipelines).

## Installing and Configuring Maven
Before building your Maven project using BuildMaster, you'll need to make sure that both [Maven is installed](https://maven.apache.org/install.html) *and* the appropriate JDK is installed on your build server.

Alternatively, you can use a Docker container image like [maven on Docker Hub](https://hub.docker.com/_/maven/); see [Image-based Services (Containerized Builds)](/docs/buildmaster/administration/buildmaster-image-based-services) to learn more.

## Build Maven Project Script Template
The easiest way to work with Maven in BuildMaster is with the [Build Maven Project Script Template](/docs/buildmaster/builds-continuous-integration/buildmaster-build-scripts/buildmaster-build-maven-project-script-template). It's a simple, no-code solution that will check out your code from Git, run Maven, then capture build artifacts for later deployment.

![buildmaster-maven-build-with-npm-template](/resources/docs/buildmaster-maven-build-with-npm-template.png){height="" width="50%"}

The script template can set the project version, configure repositories (settings.xml), and even run npm to build  front-end, Javascript code for Java web applications.

## Creating a Build Script with Maven
Although a Maven project file (i.e. POM.xml file) can get complicated, running maven itself is really easy. You just specify the "goals/phases" to run (generally `clean` and `package`), and maven will handle the rest. 

:::(Info) (💡 Best Practices: Use "clean package" and avoid "install")
When deploying with BuildMaster, we recommend only *packaging* artifacts because the package output will be captured and deployed as a BuildMaster artifact
:::

You can do this in OtterScript  with the `Java::Maven` operation as follows:
```
Java::Maven
(
    GoalsAndPhases: "clean package"
);
```

The `Java::Maven` operation is essentially a wrapper for the `mvn` command-line tool, and if you're familiar with `mvn`, it'll be just as easy to use. The above OtterScript basically translates to `mvn clean package`.

### Sample Build Script
The following sample script will get the latest source code from Git, run Maven, then capture the build output (jar file) as a BuildMaster artifact:
```
Git::Get-Source
(
    RepositoryUrl: https://github.com/Inedo/ProfitCalcJava.git,
    Branch: master
);
Java::Maven
(
    GoalsAndPhases: "clean package",
    In: ProfitCalcJava
);
Create-Artifact ProfitCalcJava
(
    From: ProfitCalcJava\target
);
```

## Integrating Maven with ProGet
When BuildMaster is connected to ProGet, ProGet feeds can be integrated with the `Java::Maven` operation by specifying the `DependenciesFeed` and `PluginsFeed` paramters.  When either of these parameters are specified, a custom "settings.xml" is created with those Maven repositories and their related authentication accounts.  This file will then be pased to the `mvn` command using the `-s` parameter.

For example:
```
Java::Maven
(
    GoalsAndPhases: "clean package",
    In: ProfitCalcJava,
    DependenciesFeed: feed::InternalMaven,
    PluginsFeed: feed::InternalMaven
);
```

This operation will generate a `settings.xml` file with these feeds and run the following `mvn` command in the ProfitCalcJava directory:
```
mvn clean package -s settings.xml
```

Alternatively, you can create a custom settings.xml on your server and pass that to the `Java::Maven` operation using the `SettingsXml` parameter.