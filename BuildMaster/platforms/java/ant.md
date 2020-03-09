---
title: Ant
subtitle: Building Java Applications with Ant
sequence: 200
show-headings-in-nav: true
---

[Ant](https://ant.apache.org) is a Java-based build tool for Java.

Written in Java, the original author, James Duncan Davidson, said the name is an acronym for "Another Neat Tool." Ant was released on April 19th, 2000, as part of [Tomcat](java/tomcat) 3.1 and was used to build Tomcat and nothing else. Ant was then separated after it's donation to the Apache Software Foundation because it was determined it could fix many issues developers had with Makefiles, and the first official standalone release was Ant 1.1 on July 19th, 2000. It was the most popular build tool for Java until [Maven](java/maven).

Some of Ant's best qualities: 
  - extremely flexible
  - does not impose coding conventions
  - does not impose directory layouts

## Overview & Concepts {#overview data-title="Ant Overview"}

Because Ant is built on Java, it can be run on any other OS. Ant uses XML based configuration files called build files that define a target tree. 

These configuration files are made up of:

{.docs}
- Project: the master container [[ATTN: RICH - If this isn't a true CONTAINER, I'd choose a different word to avoid confusion]] of all the targets, tasks, and properties 
  - Each project defines one or more targets.
- Targets: a set of tasks you want to be executed
  - Can depend on other targets
  - When starting Ant, you select which target(s) you want executed
- Tasks: a piece of code that can be executed 
- Properties: key/value pairs used to customize or provide shortcuts to values used within targets and tasks
  - These can be set:
    - externally and passed in when calling ant
    - internally within a target or task
 
To extend Ant, users can develop their own "antlibs" containing Ant tasks and types. These are based in Java and have specific interfaces they must implement. For dependency management, use [Apache Ivy](https://ant.apache.org/ivy/).

## Apache Ivy {#ivy data-title="Apachy Ivy"}
[[ATTN: RICH - what is the connection between Ant and Ivy?]]

Apache Ivy is a tool for recording, tracking, resolving, and reporting project dependencies, and it's characterized by flexibility, configurability, and tightly [[ATTN: RICH - This sentence didn't get finished haha]]. 

Ivy uses the Maven 2 repository to resolve the dependencies you declare in an Ivy file, and its syntax is very similar to Apache Ant.

## Building with Ant {#building data-title="Building with Ant"}

First, create a build file for Ant. 

A basic build file typically includes these targets:

{.docs}
    - `init` - Creates the build directory structure used by compile 
    - `compile` - Compiles the java files in the src folder into bytecode and outputs them into the build folder
    - `dist` - Packages the build folder into a JAR, WAR, etc... and copies that into the dist folder
    - `clean` - Deletes the build and dist directories
  
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

##### Using the Default Target
```
ant -buildfile "src\ProfitCalcJava.xml" -Dversion:1.1
```

##### Specifying Targets
```
ant -buildfile "src\ProfitCalcJava.xml" -Dversion:1.1 clean dist
```

## Executing Ant with BuildMaster {#buildmaster data-title="Ant in BuildMaster"}

Ant must be installed on the build server prior to executing any of these commands. Setting ANT_HOME in the path is preferable, but you can use the AntPath variable function to override it. To do any of this, however, [[ATTN: RICH - did I just make up the content in the beginning?]] the Java extension must be installed in BuildMaster.

Use the following OtterScript in a deployment plan:

```
Java::Build-AntProject
{
    BuildPath: src\ProfitCalcJava.xml
    ProjectBuildTarget: dist
    BuildProperties: @(version=$ReleaseNumber.$BuildNumber)
}
```

- Running Maven directly:
```
Exec "ant -buidfile src\ProfitCalcJava.xml -Dversion:$ReleaseNumber.$BuildNumber dist";
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
    BuildPath: src\ProfitCalcJava.xml
    ProjectBuildTarget: dist
    BuildProperties: @(version=$ReleaseNumber.$BuildNumber)
};

Create-Artifact ProfitCalcJava
(
    From: dist
);
```
