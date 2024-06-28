---
title: "Ant"
order: 2
---


[Ant](https://ant.apache.org) is a Java-based build tool for Java.

Written in Java, the original author, James Duncan Davidson, said the name was an acronym for "Another Neat Tool." Ant was released on April 19, 2000, as part of [Tomcat](/docs/buildmaster/development-platforms/buildmaster-platforms-java/deploying-java-applications-to-tomcat) 3.1 and was used to build Tomcat and nothing else. Ant then split off after being donated to the Apache Software Foundation because it was found to fix many problems developers had with Makefiles, and the first official standalone release was Ant 1.1 on July 19, 2000. It was the most popular build tool for Java until [Maven](/docs/buildmaster/development-platforms/buildmaster-platforms-java/buildmaster-platforms-java-maven).

Some of Ant's best qualities: 
  - its extreme flexibility
  - does not impose coding conventions
  - does not impose directory layouts

## Overview & Concepts

Because Ant is built on Java, it can be run on any OS. Ant uses XML-based configuration files called build files that define a target tree. 

These configuration files are made up of:

- Project: the master build definition containing all of the targets, tasks, and properties 
  - Each project defines one or more targets
- Targets: a set of tasks you want to be executed
  - Can depend on other targets
  - When starting Ant, you select which target(s) you want executed
- Tasks: a piece of code that can be executed 
- Properties: key/value pairs used to customize or provide shortcuts to values used within targets and tasks
  - These can be set:
    - externally and passed in when calling Ant
    - internally within a target or task
 
To extend Ant, users can develop their own "antlibs" containing Ant tasks and types. These are based in Java and have specific interfaces they must implement. For dependency management, use [Apache Ivy](https://ant.apache.org/ivy/).

## Apache Ivy 

[Apache Ivy](https://ant.apache.org/ivy/) is a tool for recording, tracking, resolving, and reporting project dependencies, and it's characterized by flexibility, configurability, and tight integration to Ant.  IT is the best option for managing dependencies when using Ant.

Ivy uses the Maven 2 repository to resolve the dependencies you declare in an Ivy file, and its syntax is very similar to Apache Ant.

## Building with Ant

First, create a build file for Ant. 

A basic build file typically includes these targets:


- `init`: Creates the build directory structure used by compile 
- `compile`: Compiles the java files in the src folder into bytecode and outputs them into the build folder
- `dist`: Packages the build folder into a JAR, WAR, etc... and copies that into the dist folder
- `clean`: Deletes the build and dist directories
  
Defining your project default target to be `dist` will simplify executing an Ant build. Additional targets can be included.

### Example Build File
```
<project name="ProfitCalcJava" default="dist" basedir=".">
  <description>
    simple example build file
  </description>
  <!-- set global properties for this build -->
  <property name="src" location="src"/>
  <property name="build" location="build"/>
  <property name="dist" location="dist"/>
  <property name="version" value="1.0"/>

  <target name="init">
    <!-- Create the time stamp -->
    <tstamp/>
    <!-- Create the build directory structure used by compile -->
    <mkdir dir="${build}"/>
  </target>

  <target name="compile" depends="init"
        description="compile the source">
    <!-- Compile the Java code from ${src} into ${build} -->
    <javac srcdir="${src}" destdir="${build}"/>
  </target>

  <target name="dist" depends="compile"
        description="generate the distribution">
    <!-- Create the distribution directory -->
    <mkdir dir="${dist}/lib"/>

    <!-- Put everything in ${build} into the ProfitCalcJava-${version}.jar file -->
    <jar jarfile="${dist}/lib/ProfitCalcJava-${version}.jar" basedir="${build}"/>
  </target>

  <target name="clean" description="clean up">
    <!-- Delete the ${build} and ${dist} directory trees -->
    <delete dir="${build}"/>
    <delete dir="${dist}"/>
  </target>
</project>
```

### Running Ant Via the Command Line

#### Using the Default Target
```
ant -buildfile "ProfitCalcJava.build" -Dversion:1.1
```

#### Specifying Targets
```
ant -buildfile "ProfitCalcJava.build" -Dversion:1.1 clean dist
```

## Executing Ant with BuildMaster 

Ant must be installed on the build server before any of these commands can be executed. Specifying ANT_HOME in the path is preferred, but you can use the AntPath variable function to override it. However, this requires the Java extension to be installed in BuildMaster.

Use the following OtterScript in a deployment plan:

```
Java::Build-AntProject
(
    BuildPath: ProfitCalcJava.build
    ProjectBuildTarget: dist,
    ProjectBuildTarget: target,
    BuildProperties: @(version=$ReleaseNumber.$BuildNumber)
)
```

- Running Ant directly:
```
Exec "ant -buidfile ProfitCalcJava.build -Dversion:$ReleaseNumber.$BuildNumber dist";
```

- Example plan that gets the latest source code from Git and captures a Maven artifact as a BuildMaster artifact:
```
Git::Get-Source
(
    RepositoryUrl: https://github.com/Inedo/ProfitCalcJava.git,
    Branch: master
);

Java::Build-AntProject
{
    BuildPath: ProfitCalcJava.build
    BuildTarget: dist
    ProjectBuildTarget: target,
    BuildProperties: @(version=$ReleaseNumber.$BuildNumber)
};

Create-Artifact ProfitCalcJava
(
    From: target,
    Include: @(*.jar, *.war, *.ear)
);
```