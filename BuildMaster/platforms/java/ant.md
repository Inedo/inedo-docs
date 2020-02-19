---
title: Ant
subtitle: Building Java Applications with Ant
sequence: 100
show-headings-in-nav: true
---

- [Ant](https://ant.apache.org) is a Java-based build tool for Java
- Ant is written in Java. 
- According to Ant's original author, James Duncan Davidson, the name is an acronym for "Another Neat Tool".
- Originally Ant was a part of [Tomcat] and was only used to build Tomcat and nothing else
- Ant was then separated after it's donation to the Apache Software Foundation because it was determined it could fix many issues developers had with Makefiles
- Originally released on April 19th 2000 with Tomcat 3.1
- The first official stand-alone release was Ant 1.1 on July 19 2000
- Was the most popular build tool for Java until [Maven]
- Ant is 
  - extremely flexible
  - does not impose coding conventions
  - does not impose directory layouts

## Overview & Concepts {#overview data-title="Ant Overview"}

- Because Ant is built on Java, it can be run on any other OS
- Ant uses XML based configuration files called build files that define a target tree
  - Configuration files are made up of
    - Project
      - Master container of all the targets, tasks, and properties 
      - Each project defines one or more targets.
    - Targets
      -  target is a set of tasks you want to be executed
      -  Can depend on other targets
    - Tasks
      - a piece of code that can be executed 
    - Properties
      - Are key/value pairs used to customize or provide shortcuts to values used within targets and tasks
      - They can be set 
        - externally and passed in when calling ant
        - internally within a target or task
  - When starting ant, you select which target(s) you want to have executed
- To extend Ant, users can develop their own "antlibs" containing Ant tasks and types
  - These are based in Java and have specific interfaces they must implement
- For dependency management should use [Apache Ivy](https://ant.apache.org/ivy/)

## Apache Ivy {#ivy data-title="Apachy Ivy"}

- Apache Ivy is a tool for recording, tracking, resolving, and reporting project dependencies
- It is characterized by flexibility, configurability, and tightly
- Ivy uses the Maven 2 repository to resolve the dependencies you declare in an Ivy file
- Ivy's syntax is very similar to Apache Ant

## Building with Ant {#building data-title="Building with Ant"}

- The first thing you need to do is create a build file for Ant
  - A basic build file typically includes these targets
    - `init` - Creates the build directory structure used by compile 
    - `compile` - Compiles the java files in the src folder into bytecode and outputs them into the build folder
    - `dist` - Packages the build folder into a jar, war, etc... and copies that into the dist folder
    - `clean` - Deletes the build and dist directories
  - Defining your project default target to be `dist` will simplify executing an ant build
  - Additional targets can be included 

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

### Running Ant via the command line

##### using the default target
```
ant -buildfile "src\ProfitCalcJava.xml" -Dversion:1.1
```

##### Specifying targets
```
ant -buildfile "src\ProfitCalcJava.xml" -Dversion:1.1 clean dist
```

## Executing Ant with BuildMaster {#buildmaster data-title="Ant in BuildMaster"}

- ant must be installed on the build server prior to executing any of these commands
  - setting ANT_HOME in the path is preferable, but you can use the AntPath variable function to override it
- the Java extension must be installed in BuildMaster
- use the following OtterScript in a deployment plan:

```
Java::Build-AntProject
{
    BuildPath: src\ProfitCalcJava.xml
    ProjectBuildTarget: dist
    BuildProperties: @(version=$ReleaseNumber.$BuildNumber)
}
```

- Running maven directly:
```
Exec "ant -buidfile src\ProfitCalcJava.xml -Dversion:$ReleaseNumber.$BuildNumber dist";
```

- example plan that gets the latest source code from Git and captures a Maven artifact as a BuildMaster artifact:
```
Git::Get-Source
(
    RepositoryUrl: https://github.com/Inedo/ProfitCalcJava.git,
    Branch: master
);

Java::Build-AntProject
{
    BuildPath: src\ProfitCalcJava.xml
    ProjectBuildTarget: dist
    BuildProperties: @(version=$ReleaseNumber.$BuildNumber)
};

Create-Artifact ProfitCalcJava
(
    From: dist
);
```