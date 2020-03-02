---
title: Gradle
subtitle: Building Java Applications with Gradle
sequence: 500
show-headings-in-nav: true
---

[Gradle](https://gradle.org/)  is a Java-based build automation tool focused on flexibility and performance.

- Gradle build scripts are written in [Groovy](https://groovy-lang.org/) or [Kotlin](https://kotlinlang.org/) instead of the typical XML files that Maven and Ant use.
- Gradle is the official build tool for Android
- Gradle is not just limited to building Java, it also comes with native support for
  - C/C++
  - Kotlin
  - Grrovy
  - Scala
  - JavaScript
  - and more...
- Gradle is
  - Highly customizable and extensible at its core
  - Fast. it only processes the inputs that have changed instead of everything each time
  - Powerful, it can build many popular languages



## Overview & Concepts {#overview data-title="Gradle Overview"}

-Gradle has two different options for creating build files  (build.gradle)
  {.docs}  
  - Groovy (most common)
  - Kotlin

- Gradle has to be setup in your systems PATH.
- Gradle has migration paths for [Maven](https://docs.gradle.org/6.2.1/userguide/migrating_from_maven.html) and [Ant](https://docs.gradle.org/6.2.1/userguide/migrating_from_ant.html)
- Gradle only works with two package sources currently: 
  {.docs}
  - Apache Maven
  - Apache Ivy
- Since the build files are built using Groovy or Kotlin, build files can
  {.docs}
  - Execute custom code
  - Are compiled which will give better syntax errors
  - Can provide intellisense while creating build files
- Gradle supports single and multi-project builds
- In addition to a build file, Gradle defines a settings file (settings.gradle)
- Gradle allows extension of the build script by building plugins that you include as dependencies within `build.gradle`.
- When using kotlin, you will need to append `.kts` to the end you your gradle files. i.e. `build.gradle.kts`
- You can use `gradle init` to create a java project using the dfault Gradle folder structure

### Gradle build lifecycle
- Project
  - A collection of tasks 
- Tasks
  -  Execution of a basic piece of work (i.e. compiling classes)
- Build phases
  - Initialization
    - Determines which projects are going to be built
    - Creates a project instance for each Project 
  - Configuration
    - configures all project instances 
  - Execution
    - Determines the sub set of tasks to execute based on 
      - which inputs have changed 
      - which pojects where configured in the Configuration phase
  - Executes the tasks


## Building with Gradle {#building data-title="Building with Gradle"}

First, create a build.gradle file for Gradle.  Next you would create a settings.gradle to configure your build file.  You can use other gradle files to compile other projects (like unit tests) that can be called from the main graddle file.

When using Gradle to build java, you would need to include the `java-library` plugin.  By including that plugin, you will automatically enable the following features:
{.docs}
- `compileJava` task that compiles all your Java source files under `src/main/java`
- `compileTestJava` task that compiles all your java test files under `src/test/java`
- `test` task that runs the tests from `src/test/java`
- `jar` task that packages the main compiled classes from `compileJava` and resources from `src/main/resources` into a single `JAR` named `<project>-<version>.jar`
- `javadoc` task that generates Javadoc for the main classes

Gradle is highly customizable.  You can modify the build file to work with your application.  Although they have a default folder structure, you can easily customize that by defining your folder locations in your build file.  You can also add functionality by including more plugings.

### Example build.gradle File
```
plugins {
    id 'java-library'
}

java {
    sourceCompatibility = JavaVersion.VERSION_1_8
    targetCompatibility = JavaVersion.VERSION_1_8
}

version = '1.2.1'

```

### Running Gradle Via the Command Line

Gradle highly encourages using the [Gradle Wrapper](https://docs.gradle.org/current/userguide/gradle_wrapper.html#gradle_wrapper).  When using the Gradle Wrapper, you should replace gradle with gradle.bat (or ./gradlew for Linux).

```
gradle build
```

## Executing Gradle with BuildMaster {#buildmaster data-title="Gradle in BuildMaster"}

Gradle must be installed on the build server prior to executing any of these commands. Setting GRADLE_HOME in the path is preferable. To do any of this, however, [[ATTN: RICH - did I just make up the content in the beginning?]] the Java extension must be installed in BuildMaster.

Use the following OtterScript in a deployment plan:

```
Exec "gradle build"
```

- Example plan that gets the latest source code from Git and captures a Gradle artifact as a BuildMaster artifact:
```
Git::Get-Source
(
    RepositoryUrl: https://github.com/Inedo/ProfitCalcJava.git,
    Branch: master
);

Exec "gradle build"

Create-Artifact ProfitCalcJava
(
    From: build/libs
);
```
