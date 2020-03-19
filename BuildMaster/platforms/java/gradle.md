---
title: Gradle
subtitle: Building Java Applications with Gradle
sequence: 500
show-headings-in-nav: true
---

[Gradle](https://gradle.org/) is a Java-based build automation tool focused on flexibility and performance. 
Gradle is:

{.docs}
- highly customizable and extensible at its core
- very fast, because it only processes the inputs that have changed instead of everything each time
- powerful, because it can build many popular languages.

Gradle build scripts are written in [Groovy](https://groovy-lang.org/) or [Kotlin](https://kotlinlang.org/) instead of the typical XML files that Maven and Ant use. Gradle is also the official build tool for Android.

Gradle is not just limited to building Java; it also comes with native support for:

{.docs}
  - C/C++
  - Kotlin
  - Groovy
  - Scala
  - JavaScript
  - and more.
  

## Overview & Concepts {#overview data-title="Gradle Overview"}

When creating a build file for Gradle, you typically would create a file named `build.gradle`.  Gradle has two different options for syntax within a build file:

{.docs}  
- Groovy (most common)
- Kotlin

When using Kotlin, you will need to append `.kts` to the end you your Gradle files (i.e., `build.gradle.kts` and `settings.gradle.kts`). 

Since the build files are built using Groovy or Kotlin, build files:

{.docs}
- Can execute custom code
- Are compiled which will give better syntax errors
- Can provide intellisense while creating build files


Gradle has migration paths for converting [Maven](https://docs.gradle.org/6.2.1/userguide/migrating_from_maven.html) and [Ant](https://docs.gradle.org/6.2.1/userguide/migrating_from_ant.html) builds to Gradle.

Currently, Gradle only works with two package sources:

{.docs}
- Apache Maven
- Apache Ivy

Gradle supports single and multi-project builds. In addition to a build file, Gradle defines a settings file (named settings.gradle).  The settings file allows you define your project hierarchy and/or handle multiple build projects in your build process.  It also allows you to extend the build script by building plugins that you include as dependencies within `build.gradle`. 

You can use the `gradle init` command to create a Java project using the default Gradle folder structure.  This, however, is only a starting point, as Gradle does not require any specific folder structure.

### Gradle Build Lifecycle

Gradle's build lifecycle includes:

{.docs}
- Project: a collection of tasks 
- Tasks: execution of a basic piece of work (i.e., compiling classes)
- Build phases:
  - Initialization: determines which projects are going to be built and creates a project instance for each project 
  - Configuration: configures all project instances 
  - Execution: 
    - Determines the sub-set of tasks to execute based on which inputs have changed and which projects where configured in the Configuration phase
    - Executes this determined sub-set


## Building with Gradle {#building data-title="Building with Gradle"}

Before you can build with Gradle, you need to make sure that Gradle is setup in your system's PATH.  Once it is in your system's PATH, you will need to create a `build.gradle` file for Gradle. Then, optionally create a `settings.gradle` to configure your build file. You can use other Gradle files to compile other projects (e.g., unit tests) that can be called from the main Gradle file.

When using Gradle to build Java, you would need to include the `java-library` plugin. Including that plugin automatically enables the following features:

{.docs}
- `compileJava` task that compiles all your Java source files under `src/main/java`
- `compileTestJava` task that compiles all your java test files under `src/test/java`
- `test` task that runs the tests from `src/test/java`
- `jar` task that packages the main compiled classes from `compileJava` and resources from `src/main/resources` into a single `JAR` named `<project>-<version>.jar`
- `javadoc` task that generates Javadoc for the main classes

Remember, Gradle is highly customizable: You can modify the build file to work with your application. Although Gradle has a default folder structure, you can easily customize that by defining your folder locations in your build file. And you can also add functionality by including more plugins.

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

### Running Gradle via the Command Line

Gradle highly encourages using the [Gradle Wrapper](https://docs.gradle.org/current/userguide/gradle_wrapper.html#gradle_wrapper). When using the Gradle Wrapper, you should replace Gradle with `gradlew.bat` (or `./gradlew` for Linux):

```
gradle build
```

## Executing Gradle with BuildMaster {#buildmaster data-title="Gradle in BuildMaster"}

Gradle must be installed on the build server prior to executing any of these commands.  Setting GRADLE_HOME in the path is preferable. To do any of this, however, the Java extension must be installed in BuildMaster.

Use the following OtterScript in a deployment plan:

```
Exec "gradle build"
```

An example plan that gets the latest source code from Git and captures a Gradle artifact as a BuildMaster artifact:

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
