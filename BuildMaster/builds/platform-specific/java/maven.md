---
title: maven
subtitle: Executing Maven Goals with BuildMaster
keywords: maven, buildmaster, goals
sequence: 100
---

You can use BuildMaster to build applications and components written in any language. That includes Java-based projects that use Maven to manage the build automation.

[Maven](http://maven.apache.org/) allows a project to build using its project object model (POM) and a set of plugins that are shared by all projects using Maven, providing a uniform build system.  Maven is a project management tool that uses a build lifecycle framework for developers. It is a build automation tool used primarily for Java projects.

To build Java-based projects, you can the Maven operation within BuildMaster's [Java extension](https://inedo.com/den/inedox/java) to execute goals and phases.

## What are Maven Phases and Goals?

A Maven _phase_ represents a stage in the Maven build lifecycle. Each Build Lifecycle is Made Up of Phases.

There are three major _built-in_ Build Life Cycles:

1. default
2. clean
2. site

The default lifecycle comprises of the following Build Phases:

```
- validate - validate the project is correct and all necessary information is available
- compile - compile the source code of the project
- test - test the compiled source code using a suitable unit testing framework. These tests should not require the code be packaged or deployed
- package - take the compiled code and package it in its distributable format, such as a JAR.
- integration-test - process and deploy the package if necessary into an environment where integration tests can be run
- verify - run any checks to verify the package is valid and meets quality criteria
- install - install the package into the local repository, for use as a dependency in other projects locally
- deploy - done in an integration or release environment, copies the final package to the remote repository for sharing with other developers and projects.
```

When you run a phase – all goals bound to this phase are executed in order. So to go through the above phases, we just have to call one command:

`mvn <phase> { Ex: mvn install }`

The above command, will start from the `validate` phase and continue until all the phases are executed sequentially until the `install` phase. Maven can either execute a goal or a phase (or even multiple goals or multiple phases) as follows:

`mvn clean install plugin:goal`

## How to Execute a Maven Goal in BuildMaster

There are two ways to excute a maven goal in BuildMaster. Either with the `Execute-Maven` operation or the `InedoCore::Exec`.  

`Excute-Maven` is included in our Java Extension. and Can be executed with this simple OtterScript.  
```
Execute-Maven
 (
     GoalsAndPhases: "<PLUGIN>:<GOAL>"
 );
```

Using the InedoCore::Exec command will require you to [install maven](http://maven.apache.org/) on the build machine. Maven is a Java based tool, therefore JDK (JDK 1.7 or above, preferably JDK 1.8) installation on your system is a pre-requisite.
```
 InedoCore::Exec
 (
     FileName: ~\FullPathToMaven\mvn,
     Arguments: <PLUGIN>:<GOAL>
 );
```

_To find out more information about running maven commands you can review [this documentation](http://maven.apache.org/run.html)_
